# Unicorn Web Project

AWS CI/CDパイプラインのデモンストレーション用Javaウェブアプリケーションプロジェクトです。

## 概要

このプロジェクトは、GitHub、AWS CodeBuild、CodeDeploy、CodePipelineを使用したCI/CDパイプラインの実装例を示しています。MavenベースのJavaウェブアプリケーション（WAR）をTomcatサーバーにデプロイします。

## アーキテクチャ

- **ソース管理**: GitHub
- **ビルド**: AWS CodeBuild
- **デプロイ**: AWS CodeDeploy
- **パイプライン**: AWS CodePipeline
- **アプリケーション**: Java Maven WAR
- **ランタイム**: Apache Tomcat 9.0

## プロジェクト構成

```
├── src/main/webapp/          # Webアプリケーションソース
├── scripts/                  # デプロイメントスクリプト
│   ├── install_dependencies.sh
│   ├── start_server.sh
│   └── stop_server.sh
├── appspec.yml              # CodeDeploy設定
├── buildspec.yml            # CodeBuild設定（Maven）
├── buildspec_docker.yml     # CodeBuild設定（Docker）
├── Dockerfile               # Dockerイメージ定義
├── pipeline.json            # CodePipeline設定
├── pom.xml                  # Maven設定
└── settings.xml             # Maven設定（CodeArtifact用）
```

## CI/CDパイプライン

### 1. Source Stage
- GitHubリポジトリからソースコードを取得
- ブランチ: `main`

### 2. Build Stage
- CodeBuildでMavenビルドを実行
- Java 17（Amazon Corretto）を使用
- CodeArtifactから依存関係を取得
- WARファイルを生成

### 3. Deploy Stage
- CodeDeployでステージング環境にデプロイ
- クロスアカウントデプロイメント対応

## デプロイメント

### EC2インスタンスへのデプロイ

1. **依存関係のインストール**
   - Apache Tomcat
   - Apache HTTP Server

2. **アプリケーションのデプロイ**
   - WARファイルをTomcatのwebappsディレクトリにコピー
   - Apache HTTP Serverをリバースプロキシとして設定

3. **サービスの開始**
   - Tomcatサービスの開始・有効化
   - Apache HTTP Serverの開始・有効化

### Dockerコンテナへのデプロイ

1. **イメージのビルド**
   ```bash
   docker build -t unicorn-web-project .
   ```

2. **ECRへのプッシュ**
   - Amazon ECRにDockerイメージをプッシュ
   - 環境変数でリポジトリとタグを指定

## 前提条件

- AWS CLI設定済み
- 適切なIAMロールとポリシー
- GitHubリポジトリ
- CodeBuildプロジェクト
- CodeDeployアプリケーションとデプロイメントグループ
- EC2インスタンス（CodeDeployエージェント導入済み）

## 使用方法

1. **リポジトリのクローン**
   ```bash
   git clone <github-repository-url>
   cd unicorn-web-project
   ```

2. **ローカルビルド**
   ```bash
   mvn clean compile package
   ```

3. **パイプラインの実行**
   - mainブランチへのプッシュで自動実行
   - または手動でパイプラインを開始

## 設定

### CodeArtifact
- ドメイン: `unicorns`
- 認証トークンを使用した依存関係の取得

### アプリケーション設定
- アプリケーション名: `CrossDeploy`
- デプロイメントグループ: `CrossDeployGroup`
- サーバー名: `app.wildrydes.com`

## トラブルシューティング

- ビルドエラー: `buildspec.yml`のログを確認
- デプロイエラー: CodeDeployのログとEC2インスタンスの状態を確認
- 権限エラー: IAMロールとポリシーを確認

## ライセンス

このプロジェクトはデモンストレーション目的で作成されています。