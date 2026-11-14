# sitedia for Claude (베타)

Claude 채팅·Cowork·Claude Code에서 sitedia 대지분석 다이어그램을 주소와 반경으로 만들고 대화로 SVG를 고칩니다.

- 명령: `/sitedia 강남역 반경 500m 건축물 용도` (Claude Code는 `/sitedia`, Cowork는 `/sitedia:sitedia`, 채팅에서는 그냥 말해도 됩니다)
- 연결: 커넥터 주소는 `https://sitedia.app/mcp`입니다. 연결할 때 sitedia 로그인 창이 뜹니다(구글·이메일, 홈페이지와 같은 계정).
- 이용권: 도판은 그 나라 sitedia 이용권이 있을 때 그립니다(나라별). 이용권이 하나도 없는 계정은 처음 도판을 만들 때부터 48시간 동안 가입한 나라의 대지를 무료로 쓸 수 있습니다(계정당 한 번).
- 주고받는 곳: sitedia.app 한 곳입니다. 찾은 주소와 만든 도판은 sitedia 서버에 저장됩니다. Claude Code에서는 도판 파일(SVG·PNG)을 작업 폴더에 내려받을 때 curl을 씁니다.

## 日本語

Claude のチャット・Cowork・Claude Code で、住所と半径を伝えると sitedia の敷地分析ダイアグラムを作り、会話で SVG を修正します。

- コマンド：`/sitedia 渋谷駅 半径500m 建物用途`（Claude Code は `/sitedia`、Cowork は `/sitedia:sitedia`、チャットでは話しかけるだけでも使えます）
- 連携：コネクタのアドレスは `https://sitedia.app/mcp` です。連携するときに sitedia のログイン画面（Google・メール、サイトと同じアカウント）が開きます。
- パス：図版は、その国の sitedia パスがあるときに作れます（国ごと）。パスが1つもないアカウントは、はじめて図版を作るときから48時間、登録した国の敷地を無料で使えます（1アカウント1回）。
- 通信先：sitedia.app だけです。検索した住所と作った図版は sitedia のサーバーに保存されます。Claude Code では、図版ファイル（SVG・PNG）を作業フォルダーに保存するときに curl を使います。

© sitedia. All rights reserved.
