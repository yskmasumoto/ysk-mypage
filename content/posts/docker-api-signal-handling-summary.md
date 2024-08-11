+++
title = 'Docker環境でのAPI開発におけるシグナル処理と終了プロセス'
date = 2024-08-11T13:38:47+09:00
draft = false
+++

## 概要
AWS ECSなどのコンテナ環境でAPIを開発する際、適切なシグナル処理と正常な終了プロセスの実装は重要です。特に、`SIGTERM`シグナルの処理は、アプリケーションのグレースフルシャットダウンに不可欠です。

## 問題点
初期の実装では、以下の問題が発生していました：

1. `entrypoint.sh`スクリプト内の`trap`コマンドが、子プロセス（uvicorn）に適用されていなかった。
2. uvicornプロセスが`SIGTERM`を受信した際に、エラーログを出力していた。
3. コンテナが異常終了として解釈される可能性があった。

## スクリプトの比較

### 修正前の`entrypoint.sh`スクリプト

```bash
#!/bin/bash

trap "exit 0" SIGTERM
uvicorn --config gunicorn_conf.py main:app
```

### 修正後の`entrypoint.sh`スクリプト

```bash
#!/bin/bash

handle_sigterm() {
    echo "Received SIGTERM. Shutting down gracefully..."
    kill -TERM "$child" 2>/dev/null
}

trap handle_sigterm SIGTERM

uvicorn --config gunicorn_conf.py main:app &
child=$!

wait "$child"

exit_code=$?

echo "uvicorn process exited with code $exit_code"
exit 0
```

## 主な改善点

1. カスタムシグナルハンドラの実装
   - 修正前: 単純な`trap "exit 0" SIGTERM`
   - 修正後: `handle_sigterm`関数を定義し、`SIGTERM`受信時の動作をカスタマイズ
   - 子プロセス（uvicorn）に`SIGTERM`を伝播するよう改善

2. バックグラウンドでのuvicorn起動
   - 修正前: フォアグラウンドで直接実行
   - 修正後: `&`を使用してバックグラウンドで起動し、プロセスIDを`$child`変数に保存

3. 子プロセスの終了待機
   - 修正前: 待機処理なし
   - 修正後: `wait "$child"`コマンドを使用して、uvicornプロセスの終了を待機

4. 終了コードの処理
   - 修正前: 単純に0で終了
   - 修正後: 子プロセスの終了コードを取得し、ログに出力。その後、スクリプトは常に0で終了

## 注意点
- uvicornのログメッセージ（例：`Worker (pid:28) was sent SIGTERM!`）は依然として表示される可能性がありますが、これはuvicornの正常な動作の一部です。
- 修正後の実装により、アプリケーションはグレースフルシャットダウンを実行し、コンテナも正常に終了するはずです。

## 推奨事項
- 修正後のスクリプトを実装し、再度テストを行う。
- ログ出力を監視し、シャットダウンプロセスが期待通りに動作することを確認する。
- 必要に応じて、uvicornの設定やログレベルを調整し、より詳細な情報を取得する。

この実装により、Docker環境でのAPI開発における信頼性とロバスト性が向上し、特にコンテナのライフサイクル管理が改善されます。修正後のスクリプトは、シグナル処理をより適切に行い、子プロセスの終了を確実に管理します。
