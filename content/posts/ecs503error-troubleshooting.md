+++
title = 'ELB/ECS構成のAPIトラブルシューティング要約'
date = 2024-07-23T21:22:57+09:00
draft = false
+++

## 筆者がAIにした質問内容

1. curlでELB, ECS構成のAPIを呼び出すと503エラーが返ってくる問題の解決方法
2. ELBのポート番号の確認方法
3. Dockerfileの`CMD`部分とELBの設定の不一致に関する質問
4. ECSのタスク定義のCloudFormationに関する質問
5. uvicornのポート変更後もcurlで503エラーが発生する問題の解決方法

## 解決プロセス

1. ECSタスクの状態確認（稼働状況、エラーログ、ヘルスチェック設定）
2. Dockerfileの確認と改善提案
3. ELBの設定確認（ポート番号、リスナー設定）
4. ECSタスク定義の確認とDockerfileとの整合性チェック
5. ポートマッピングの修正（8000から80へ）
6. セキュリティグループ、ネットワーク設定の確認
7. アプリケーションログとELBアクセスログの確認
8. ロードバランサーのリスナールール優先度の確認

## 原因

1. DockerfileとECSタスク定義のポート設定の不一致（8000 vs 80）
2. ロードバランサーのリスナールール優先度設定ミス（v1とv2のルーティング）

## 再発防止策

1. Dockerfileとタスク定義の一貫性確保
2. デプロイ前の設定チェックリストの作成と使用
3. 自動テストの導入（ルーティングチェック等）
4. ブルー/グリーンデプロイメント戦略の採用
5. インフラストラクチャのコード化（IaC）
6. 定期的な設定レビューの実施
7. 問題と解決策の文書化とチーム内共有

## 関連キーワード

- ELB (Elastic Load Balancer)
- ECS (Elastic Container Service)
- Docker
- uvicorn
- CloudFormation
- ポートマッピング
- セキュリティグループ
- ヘルスチェック
- リスナールール
- ロードバランサー優先度設定
- Continuous Integration/Continuous Deployment (CI/CD)
- Infrastructure as Code (IaC)
- ブルー/グリーンデプロイメント