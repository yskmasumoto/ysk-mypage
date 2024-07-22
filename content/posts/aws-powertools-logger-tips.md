+++
title = 'AWS Lambda Powertools Logger TypeErrorの解決'
date = 2024-07-22T21:29:04+09:00
draft = false
+++

# AWS Lambda Powertools Logger TypeErrorの解決

## エラー内容
```
TypeError: aws_lambda_powertools.logging.logger.Logger._init_logger() got multiple values for keyword argument 'log_level'
```

## エラーの原因
このエラーは通常、`Logger`クラスのインスタンス化時に`log_level`パラメータが重複して指定されている場合に発生します。以下の状況で起こる可能性があります：

1. コード内で複数回`Logger`インスタンスを作成し、それぞれで`log_level`を指定している。
2. 環境変数`LOG_LEVEL`が設定されている状態で、コード内でも`log_level`を指定している。
3. デコレータ`@logger.inject_lambda_context`で`log_level`を指定し、同時に`Logger`インスタンス作成時にも指定している。

## 解決方法

### 1. `Logger`インスタンスの一元管理
- アプリケーション全体で単一の`Logger`インスタンスを使用します。
- 可能な限り、`Logger`インスタンスを作成する場所を1箇所に限定します。

```python
from aws_lambda_powertools import Logger

logger = Logger()

# 他のモジュールやクラスでこのloggerを再利用
```

### 2. 環境変数の活用
- `LOG_LEVEL`環境変数を使用してログレベルを制御します。
- コード内で`log_level`を指定する代わりに、環境変数に依存します。

```python
# 環境変数 LOG_LEVEL を設定し、コードでは指定しない
logger = Logger()
```

### 3. デコレータの適切な使用
- `@logger.inject_lambda_context`デコレータを使用する場合、デコレータ内で`log_level`を指定せず、`Logger`インスタンス化時にのみ指定します。

```python
logger = Logger(log_level="INFO")

@logger.inject_lambda_context(log_event=True)
def handler(event, context):
    logger.info("This is an info message")
```

### 4. 依存性注入パターンの採用
- `Logger`インスタンスを外部から注入できるようにクラスを設計します。

```python
class SQS:
    def __init__(self, queue_url: str, logger: Logger = None):
        self.queue_url = queue_url
        self.logger = logger or Logger()

# 使用例
logger = Logger()
sqs = SQS(queue_url="https://example.com/queue", logger=logger)
```

### 5. 動的なログレベル変更
- 特定のコンポーネントで異なるログレベルが必要な場合は、`Logger`インスタンスのメソッドを使用して動的にログレベルを変更します。

```python
logger.setLevel("DEBUG")
```

## ベストプラクティス
- プロジェクト全体で一貫したロギング設定を使用します。
- 環境変数を使用してグローバルにログレベルを設定し、必要に応じて動的に変更します。
- `Logger`インスタンスの作成を一元管理し、依存性注入を活用して他のクラスやモジュールに渡します。

これらの方法を適切に組み合わせることで、`log_level`の重複指定によるエラーを回避し、より柔軟で一貫性のあるロギング設定を実現できます。
