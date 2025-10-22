---
title: "AWS Diagram MCP Server で自然言語からAWSアーキテクチャ図を作成し、Pythonでコード管理する"
emoji: "📝"
type: "idea" # tech: 技術記事 / idea: アイデア記事
topics: ["diagram", "aws", "mcp"] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
publication_name: "fusic"
---

## はじめに

今回はAWS Diagram MCP Serverを紹介します。
https://awslabs.github.io/mcp/servers/aws-diagram-mcp-server

MCPとは、大まかにいうと、Claude CodeやCursor、Claude DesktopやらのAIツール系と連携して、いい感じにあれこれ便利なことできるヤツです。

この記事では、AWS Diagram MCPを用いてAWSアーキテクチャ図を作成します。
また、上記の公式Docにも記載の通り、内部的にはPythonライブラリのdiagramsが使用されるので、ダイアグラムをソースコード管理しましょう。

### この記事で紹介すること

* AWS Diagram MCP Serverの設定・使い方
* ダイアグラムの日本語フォント設定（WSL2 Ubuntu24.04のケース）
* 出力したダイアグラムをPythonコードで出力する

### メリット

Pythonコード化することで、次のメリットがあります。

* Gitでバージョン管理できる
* 行・記述ベースで細かいところを手動 or AIツールで調整できる

この記事においては`Draw.io` は出てきませんが、Draw.ioのXML構造をAIツールで生成する方法も、書きやすい・管理しやすいと思ってます。

## 実際にやってみた

例のMCPの記述を書いておきましょう。
公式の記述の通り、いい感じに手元の`mcp.json`等に書いてOKです。

```json
{
  "mcpServers": {
    "awslabs.aws-diagram-mcp-server": {
      "command": "uvx",
      "args": ["awslabs.aws-diagram-mcp-server"],
      "env": {
        "FASTMCP_LOG_LEVEL": "ERROR"
      },
      "autoApprove": [],
      "disabled": false
    }
  }
}
```

次に、手元のWSL2のUbutu-24.04の環境で確認した問題ですが、出力されるダイアグラムは日本語フォントにデフォルトで対応してないようなので、調整します。

```bash
# Noto CJKフォントをインストール
sudo apt-get install -y fonts-noto-cjk fonts-noto-cjk-extra

# フォントキャッシュを更新
fc-cache -fv
```

次に、自然言語でプロンプトを書きましょう。

```bash
WEB3層構造のAWSアーキテクチャを設計し、最終的にはAWSアーキテクチャ図を構成します
* APIサーバーをFargateで構築します
* シングルリージョンのマルチAZの構成とします
* 前段にALBとAWS WAFをかませて、更に何かしらのキャッシュレイヤを採用して下さい
* DBはAuroraを使い、リードレプリカを採用します
* Auroraを見に行く時・readにもキャッシュレイヤを採用を検討する
AWSインフラをまずはmarkdownで検討してください
```

今回は、AI IDEのWindsurfを使いました。

![ダイアグラムの出力](/images/aws_diagram_mcp/diagram-output-chat.png)

いい感じに出力できました！

![AWSダイアグラム](/images/aws_diagram_mcp/diagram.png)

このままだとpngしか出力しないのですが、追加で指示することでPythonコードも出力できます。

![Pythonコード](/images/aws_diagram_mcp/python-output-chat.png)

出力されたPythonコードは下記です。

```python
"""
WEB3層構造 AWSアーキテクチャ図生成スクリプト

このスクリプトは、diagramsパッケージを使用してAWSアーキテクチャ図を生成します。
"""

from diagrams import Diagram, Cluster, Edge
from diagrams.aws.general import User
from diagrams.aws.network import CloudFront, ALB, NATGateway, InternetGateway
from diagrams.aws.security import WAF, SecretsManager
from diagrams.aws.compute import Fargate
from diagrams.aws.database import Aurora, AuroraInstance
from diagrams.aws.database import ElasticacheForRedis
from diagrams.aws.management import Cloudwatch
from diagrams.aws.storage import S3

# ダイアグラム設定
graph_attr = {
    "fontsize": "14",
    "bgcolor": "white",
    "pad": "0.5",
}

with Diagram(
    "WEB3層構造 AWSアーキテクチャ",
    filename="web3tier_architecture",
    direction="LR",
    show=False,
    graph_attr=graph_attr
):
    # ユーザー
    users = User("ユーザー")
    
    # プレゼンテーション層
    with Cluster("プレゼンテーション層"):
        cloudfront = CloudFront("CloudFront\n(CDN)")
        waf = WAF("AWS WAF")
        
        with Cluster("マルチAZ - Public Subnet"):
            alb = ALB("Application\nLoad Balancer")
    
    # アプリケーション層
    with Cluster("アプリケーション層"):
        with Cluster("マルチAZ - Private Subnet (App)"):
            with Cluster("ECS Fargate Cluster"):
                fargate_tasks = [
                    Fargate("Fargate\nTask 1"),
                    Fargate("Fargate\nTask 2"),
                    Fargate("Fargate\nTask N")
                ]
        
        with Cluster("マルチAZ - Private Subnet (Cache)"):
            elasticache_app = ElasticacheForRedis("ElastiCache\n(App Cache)\nセッション・API")
    
    # データ層
    with Cluster("データ層"):
        with Cluster("マルチAZ - Private Subnet (Cache)"):
            elasticache_db = ElasticacheForRedis("ElastiCache\n(DB Cache)\nクエリ結果")
        
        with Cluster("マルチAZ - Private Subnet (DB)"):
            with Cluster("Aurora MySQL Cluster"):
                aurora_writer = Aurora("Aurora\nWriter")
                aurora_readers = [
                    AuroraInstance("Aurora\nReader 1"),
                    AuroraInstance("Aurora\nReader 2")
                ]
    
    # ネットワーク
    with Cluster("VPC (10.0.0.0/16)"):
        nat = NATGateway("NAT Gateway\n(各AZ)")
        igw = InternetGateway("Internet\nGateway")
    
    # セキュリティ・監視
    with Cluster("セキュリティ・監視"):
        secrets = SecretsManager("Secrets\nManager")
        cloudwatch = Cloudwatch("CloudWatch\nログ・メトリクス")
        s3_logs = S3("S3\nログ保存")
    
    # データフロー - 読み取り
    users >> cloudfront >> waf >> alb
    alb >> fargate_tasks[0]
    alb >> fargate_tasks[1]
    
    # アプリケーション層のキャッシュフロー
    fargate_tasks[0] >> Edge(label="1. キャッシュ確認") >> elasticache_app
    fargate_tasks[0] >> Edge(label="2. DBキャッシュ確認") >> elasticache_db
    
    # データベースアクセス
    fargate_tasks[0] >> Edge(label="3. 読み取り", color="blue") >> aurora_readers[0]
    fargate_tasks[0] >> Edge(label="書き込み", color="red") >> aurora_writer
    
    # レプリケーション
    aurora_writer >> Edge(label="レプリケーション", style="dashed") >> aurora_readers[0]
    aurora_writer >> Edge(label="レプリケーション", style="dashed") >> aurora_readers[1]
    
    # 外部接続
    fargate_tasks[0] >> Edge(label="外部API") >> nat >> igw
    
    # セキュリティ・監視
    fargate_tasks[0] >> Edge(label="秘密情報取得", style="dotted") >> secrets
    fargate_tasks[0] >> Edge(label="ログ出力", style="dotted") >> cloudwatch
    alb >> Edge(label="アクセスログ", style="dotted") >> s3_logs
    waf >> Edge(label="WAFログ", style="dotted") >> s3_logs

print("✅ ダイアグラムが生成されました: web3tier_architecture.png")
```

Pythonから、先ほどのdiagramを出力できます。

```bash
# 事前にdiagramsを入れておく（ uvxを介さずにPython実行するので
pip install diagrams

# 実行
python web3tier_architecture_diagram.py
```

Pythonコード化は、AIツールのMemory機能などで、ダイアグラム出力とセットで行うように調整してよさそうです。

このようにPythonコード化しておくと、先述の通り、バージョン管理や細かい調整ができてよいですね！


## 最後に

以上、AWS Diagram MCPとその使い方を紹介しました。

他にも、AWS公式のMCPはたくさんあり、公式ドキュメントやGitHubから確認できます。

https://awslabs.github.io/mcp/ 
https://github.com/awslabs/mcp/tree/main/src 

### たくさんあるAWS公式MCP

他のオススメとしては、下記です。

* aws-documentation-mcp-server（AWSドキュメントの参照
    * https://github.com/awslabs/mcp/tree/main/src/aws-documentation-mcp-server
* aws-knowledge-mcp-server (AWS 全般・API・CLI周りのナレッジ
    * https://github.com/awslabs/mcp/tree/main/src/aws-knowledge-mcp-server 


他に気になっているものとしては、下記です。

* 全部入りっぽい `core-mcp-server`
    * https://awslabs.github.io/mcp/servers/core-mcp-server 
* サーバレス関連の `aws-serverless-mcp-server`
    * https://github.com/awslabs/mcp/tree/main/src/aws-serverless-mcp-server 
* セキュリティべスプラ関連の `well-architected-security-mcp-server`
    * https://github.com/awslabs/mcp/tree/main/src/well-architected-security-mcp-server 
* `cdk-mcp-server`
    * https://github.com/awslabs/mcp/tree/main/src/cdk-mcp-server 

みなさんも楽しいAWS MCPライフを！
