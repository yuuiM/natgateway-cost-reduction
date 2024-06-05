# 本テンプレートについて

NAT Gatewayを定期的に削除、作成するLambda関数をデプロイするCloudFormationテンプレート。デフォルト状態では平日夜間(24:00-)、土日祝日にNAT Gatewayが存在しない状態になり、平日早朝(6:00-)に作成する。

## テンプレート適用手順

CloudFormationの画面から、「InstanceStartStop.yaml」をファイルアップロードしてデプロイする。下記パラメータを環境にあわせて指定する。
あらかじめNAT Gatewayが設定されている環境で適用し、NAT Gatewayで使用しているElastic  IP、サブネットID、ルートテーブルをパラメータで指定する。
NAT Gatewayやルートテーブルが複数ある場合は、JSONのリストを増やします。コード内にJSONサンプルがあるので、そちらを使用してください。

※Elastic　IPは解放中にわずかに料金が発生します。
https://aws.amazon.com/jp/ec2/pricing/on-demand/

| パラメータ名 | デフォルト値 | 説明 |
| ------------- | ------------- | ------------- |
| StartSchedule | 0 21 ? * SUN-THU * | NAT Gateway作成時刻をcron形式で指定。UTC時刻なので注意。デフォルトは月－金の6:00 |
| StopSchedule | 0 15 ? * MON-FRI * | NAT Gateway削除時刻をcron形式で指定。UTC時刻なので注意。デフォルトは月－金の24:00 |
| EipAllocId | eipalloc-xxxxxxxxxx | NAT Gatewayに付与するElastic IPの割り当てID (EC2から確認)|
| SubnetId | subnet-xxxxxxxxxx | NAT Gatewayを配置するサブネットID（Internet Gatewayへルーティングがあるサブネット） |
| RouteTableId | rtb-xxxxxxxxxx | Nat Gatewayへ宛先0.0.0.0/0でルーティングを設定するルートテーブルID |

## 祝日の実行について

停止起動処理（Lamnda）の中で祝日判定を行っており、祝日であれば処理がスキップされるようになっている。祝日情報は下記から取得しています。
https://holidays-jp.github.io

会社で定められた休日や、年末年始、お盆などの休日を追加したい場合は、S3に休みを記述したjsonファイルを置き、呼び出すようにするとカスタマイズ性がありそうです。
