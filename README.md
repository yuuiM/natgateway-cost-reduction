# 本テンプレートについて

NAT Gatewayを定期的に削除、作成するLambda関数をデプロイするCloudFormationテンプレートです。デフォルト状態では平日夜間、土日祝日にNAT Gatewayが存在しない状態になります。

開発環境など利用料金をおさえたい環境にご利用ください。

## テンプレート適用手順

CloudFormationの画面から、「InstanceStartStop.yaml」をファイルアップロードしてデプロイすれば使用できます。下記パラメータを指定してください。
あらかじめNAT Gatewayが設定されている環境で適用し、NAT Gatewayで使用しているElastic  IP、サブネットID、ルートテーブルをパラメータで指定する設定としています。

※Elastic　IPは解放中にわずかに料金が発生します。
https://aws.amazon.com/jp/ec2/pricing/on-demand/

| パラメータ名 | デフォルト値 | 説明 |
| ------------- | ------------- | ------------- |
| StartSchedule | 0 21 ? * SUN-THU * | NAT Gateway作成時刻をcron形式で指定。UTC時刻なので注意。デフォルトは月－金の6:00 |
| StopSchedule | 0 15 ? * MON-FRI * | NAT Gateway削除時刻をcron形式で指定。UTC時刻なので注意。デフォルトは月－金の24:00 |
| EipAllocId | eipalloc-xxxxxxxxxx | NAT Gatewayに付与するElastic IPの割り当てID|
| SubnetId | subnet-xxxxxxxxxx | NAT Gatewayを配置するサブネットID（Internet Gatewayへルーティングがあるサブネット） |
| RouteTableId | rtb-xxxxxxxxxx | Nat Gatewayへ宛先0.0.0.0/0でルーティングを設定するルートテーブルID |

## 祝日の実行について

停止起動処理（Lamnda）の中で祝日判定を行っており、祝日であれば処理がスキップされるようになっている。祝日情報は下記から取得しています。
https://holidays-jp.github.io

