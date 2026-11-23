<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/wordmark-paper.svg">
  <img alt="sitedia." src="assets/wordmark-ink.svg" height="40">
</picture>

# Claudeでsitediaを使う

Claude のチャット・Cowork・Claude Code で住所と半径を伝えると、Claude が sitedia の敷地分析ダイアグラムを作り、会話で SVG を修正します。

パスがなくても、はじめて図版を作るときから48時間、登録した国の敷地を無料で使えます（1アカウント1回）。

[English](README.md) · [한국어](README.ko.md)

## インストール

**claude.ai・Cowork**

1. 「カスタマイズ」→「プラグイン」→「追加」→「マーケットプレイスを追加」（Customize → Plugins → Add → Add marketplace）に `sitedia-app/claude-plugin` を入力します。
2. 一覧に出た sitedia を追加します。
3. プラグインの「コネクタ」（Connectors）タブで sitedia の「連携」（Connect）を押し、sitedia のアカウントでログインします。コネクタがまだない場合は、先にそのタブで追加してください。

チーム・エンタープライズプランでは、組織のオーナーが先に「組織設定」→「コネクタ」（Organization settings → Connectors）で `https://sitedia.app/mcp` をカスタムコネクタとして追加すると、メンバーが「連携」を押せるようになります。

**Claude Code**

```
claude plugin install sitedia --marketplace sitedia-app/claude-plugin
```

そのあと `/mcp` で sitedia を選んでログインします。claude.ai で追加した場合は、Claude Code にも自動で入ります。

**コネクタだけ**

`https://sitedia.app/mcp` をカスタムコネクタとして追加します。この場合 `/sitedia` コマンドはないので、話しかけて頼んでください。詳しい手順（動画つき）：https://sitedia.app/claude/ja/

## 使い方

`/sitedia 渋谷駅 半径500m 建物用途` のように頼むと図版を作ります。続けて「道路をグレーに」「凡例を入れて」のように話すと修正し、修正のたびに新しい版として残ります。

## 通信先

このプラグインが通信するのは sitedia.app だけです。連携するときに sitedia のアカウントでログインします。検索した場所と作った図版はそのアカウントで sitedia のサーバーに保存され、Claude との会話の内容は sitedia に届きません。図版は、その国の sitedia パスがあるときに作れます。Claude Code では、図版ファイル（SVG・PNG）を作業フォルダーに保存するときに curl を使います。

sitedia は Anthropic とは関係のない独立した製品です。

© sitedia. All rights reserved.
