<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/wordmark-paper.svg">
  <img alt="sitedia." src="assets/wordmark-ink.svg" height="40">
</picture>

# sitedia for Claude

Claude 채팅·Cowork·Claude Code에서 주소와 반경을 말하면 Claude가 sitedia 대지분석 다이어그램을 그려 주고 대화로 SVG도 고칩니다. 지금은 베타입니다.

## 설치

**claude.ai·Cowork**

1. Customize → Plugins → Add → Add marketplace에 `sitedia-app/claude-plugin`을 넣습니다.
2. 목록에 나온 sitedia를 추가합니다.
3. 플러그인의 Connectors 탭에서 sitedia «연결»을 누르고 sitedia 계정으로 로그인합니다. 커넥터가 아직 없으면 그 탭에서 먼저 추가합니다.

팀·엔터프라이즈 요금제는 조직 Owner가 먼저 조직 설정 → Connectors → Add → Custom에 `https://sitedia.app/mcp`를 넣어야 «연결»이 눌립니다.

**Claude Code**

```
claude plugin install sitedia --marketplace sitedia-app/claude-plugin
```

그다음 `/mcp`에서 sitedia를 골라 로그인합니다. claude.ai에서 추가했다면 Claude Code에도 저절로 들어오니 따로 설치하지 않아도 됩니다.

**커넥터만**

`https://sitedia.app/mcp`를 커스텀 커넥터로 추가합니다. 이 경우 `/sitedia` 명령은 없어서 말로 부탁해야 합니다. 설치 안내: https://sitedia.app/claude/

## 쓰는 법

`/sitedia 강남역 반경 500m 건축물 용도`처럼 부탁하면 도판을 그립니다. 이어서 «업무만 파랗게», «범례 넣어 줘»처럼 말하면 고칩니다.

## 주고받는 곳

이 플러그인은 sitedia.app 한 곳과만 통신합니다. 연결할 때 sitedia 계정으로 로그인합니다. 찾은 주소와 만든 도판은 sitedia 서버에 저장됩니다. 도판은 그 나라 sitedia 이용권이 있을 때 그립니다. Claude Code에서는 도판 파일(SVG·PNG)을 작업 폴더에 내려받을 때 curl을 씁니다.

© sitedia. All rights reserved.
