<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/wordmark-paper.svg">
  <img alt="sitedia." src="assets/wordmark-ink.svg" height="40">
</picture>

# sitedia plugin for Claude

Site-analysis diagrams in Claude chat, Cowork and Claude Code. Give Claude an address and a radius, and it draws a sitedia site-analysis diagram. Then edit the SVG by chatting.

[한국어](README.ko.md) · [日本語](README.ja.md)

## Install

**claude.ai and Cowork**

1. Go to Customize → Plugins → Add → Add marketplace and enter `sitedia-app/claude-plugin`.
2. Add sitedia from the list.
3. On the plugin's Connectors tab, click Connect next to sitedia and sign in with your sitedia account. If the connector is not there yet, add it on that tab first.

On Team and Enterprise plans, an organization Owner first adds `https://sitedia.app/mcp` as a custom connector under Organization settings → Connectors. After that, members can connect.

**Claude Code**

```
claude plugin install sitedia --marketplace sitedia-app/claude-plugin
```

Then choose sitedia in `/mcp` and sign in. If you added the plugin on claude.ai, it is already in Claude Code.

**Connector only**

Add `https://sitedia.app/mcp` as a custom connector. There is no `/sitedia` command in this case, so ask in plain words.

Installation guides: [English](https://sitedia.app/claude/en/) · [Korean](https://sitedia.app/claude/?lang=ko) · [Japanese](https://sitedia.app/claude/ja/)

## Use

Type `/sitedia`, then a place or address, a radius and a diagram type (for example building use or building height). After the diagram appears, ask for changes such as "make offices blue" or "add a legend". Each change is saved as a new version.

## Pass and free trial

Sign in with your sitedia account, the same one you use on sitedia.app. Diagrams need a sitedia pass for the country of the site. An account with no pass can use sites in its sign-up country free for 48 hours from the first diagram (once per account).

## Where data goes

The plugin talks only to sitedia.app. Places you look up and diagrams you make are stored on the sitedia server under your account. sitedia does not receive your Claude conversation. In Claude Code, the plugin uses curl to save diagram files (SVG, PNG) to your working folder.

sitedia is an independent product and is not affiliated with Anthropic.

© sitedia. All rights reserved.
