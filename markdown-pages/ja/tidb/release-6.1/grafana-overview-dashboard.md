---
title: Key Metrics
summary: Learn some key metrics displayed on the Grafana Overview dashboard.
---

# 主要な指標 {#key-metrics}

TiUPを使用してTiDBクラスタをデプロイする場合、監視システム（Prometheus＆Grafana）が同時にデプロイされます。詳細については、 [TiDBモニタリングフレームワークの概要](/tidb-monitoring-framework.md)を参照してください。

Grafanaダッシュボードは、Overview、PD、TiDB、TiKV、Node_exporter、Disk Performance、Performance_overviewなどを含む一連のサブダッシュボードに分割されています。診断に役立つ多くのメトリックがあります。

日常的な操作の場合、主要なメトリックが表示される概要ダッシュボードから、コンポーネント（PD、TiDB、TiKV）のステータスとクラスタ全体の概要を取得できます。このドキュメントでは、これらの主要な指標について詳しく説明します。

## 主な指標の説明 {#key-metrics-description}

概要ダッシュボードに表示される主要なメトリックを理解するには、次の表を確認してください。

| サービス         | パネル名                               | 説明                                                                                                                                                                                                                                                                                                                                                   | 正常範囲                                                                                          |
| ------------ | ---------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| サービスポートステータス | サービスアップ                            | 各サービスのオンラインノード番号。                                                                                                                                                                                                                                                                                                                                    |                                                                                               |
| PD           | PDの役割                              | 現在のPDの役割。                                                                                                                                                                                                                                                                                                                                            |                                                                                               |
| PD           | ストレージ容量                            | TiDBクラスタの合計ストレージ容量。                                                                                                                                                                                                                                                                                                                                  |                                                                                               |
| PD           | 現在のストレージサイズ                        | TiKVレプリカが占有するスペースを含む、TiDBクラスタの占有ストレージ容量。                                                                                                                                                                                                                                                                                                             |                                                                                               |
| PD           | 通常の店舗                              | 通常状態のノードの数。                                                                                                                                                                                                                                                                                                                                          |                                                                                               |
| PD           | 異常な店                               | 異常状態にあるノードの数。                                                                                                                                                                                                                                                                                                                                        | 0                                                                                             |
| PD           | 地域の数                               | 現在のクラスタのリージョンの総数。リージョンの数はレプリカの数とは関係がないことに注意してください。                                                                                                                                                                                                                                                                                                   |                                                                                               |
| PD           | 99％completed_cmds_duration_seconds | pd-serverリクエストを完了するための99パーセンタイル期間。                                                                                                                                                                                                                                                                                                                   | 5ms未満                                                                                         |
| PD           | Handle_requests_duration_seconds   | PD要求のネットワーク期間。                                                                                                                                                                                                                                                                                                                                       |                                                                                               |
| PD           | 地域の健康                              | 各地域の状態。                                                                                                                                                                                                                                                                                                                                              | 一般に、保留中のピアの数は100未満であり、欠落しているピアの数は常に`0`より大きくなるとは限りません。                                         |
| PD           | ホットライトリージョンのリーダーディストリビューション        | 各TiKVインスタンスの書き込みホットスポットであるリーダーの総数。                                                                                                                                                                                                                                                                                                                   |                                                                                               |
| PD           | ホットリードリージョンのリーダーディストリビューション        | 各TiKVインスタンスの読み取りホットスポットであるリーダーの総数。                                                                                                                                                                                                                                                                                                                   |                                                                                               |
| PD           | リージョンハートビートレポート                    | インスタンスごとにPDに報告されたハートビートの数。                                                                                                                                                                                                                                                                                                                           |                                                                                               |
| PD           | 99％のリージョンハートビートレイテンシ               | TiKVインスタンスごとのハートビート遅延（P99）。                                                                                                                                                                                                                                                                                                                          |                                                                                               |
| TiDB         | ステートメントOPS                         | `INSERT`秒あたりに実行されるさまざまなタイプのSQLステートメントの数。これは、 `SELECT` 、およびその他のタイプのステートメントに従ってカウントされ`UPDATE` 。                                                                                                                                                                                                                                                        |                                                                                               |
| TiDB         | 間隔                                 | 実行時間。<br/> 1.クライアントのネットワーク要求がTiDBに送信されてから、TiDBが要求を実行した後に要求がクライアントに返されるまでの時間。一般に、クライアント要求はSQLステートメントの形式で送信されます。ただし、この期間には、 `COM_PING`などの`COM_STMT_FETCH`の実行時間を`COM_SEND_LONG_DATA`ことができ`COM_SLEEP` 。<br/> 2. TiDBはマルチクエリをサポートしているため、TiDBは`select 1; select 1; select 1;`などの複数のSQLステートメントの送信を一度にサポートします。この場合、このクエリの合計実行時間には、すべてのSQLステートメントの実行時間が含まれます。 |                                                                                               |
| TiDB         | インスタンス別のCPS                        | インスタンス別のCPS：各TiDBインスタンスのコマンド統計。コマンド実行結果の成功または失敗に応じて分類されます。                                                                                                                                                                                                                                                                                           |                                                                                               |
| TiDB         | 失敗したクエリOPM                         | 各TiDBインスタンスで1秒あたりのSQLステートメントを実行するときに発生したエラーに基づくエラータイプ（構文エラーや主キーの競合など）の統計。エラーが発生したモジュールとエラーコードが含まれています。                                                                                                                                                                                                                                               |                                                                                               |
| TiDB         | 接続数                                | 各TiDBインスタンスの接続番号。                                                                                                                                                                                                                                                                                                                                    |                                                                                               |
| TiDB         | メモリ使用量                             | 各TiDBインスタンスのメモリ使用統計。これは、プロセスによって占有されているメモリと、ヒープ上でGolangによって適用されているメモリに分割されます。                                                                                                                                                                                                                                                                        |                                                                                               |
| TiDB         | トランザクションOPS                        | 1秒あたりに実行されたトランザクションの数。                                                                                                                                                                                                                                                                                                                               |                                                                                               |
| TiDB         | トランザクション期間                         | トランザクションの実行時間                                                                                                                                                                                                                                                                                                                                        |                                                                                               |
| TiDB         | KV Cmd OPS                         | 実行されたKVコマンドの数。                                                                                                                                                                                                                                                                                                                                       |                                                                                               |
| TiDB         | KVCmd期間99                          | KVコマンドの実行時間。                                                                                                                                                                                                                                                                                                                                         |                                                                                               |
| TiDB         | PD TSO OPS                         | TiDBが1秒あたりPDから取得するTSOの数。                                                                                                                                                                                                                                                                                                                             |                                                                                               |
| TiDB         | PDTSO待機時間                          | TiDBがPDがTSOを返すのを待つ期間。                                                                                                                                                                                                                                                                                                                                |                                                                                               |
| TiDB         | TiClientリージョンエラーOPS                | TiKVによって返されたリージョン関連のエラーの数。                                                                                                                                                                                                                                                                                                                           |                                                                                               |
| TiDB         | ロック解決OPS                           | ロックを解決するTiDB操作の数。 TiDBの読み取りまたは書き込み要求がロックに遭遇すると、ロックを解決しようとします。                                                                                                                                                                                                                                                                                        |                                                                                               |
| TiDB         | KVバックオフOPS                         | TiKVによって返されたエラーの数。                                                                                                                                                                                                                                                                                                                                   |                                                                                               |
| TiKV         | 盟主                                 | 各TiKVノードのリーダーの数。                                                                                                                                                                                                                                                                                                                                     |                                                                                               |
| TiKV         | 領域                                 | 各TiKVノードのリージョンの数。                                                                                                                                                                                                                                                                                                                                    |                                                                                               |
| TiKV         | CPU                                | 各TiKVノードのCPU使用率。                                                                                                                                                                                                                                                                                                                                     |                                                                                               |
| TiKV         | メモリー                               | 各TiKVノードのメモリ使用量。                                                                                                                                                                                                                                                                                                                                     |                                                                                               |
| TiKV         | 店舗サイズ                              | 各TiKVインスタンスによって使用されるストレージスペースのサイズ。                                                                                                                                                                                                                                                                                                                   |                                                                                               |
| TiKV         | cfサイズ                              | 各列ファミリーのサイズ（略してCF）。                                                                                                                                                                                                                                                                                                                                  |                                                                                               |
| TiKV         | チャンネルがいっぱい                         | 各TiKVインスタンスでの「チャネルフル」エラーの数。                                                                                                                                                                                                                                                                                                                          | 0                                                                                             |
| TiKV         | サーバーレポートの障害                        | 各TiKVインスタンスによって報告されたエラーメッセージの数。                                                                                                                                                                                                                                                                                                                      | 0                                                                                             |
| TiKV         | スケジューラ保留中のコマンド                     | 各TiKVインスタンスで保留中のコマンドの数。                                                                                                                                                                                                                                                                                                                              |                                                                                               |
| TiKV         | コプロセッサーエグゼキューターカウント                | 1秒あたりにTiKVが受信したコプロセッサー操作の数。各タイプのコプロセッサーは個別にカウントされます。                                                                                                                                                                                                                                                                                                 |                                                                                               |
| TiKV         | コプロセッサー要求期間                        | コプロセッサーの読み取り要求の処理にかかる時間。                                                                                                                                                                                                                                                                                                                             |                                                                                               |
| TiKV         | ラフトストアCPU                          | raftstoreスレッドのCPU使用率                                                                                                                                                                                                                                                                                                                                 | デフォルトのスレッド数は2（ `raftstore.store-pool-size`で構成）です。シングルスレッドの値が80％を超える場合は、CPU使用率が非常に高いことを示しています。 |
| TiKV         | コプロセッサーCPU                         | コプロセッサースレッドのCPU使用率。                                                                                                                                                                                                                                                                                                                                  |                                                                                               |
| システム情報       | Vcores                             | CPUコアの数。                                                                                                                                                                                                                                                                                                                                             |                                                                                               |
| システム情報       | メモリー                               | 総メモリ。                                                                                                                                                                                                                                                                                                                                                |                                                                                               |
| システム情報       | CPU使用率                             | CPU使用率、最大100％。                                                                                                                                                                                                                                                                                                                                       |                                                                                               |
| システム情報       | 負荷[1m]                             | 1分以内の過負荷。                                                                                                                                                                                                                                                                                                                                            |                                                                                               |
| システム情報       | 利用可能なメモリ                           | 使用可能なメモリのサイズ。                                                                                                                                                                                                                                                                                                                                        |                                                                                               |
| システム情報       | ネットワークトラフィック                       | ネットワークトラフィックの統計。                                                                                                                                                                                                                                                                                                                                     |                                                                                               |
| システム情報       | TCPリトランス                           | TOC再送信の頻度。                                                                                                                                                                                                                                                                                                                                           |                                                                                               |
| システム情報       | IO Util                            | ディスク使用率、最大100％。通常、使用率が最大80％〜90％の場合は、新しいノードの追加を検討する必要があります。                                                                                                                                                                                                                                                                                           |                                                                                               |

## 概要ダッシュボードのインターフェース {#interface-of-the-overview-dashboard}

![overview](https://download.pingcap.com/images/docs/grafana-monitor-overview.png)