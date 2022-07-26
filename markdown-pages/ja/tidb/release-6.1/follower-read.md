---
title: Follower Read
summary: This document describes the use and implementation of Follower Read.
---

# フォロワー読み取り {#follower-read}

読み取りホットスポットがリージョンに表示されると、リージョンリーダーがシステム全体の読み取りボトルネックになる可能性があります。この状況で、フォロワー読み取り機能を有効にすると、リーダーの負荷が大幅に軽減され、複数のフォロワー間で負荷が分散されるため、システム全体のスループットが向上します。このドキュメントでは、フォロワー読み取りの使用と実装のメカニズムを紹介します。

## 概要 {#overview}

フォロワー読み取り機能とは、リージョンのフォロワーレプリカを使用して、強一貫性のある読み取りを前提として読み取り要求を処理することを指します。この機能により、TiDBクラスタのスループットが向上し、リーダーの負荷が軽減されます。これには、リージョン内のリーダーレプリカからフォロワーレプリカにTiKV読み取り負荷をオフロードする一連の負荷分散メカニズムが含まれています。 TiKVのFollowerRead実装は、ユーザーに強力な一貫性のある読み取りを提供します。

> **ノート：**
>
> 強一貫性のある読み取りを実現するには、現在、フォロワーノードがリーダーノード（つまり`ReadIndex` ）に現在の実行の進行状況を要求する必要があります。これにより、追加のネットワーク要求のオーバーヘッドが発生します。したがって、フォロワー読み取りの主な利点は、クラスタの書き込み要求から読み取り要求を分離し、全体的な読み取りスループットを向上させることです。

## 使用法 {#usage}

TiDBのフォロワー読み取り機能を有効にするには、 `tidb_replica_read`変数の値を`follower`または`leader-and-follower`に変更します。


```sql
set [session | global] tidb_replica_read = '<target value>';
```

スコープ：セッション|グローバル

デフォルト：リーダー

この変数は、予想されるデータ読み取りモードを設定するために使用されます。

-   `tidb_replica_read`の値が`leader`または空の文字列に設定されている場合、TiDBは元の動作を維持し、すべての読み取り操作をリーダーレプリカに送信して実行します。
-   `tidb_replica_read`の値が`follower`に設定されている場合、TiDBはリージョンのフォロワーレプリカを選択して、すべての読み取り操作を実行します。
-   `tidb_replica_read`の値が`leader-and-follower`に設定されている場合、TiDBは任意のレプリカを選択して読み取り操作を実行できます。

## 実装メカニズム {#implementation-mechanism}

フォロワー読み取り機能が導入される前は、TiDBは強力なリーダーの原則を適用し、すべての読み取りおよび書き込み要求をリージョンのリーダーノードに送信して処理していました。 TiKVは複数の物理ノードにリージョンを均等に分散できますが、リージョンごとに、リーダーのみが外部サービスを提供できます。他のフォロワーは、読み取り要求を処理するために何もできませんが、リーダーから複製されたデータを常に受信し、フェイルオーバーの場合にリーダーを選出するための投票の準備をします。

線形化可能性に違反したり、TiDBのスナップショットアイソレーションに影響を与えたりせずにフォロワーノードでデータを読み取れるようにするには、フォロワーノードでRaftプロトコルの`ReadIndex`を使用して、読み取り要求がリーダーでコミットされた最新のデータを読み取れるようにする必要があります。 TiDBレベルでは、フォロワー読み取り機能は、負荷分散ポリシーに基づいて、リージョンの読み取り要求をフォロワーレプリカに送信するだけで済みます。

### 強一貫性のある読み取り {#strongly-consistent-reads}

フォロワーノードが読み取り要求を処理するとき、最初にRaftプロトコルの`ReadIndex`つを使用してリージョンのリーダーと対話し、現在のRaftグループの最新のコミットインデックスを取得します。リーダーの最新のコミットインデックスがフォロワーにローカルに適用された後、読み取り要求の処理が開始されます。

### フォロワーレプリカ選択戦略 {#follower-replica-selection-strategy}

フォロワー読み取り機能はTiDBのスナップショット分離トランザクション分離レベルに影響を与えないため、TiDBはラウンドロビン戦略を採用してフォロワーレプリカを選択します。現在、コプロセッサー要求の場合、フォロワー読み取り負荷分散ポリシーの粒度は接続レベルにあります。特定のリージョンに接続されているTiDBクライアントの場合、選択されたフォロワーは固定されており、障害が発生した場合、またはスケジューリングポリシーが調整された場合にのみ切り替えられます。

ただし、ポイントクエリなどの非コプロセッサ要求の場合、フォロワー読み取り負荷分散ポリシーの粒度はトランザクションレベルにあります。特定のリージョンでのTiDBトランザクションの場合、選択されたフォロワーは固定され、失敗した場合、またはスケジューリングポリシーが調整された場合にのみ切り替えられます。