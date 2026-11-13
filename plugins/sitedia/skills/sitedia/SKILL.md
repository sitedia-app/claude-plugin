---
name: sitedia
description: sitedia 대지분석 다이어그램을 만들고 고친다. 사용자가 /sitedia를 치거나, 어떤 장소의 대지분석 도판(건축물 용도·준공 연대·높이·건물 피규어 그라운드·도로·녹지·용도지역·필지·지형·경사·일조·개방감·가시권 등)을 만들어 달라거나, 만든 도판의 색·레이어·범례·제목·글자를 바꿔 달라고 할 때 쓴다. Site-analysis diagrams (figure-ground, building use/age/height, roads, green, zoning, sun hours) as editable SVG via the sitedia connector.
argument-hint: "[주소·장소] [반경] [도판 종류]"
allowed-tools: Bash(curl:*), Bash(mkdir:*), mcp__ccd_view__show_pane
---

# sitedia — 대지분석 다이어그램

sitedia 커넥터 도구로 도판(SVG)을 만들고, 사용자와 대화하면서 고친다. 요청: $ARGUMENTS

## 1. 연결 확인
- sitedia 도구(`find_site`, `create_diagram` …)가 안 보이면 연결부터 안내한다. 커넥터 주소는 `https://sitedia.app/mcp`이고, 연결할 때 sitedia 로그인 창(구글·이메일, 홈페이지와 같은 계정)이 뜬다.
  - Claude Code: `/mcp` → sitedia → Authenticate.
  - claude.ai·Cowork: 플러그인의 **Connectors** 탭에서 sitedia «연결». 팀·엔터프라이즈 요금제에서 «연결»이 눌리지 않으면 Owner가 먼저 **조직 설정 → Connectors → 추가 → Custom**에 `https://sitedia.app/mcp`를 넣어야 한다(플러그인을 올려도 커넥터는 조직에 저절로 들어가지 않는다).
- 도구가 «unauthorized»·401로 실패하면 연결이 풀린 것이다(sitedia 계정 탈퇴·연결 해제 등). 같은 방법으로 다시 연결하라고 안내한다.

## 2. 만들기
1. `find_site`로 대지를 찾는다. `query`에는 주소·장소 이름만 넣고(반경은 빼고), 그 장소가 어느 나라인지 알면 `country`(kr·us·jp·gb·es·fr·nl·de…)를 꼭 같이 넣는다 — sitedia가 안 하는 나라여도 넣는다.
   - 후보가 여럿이면 가장 맞는 것을 고르고, 애매하면 주소 두세 개를 보여 주고 묻는다.
   - 결과에 `available: false`가 있으면 그 `message`를 그대로 전하고 멈춘다: 그 나라 이용권이 없으면 «홈페이지에서 이용권을 구매해 주세요» + 구매 링크, 지원하지 않는 지역이면 그렇다고. 다른 나라 대지로 바꿔 그리거나 우회하지 않는다.
   - 결과에 `also`가 있으면(같은 이름의 해외 장소) 그 `message`도 한 줄로 알려 준다.
2. 도판 종류가 분명하면 바로 `create_diagram`(diagram_type에 «건축물 용도»처럼 우리말을 넣어도 된다). 반경을 말했으면 `radius_m`에 미터로 넣는다(«1km» → 1000, «0.5마일» → 805, 150~1600). 말하지 않았으면 비워 둔다(기본 800 m, 미국 805 m). 애매하면 `list_diagram_types`로 보고 고른다. 여러 장을 원하면 하나씩 만든다.
   - 처음 보는 장소는 그리는 데 10~60초, 일조·개방감 같은 무거운 도판은 더 걸린다. «오래 걸린다»는 오류가 오면 1~2분 뒤 같은 요청을 한 번 다시 한다.
3. `create_diagram`은 도판(원판)만 낸다 — 제목·범례·출처 줄 없음. 사용자가 말하지 않았으면 이어서 `edit_diagram`으로 제목·범례·출처를 넣지 않는다. 같은 요청에서 «제목·범례도 넣어서»처럼 말했으면 `annotate: true`로 한 번에 만든다(판·위젯이 두 개 생기지 않게).
4. 도판이 바로 보이게 한다.
   - 채팅·Desktop 채팅·Cowork: `create_diagram`·`edit_diagram` 뒤에 대화창 안에 도판 보기 창(그림 + «SVG 내려받기»·«크게 보기»)이 뜬다. 그림을 다시 설명하거나 링크를 강조하지 않는다.
   - Claude 데스크톱 **Code 탭**(`mcp__ccd_view__show_pane` 도구가 있는 곳): 이 보기 창이 도구 호출과 함께 접혀서 안 보인다. 그래서 `create_diagram`·`edit_diagram` 뒤마다 바로 ① `get_diagram_file`(`format: "both"`)의 `save_command`를 실행해 작업 폴더에 SVG·PNG를 저장하고 ② `mcp__ccd_view__show_pane`(`pane: "file"`, `path`: 결과의 `show_file` = PNG 파일)으로 채팅 옆 파일 창에 도판을 띄우고 ③ 답 맨 앞에 그 PNG를 마크다운 그림으로 한 줄 넣는다: `![잠실역 반경 500 m · 건축물 높이](sitedia_잠실역_height_v1.png)` — 앱이 작업 폴더 그림을 답 안에 바로 그린다. 브라우저 창 `navigate`는 쓰지 않는다(뒤에서만 열려 사용자에게 안 보인다).
   - 터미널 Claude Code: 보기 창도 브라우저 창도 없다. view_url을 주고 4장 «파일»대로 저장한다.
5. 답은 짧게: 무슨 도판인지 한 줄, 눈에 띄는 분포 한두 줄, view_url 한 줄. 미리보기 그림을 보고 이상한 점이 있으면 같이 말한다.

## 3. 대화로 고치기
- 사용자의 말을 `edit_diagram`의 `edits`로 옮긴다. 한 번 요청에 여러 수정이면 한 번에 묶어 보낸다.
  - «업무만 파랗게» → `{op:"style", target:"업무", fill:"#2563eb"}`
  - «미상 빼» → `{op:"hide", target:"미상"}` · 다시 보이기 `show`
  - «이 색을 저 색으로» → `{op:"recolor", from:"#…", to:"#…"}`
  - «범례 넣어» → `{op:"legend", position:"right"}` · «제목» → `{op:"title", text:"…", subtitle:"…"}` · «출처 넣어» → `{op:"credit"}`
  - «선 굵게» → `{op:"stroke_scale", factor:1.5}` · «배경 흰색» → `{op:"background", circle:"#ffffff"}`
  - 글자 → `{op:"text", text:"명동역", x:120, y:260}` · 화살표·축척 막대·도형 → `{op:"svg", markup:"<g>…</g>"}`
  - «아까 걸로» → `{op:"revert", version:n}` (혼자 보낸다)
- target 이름은 결과의 `layers` 목록에서 그대로 가져온다. 모르겠으면 `describe_diagram`.
- 좌표: 대지 중심이 (0,0), 1 = 1 m, x는 동쪽, y는 남쪽(SVG). 원 반지름 = 반경(결과의 radius_m). 북쪽 위는 y가 음수.
- 고친 뒤엔 무엇을 바꿨는지 한두 줄 + 새 view_url. 미리보기를 보고 의도와 다르면 바로 한 번 더 고친다.
- sitedia 도판의 기본 문법: 원 안에는 글자·이름표·화살표를 두지 않고, 제목·범례·출처는 넣어 달라고 할 때 원 밖에 둔다. 사용자가 원 안에 넣어 달라면 그대로 해 준다. 제목·범례 문구·위치를 바꿔 달라면 `title`·`legend`를 다시 보내면 바뀐다(겹쳐 쌓이지 않는다).

## 3-1. 합치기·자유 편집·실시간 링크
- 여러 장 합치기 = `combine_diagrams`(결과는 새 도판, 1판).
  - «겹쳐 줘»(같은 대지의 레이어를 한 장에) → `mode: "overlay"`. 먼저 넣은 판이 맨 아래 — 녹지·용도지역 같은 면 도판을 앞에, 건물·도로를 뒤에. 위에 올리는 판의 바탕 원·회색 건물은 저절로 빠진다. 다른 대지끼리는 안 된다.
  - «나란히 놓아 줘»·«비교»(대지가 달라도 됨) → `mode: "grid"`, 필요하면 `columns`·`captions`·`title`. 칸 이름(캡션)으로 칸을 옮기거나(`transform`) 고칠 수 있다.
- 선·색·글꼴을 더 자세히 → `edit_diagram`의 `attr`(선 끝·이음 모양, 채움 규칙, 글꼴 등)·`transform`(옮기기·돌리기·키우기)을 쓴다.
- 도구로 안 되는 편집(경로 모양을 직접 고치기 등)은 셸이 있을 때만: 저장한 SVG 파일을 직접 고친 뒤 `get_diagram_file` 결과의 `upload_command`를 실행해 다음 판으로 올린다(링크에 바로 반영). 뿌리 `viewBox`는 지킨다.
- 실시간 보기 → 결과의 `live_url`. 사용자의 모든 도판에 같은 주소이고, 가장 최근에 만들거나 고치거나 합친 도판을 3초 안에 따라 보여 준다. 사용자가 «보면서 작업»하고 싶어 하면 한 번만 알려 준다(브라우저에 열어 두면 된다).

## 4. 파일
- Code 탭: 위 2장 4번대로 SVG·PNG를 저장하고, PNG를 파일 창에 띄우고, 답 맨 앞에 PNG 그림을 넣는다. 답에는 SVG 파일도 마크다운 링크로 적는다(예: `[sitedia_잠실역_height_v1.svg](sitedia_잠실역_height_v1.svg)`).
- 터미널 Claude Code·Cowork(셸이 있을 때): 만들거나 고칠 때마다 `get_diagram_file`로 받은 `save_command`를 실행해 작업 폴더에 SVG를 저장하고 경로를 알려 준다(같은 도판은 새 판 파일로).
- 저장한 SVG를 사용자가 직접 고쳐 달라고 하면 파일을 고쳐도 되지만, 가능하면 `edit_diagram`으로 고쳐 서버 판 기록과 맞춘다.
- 채팅: `get_diagram_file`의 링크(SVG·PNG)를 준다. SVG는 일러스트레이터·피그마에서 레이어 이름 그대로 열린다.

## 5. 안 될 때
- 결과에 `available: false`가 오면 `message`와 구매 링크(`buy_url`)를 그대로 전하고 멈춘다. 이미 만든 도판도 그 나라 이용권이 끝나면 고치기·합치기·파일 받기에서 같은 안내가 나온다.
- 도구 오류 메시지에 고칠 방법(있는 레이어 이름 등)이 같이 오면 그걸 보고 한 번 다시 시도한다.
