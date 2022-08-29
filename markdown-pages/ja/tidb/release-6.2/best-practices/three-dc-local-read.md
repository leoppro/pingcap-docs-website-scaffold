---
title: Local Read under Three Data Centers Deployment
summary: Learn how to use the Stale Read feature to read local data under three DCs deployment and thus reduce cross-center requests.
---

# 3 つのデータ センター展開でのローカル読み取り {#local-read-under-three-data-centers-deployment}

3 つのデータ センターのモデルでは、リージョンには、各データ センターで分離された 3 つのレプリカがあります。ただし、強力な一貫性のある読み取りの要件により、TiDB はすべてのクエリで対応するデータのリーダー レプリカにアクセスする必要があります。リーダー レプリカとは異なるデータ センターでクエリが生成された場合、TiDB は別のデータ センターからデータを読み取る必要があるため、アクセス レイテンシが増加します。

このドキュメントでは、 [古い読み取り](/stale-read.md)の機能を使用してクロスセンター アクセスを回避し、リアルタイム データの可用性を犠牲にしてアクセスの待ち時間を短縮する方法について説明します。

## 3 つのデータ センターの TiDBクラスタをデプロイする {#deploy-a-tidb-cluster-of-three-data-centers}

3 データセンターの配置方法については、 [1 つの地域展開における複数のデータセンター](/multi-data-centers-in-one-city-deployment.md)を参照してください。

TiKV ノードと TiDB ノードの両方に構成項目`labels`が構成されている場合、同じデータ センター内の TiKV ノードと TiDB ノードの`zone`ラベルの値は同じでなければならないことに注意してください。たとえば、TiKV ノードと TiDB ノードの両方がデータセンターにある場合`dc-1` 、次のラベルを使用して 2 つのノードを構成する必要があります。

```
[labels]
zone=dc-1
```

## Stale Read を使用してローカル読み取りを実行する {#perform-local-read-using-stale-read}

[古い読み取り](/stale-read.md)は、ユーザーが履歴データを読み取るために TiDB が提供するメカニズムです。このメカニズムを使用すると、特定の時点または指定された時間範囲内の対応する履歴データを読み取ることができるため、ストレージ ノード間のデータ レプリケーションによって生じるレイテンシを節約できます。地理的に分散した展開のいくつかのシナリオで古い読み取りを使用する場合、TiDB は現在のデータ センターのレプリカにアクセスして、リアルタイム パフォーマンスをいくらか犠牲にして対応するデータを読み取ります。クエリ プロセス全体のアクセス レイテンシ。

TiDB が Stale Read クエリを受信すると、その TiDB ノードの`zone`ラベルが構成されている場合、TiDB は、対応するデータ レプリカが存在する同じ`zone`ラベルを持つ TiKV ノードに要求を送信します。

Stale Read を実行する方法については、 [`AS OF TIMESTAMP`句を使用して古い読み取りを実行する](/as-of-timestamp.md)を参照してください。