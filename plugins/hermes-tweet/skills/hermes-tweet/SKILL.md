---
name: hermes-tweet
description: Use this skill when the user asks to install, configure, or troubleshoot Hermes Tweet, the Hermes Agent X/Twitter plugin, Hermes Agent Twitter automation, or Hermes Agent social monitoring.
argument-hint: <install|configure|use|troubleshoot>
---

# Hermes Tweet

Hermes Tweet は Hermes Agent 用の X/Twitter プラグインです。X/Twitter の検索、読み取り、監視、投稿系ワークフローを Hermes Agent から扱うときに使います。

## 公式リンク

- GitHub: <https://github.com/Xquik-dev/hermes-tweet>
- PyPI: <https://pypi.org/project/hermes-tweet/>

## インストール

推奨インストール:

```bash
hermes plugins install Xquik-dev/hermes-tweet --enable
```

PyPI から Hermes Agent の仮想環境へ入れる場合:

```bash
~/.hermes/hermes-agent/venv/bin/pip install hermes-tweet
hermes plugins enable hermes-tweet
```

Windows では仮想環境内の `Scripts\pip.exe` を使います。

インストール後は次で有効化状態を確認します:

```bash
hermes plugins list
hermes tools list
```

## 設定

読み取りには `XQUIK_API_KEY` が必要です。対話式インストールでは Hermes が `~/.hermes/.env` への保存を促します。非対話式インストールでは、Hermes を起動する環境の環境変数、または `~/.hermes/.env` に設定します。

```bash
export XQUIK_API_KEY="<your-xquik-api-key>"
```

投稿、DM、フォロー、Webhook、メディア変更などのアクション系ツールは既定で無効です。必要なセッションだけで明示的に有効化します。

```bash
export HERMES_TWEET_ENABLE_ACTIONS="true"
```

監視、調査、読み取り中心の運用では `HERMES_TWEET_ENABLE_ACTIONS=false` のまま使います。

## 使い方

1. `tweet_explore` で利用できるカタログ済みエンドポイントを探します。API キーなしでも使えます。
2. `tweet_read` で読み取り専用エンドポイントを呼び出します。
3. `tweet_action` はアクションを明示的に有効化したセッションだけで使います。

例:

```bash
hermes -z "Use tweet_explore, then read /api/v1/account. Do not call tweet_action." --toolsets hermes-tweet
```

## 注意事項

- `XQUIK_API_KEY` がない場合、Hermes Tweet は安全な `tweet_explore` だけを公開します。
- `tweet_action` は `HERMES_TWEET_ENABLE_ACTIONS=true` のときだけ使います。
- Hermes Desktop のリモートゲートウェイプロファイルを使う場合、プラグインを実行するホスト側に環境変数を設定します。
- 詳細な導入、検証、トラブルシュート手順は公式 README を参照します。
