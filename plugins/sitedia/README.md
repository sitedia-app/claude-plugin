# sitedia for Claude (베타)

Claude 채팅·Cowork·Claude Code에서 sitedia 대지분석 다이어그램을 주소와 반경으로 만들고 대화로 SVG를 고쳐요.

- 명령: `/sitedia 강남역 반경 500m 건축물 용도` (Claude Code는 `/sitedia`, Cowork는 `/sitedia:sitedia`, 채팅에선 그냥 말해도 돼요)
- 연결: 커넥터 주소는 `https://sitedia.app/mcp`예요. 연결할 때 sitedia 로그인 창이 떠요(구글·이메일, 홈페이지와 같은 계정).
- 이용권: 도판은 그 나라 sitedia 이용권이 있을 때 그려요(나라별). 없으면 홈페이지 구매 안내가 나와요.
- 주고받는 곳: sitedia.app 한 곳이에요. 찾은 주소와 만든 도판은 sitedia 서버에 저장돼요. Claude Code에서는 도판 파일(SVG·PNG)을 작업 폴더에 내려받을 때 curl을 써요.

© sitedia. All rights reserved.
