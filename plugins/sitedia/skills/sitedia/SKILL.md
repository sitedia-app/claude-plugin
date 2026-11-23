---
name: sitedia
description: "Make and edit site-analysis diagrams for an address and radius: building use, height, roads, zoning and more."
argument-hint: "[place or address] [radius] [diagram type]"
allowed-tools: Bash(curl:*), Bash(mkdir:*), mcp__ccd_view__show_pane
---

# sitedia — site-analysis diagrams

Use the sitedia connector to make SVG site-analysis diagrams and edit them with the user. Request: $ARGUMENTS

**Reply in the language of the user's current request.** Tool messages, layer names and site data may be in another language. Translate user-facing explanations, but pass the tool's identifiers and layer names back unchanged.

## 1. Connect

- If sitedia tools such as `find_site` and `create_diagram` are missing, explain how to connect. The connector URL is https://sitedia.app/mcp. Sign in to sitedia with the same Google or email account used on the website.
  - Claude Code: open `/mcp`, select sitedia, then Authenticate.
  - Claude chat and Cowork: open the plugin's **Connectors** tab and click Connect next to sitedia. If members on a Team or Enterprise plan cannot connect, an organization Owner must first add the URL as a custom connector under **Organization settings → Connectors**. Adding the plugin does not add the connector to the organization.
  - Plugins and connectors are under **Plugins** in the left menu (Add → Add marketplace or Add custom connector). Use the menu names that match the user's interface. Korean: 플러그인, 추가, 마켓플레이스 추가, 커스텀 커넥터 추가, 연결. Japanese: プラグイン, 追加, マーケットプレイスを追加, カスタムコネクタを追加, 連携.
  - Installation guides: [English](https://sitedia.app/claude/en/), [Korean](https://sitedia.app/claude/?lang=ko), [Japanese](https://sitedia.app/claude/ja/), [Spanish](https://sitedia.app/claude/es/), [French](https://sitedia.app/claude/fr/), [Dutch](https://sitedia.app/claude/nl/), [German](https://sitedia.app/claude/de/).
- If a tool returns `unauthorized` or HTTP 401, ask the user to reconnect the same way.

## 2. Create a diagram

1. Call `find_site`. Put only the address or place name in `query`, not the radius. If you know the country, supply `country` even if sitedia may not cover it (for example `kr`, `us`, `jp`, `gb`, `es`, `fr`, `nl`, `de`).
   - Pick the best match. If it is ambiguous, show two or three candidate addresses and ask the user to choose.
   - If the result says `available: false`, relay its `message` in the user's language, keeping links, dates and numbers unchanged. If it includes a pass purchase link, pass it on. Stop rather than drawing a site in another country or trying another route around the restriction.
   - If the result includes `trial_notice`, relay it once in one line. For an account without a pass, the 48-hour trial covers the country where the account was created.
   - If the result includes `also` (a place with the same name in another country), mention its `message` in one line.
2. Call `create_diagram` when the requested diagram type is clear. `diagram_type` also accepts names such as “건축물 용도” and “建物用途”. If unsure, use `list_diagram_types`. For multiple diagrams, make them one at a time.
   - Set `lang` to the language of the user's request for diagram titles, labels and viewer text: `ko`, `ja`, `en`, `es`, `fr`, `nl` or `de`. For any other language, use `en` and still reply in the user's language.
   - Convert a requested radius to meters and pass it as `radius_m` (150–1600 m; 1 km → 1000, 0.5 mi → 805). If no radius is requested, omit it (default: 800 m, or 805 m in the US).
   - Some diagrams, such as sunlight or sky openness, take longer to draw. If the tool says it is still drawing, retry the same request as directed so the tool can pick up the existing job.
3. By default, `create_diagram` returns the base diagram without a title, legend or credits. If the user asks for annotations along with the diagram, use `annotate: true` in that call so they get one diagram and one viewer. Otherwise, don't add them in a second call unless asked.
4. Show the diagram in the current Claude surface.
   - **Chat, Desktop chat and Cowork:** `create_diagram` and `edit_diagram` show an inline viewer with SVG download and open-view controls. Don't describe the image again or put extra emphasis on the link.
   - **Claude Desktop Code tab** (where `mcp__ccd_view__show_pane` is available): the inline viewer collapses with the tool call. After every create or edit, call `get_diagram_file` with `format: "both"` and run its `save_command` to save the SVG and PNG to the working folder. Call `mcp__ccd_view__show_pane` with `pane: "file"` and `path` set to the returned `show_file` (the PNG). Start the reply with the local PNG as a Markdown image, for example `![Jamsil Station, 500 m — Building Height](sitedia_잠실역_height_v1.png)`, using the actual saved path. Don't use the browser pane's `navigate`; it opens in the background and the user won't see it.
   - **Claude Code in a terminal:** there is no inline or browser pane. Share `view_url` and save the file as described in section 4.
5. Keep the reply short: name the diagram, mention one or two useful observations and include `view_url`. If the preview reveals a problem, mention it.

## 3. Edit a diagram

- Translate the user's request into `edit_diagram` operations. Send all edits in one request as a single `edits` array.
  - “Make only offices blue” → `{op:"style", target:"업무", fill:"#2563eb"}`.
  - “Hide unknown” → `{op:"hide", target:"미상"}`. Use `show` to restore it.
  - “Replace this color with that one” → `{op:"recolor", from:"#…", to:"#…"}`.
  - “Add a legend” → `{op:"legend", position:"right"}`. For a title use `{op:"title", text:"…", subtitle:"…"}`; for credits use `{op:"credit"}`.
  - “Make the lines thicker” → `{op:"stroke_scale", factor:1.5}`. “Make the background white” → `{op:"background", circle:"#ffffff"}`.
  - For text use `{op:"text", text:"Myeong-dong Station", x:120, y:260}`. For an arrow, scale bar or shape use `{op:"svg", markup:"<g>…</g>"}`.
  - “Go back to the earlier version” → `{op:"revert", version:n}`. Send a revert on its own.
- Copy exact target names from the result's `layers` list. Use `describe_diagram` if needed.
- The site center is (0, 0); one unit is one meter. Positive x is east, positive y is south (SVG). The circle radius is `radius_m`. North is negative y.
- After editing, summarize the change in a line or two and include the new `view_url`. If the preview doesn't match the request, correct it once.
- By default, keep text, labels and arrows out of the circle. Add a title, legend or credits outside it only when requested. If the user explicitly asks to put something inside, do so. To change existing title or legend text or placement, send another `title` or `legend` operation; it replaces rather than duplicates the annotation.

### Combine, edit directly or follow changes live

- `combine_diagrams` makes a new diagram starting at version 1.
  - To layer diagrams of the same site, use `mode: "overlay"`. The first diagram goes on the bottom. Put area layers such as green space or zoning before buildings and roads. Upper layers drop their background circle and gray buildings automatically. Don't overlay different sites.
  - To compare diagrams side by side, including different sites, use `mode: "grid"` with `columns`, `captions` or `title` as needed. Use the cell caption as the target when moving (`transform`) or editing a grid cell.
- Use `attr` for details such as line caps, joins, fill rules and fonts. Use `transform` to move, rotate or scale items.
- If a tool can't make the requested edit (for example, reshaping a path) and a shell is available, edit the saved SVG directly. Preserve the root `viewBox` and run `upload_command` from `get_diagram_file` to save it as the next version.
- The `live_url` is shared across the user's diagrams and follows the latest creation, edit or combination within about three seconds. Share it once if the user wants to watch changes as they work.

## 4. Files

- **Desktop Code tab:** save the SVG and PNG and show the PNG as described in section 2. Also include a Markdown link to the saved SVG.
- **Terminal Claude Code and Cowork with a shell:** after each creation or edit, run `save_command` from `get_diagram_file` to save the SVG in the working folder and tell the user where it is. Save each version as a separate file.
- If the user asks you to edit a saved SVG, prefer `edit_diagram` when possible so the server's version history stays in sync.
- **Chat:** share the SVG and PNG links from `get_diagram_file`. The SVG preserves layer names for Illustrator and Figma.

## 5. When a tool cannot complete the request

- If `available: false` is returned, relay `message` and any `buy_url` in the user's language without changing the link, then stop. When a pass expires, editing, combining and downloading existing diagrams for that country can also return this notice.
- If the error suggests a correction, such as a valid layer name, apply it and retry once.
