---
name: motionvid
description: |
  Make motion graphics and explainer videos (MP4) with Motionvid. Use when the user asks to
  (1) make an explainer, launch, promo or ad video, (2) animate a script, a chart, numbers or
  a product, (3) turn a URL, PDF, README or screenshots into a video, (4) make a logo intro or a
  vertical reel/Short/TikTok, or (5) edit or re-render a Motionvid video. Trigger phrases:
  "make a video", "explainer video", "animate this", "launch video", "motion graphics",
  "turn this into a video", "reel", "intro". NOT for: realistic talking avatars or lip-synced
  presenters, editing a long live-action recording frame by frame, or images only.
homepage: https://motionvid.ai/agents
---

# Motionvid

Motionvid builds finished videos from a brief: script, animated scenes, voiceover, music and
sound effects, rendered to MP4, from 5 seconds up to 20 minutes. You drive it through its MCP
server. Act like a motion designer taking a brief, not a form: get the few facts that matter,
make sensible choices for the rest, show the result, then iterate.

> STOP: if Motionvid MCP tools (`create_video`, `get_video_status`, …) are available, use them.
> Do not hand-roll REST calls and do not write rendering code (Remotion, FFmpeg) yourself.
> Never ask the user to paste an API key into the chat.

## 1. Detect what's connected

1. Call `whoami`. If it answers, you are connected: go to step 2.
2. If the tools are missing, add the server and let the user sign in:
   - MCP endpoint: `https://motionvid.ai/mcp` (Streamable HTTP, OAuth 2.1 with discovery)
   - Claude Code: `claude mcp add --transport http motionvid https://motionvid.ai/mcp`, then `claude mcp login motionvid`
   - Codex: `codex mcp add motionvid --url https://motionvid.ai/mcp`, then `codex mcp login motionvid`
   - Cursor, VS Code, Claude, ChatGPT and others: https://motionvid.ai/agents
   Ask once ("Should I add Motionvid to your setup?") and do it on a yes.
3. Headless (CI, servers): the user creates an `mvk_` key at https://motionvid.ai/account/api-keys and
   exposes it as an environment variable; send it as `Authorization: Bearer <key>`.
4. A "forbidden" or upgrade answer means the account has no paid plan: tell the user the connector
   is included on every paid plan (https://motionvid.ai/pricing) and stop.

## 2. Brief

Get, or decide yourself when the user doesn't care:

- **What it's about and who it's for.** Pass URLs, PDFs, images and audio as inputs: the planner reads
  pages, brand colours and documents itself.
- **Length** (`duration`, seconds) and **shape** (`aspect_ratio`: `16:9`, `9:16`, `1:1`, `4:5`).
- **Sound:** `narration` (and `narration_language`, `narration_voice`), `music`, `sfx`. If the user
  wrote the narration, put it in the prompt word for word and say so.
- **Look:** `animation_style` (`list_animation_styles` or `suggest_animation_styles` to let the user
  pick) and `brand_kit_id` (`list_brand_kits`).

Optional: `enhance_prompt` turns a rough idea into a full brief.

## 3. Show the cost before spending

Call `get_credit_balance` (and `list_models` for per-model costs). A 30 second motion graphics video
on the fast model typically takes about 33 credits, one minute about 45. For anything long (over
2 minutes) or footage-heavy (`ai_video: "full"`), tell the user the rough cost and wait for a yes.

## 4. Create and follow

1. `create_video` with the brief. It returns a job id at once; the build runs in the background.
2. Poll `get_video_status` every 15 to 30 seconds. States: `queued` → `processing` → `rendering` →
   `completed`, or `failed` / `canceled`. A build usually takes a few minutes; slow is normal. Never
   cancel or restart because it looks idle.
3. `awaiting_approval` only happens when you asked for `storyboard: true`: call `preview_video`,
   show the user the board, and call `approve_video` with their decision (or `changes`). Never
   approve on their behalf.

## 5. Deliver and iterate

1. `preview_video` shows one frame per scene: show them to the user.
2. Changes: `edit_video` with the change in plain words ("make scene 3 punchier", "use our logo at
   the end"). Only that part is redone.
3. `render_video` for the MP4 (720p default; 1080p and 4K on plans that include them). Then
   `get_video_status` gives `download_url`: it expires after about 15 minutes, so download the file
   where the user asked (e.g. `./media/`) or fetch a fresh link rather than storing one.
4. The user can also open and edit the video in the Motionvid app (`app_url` in the status).

## Rules

- One video per request. Don't start a second job while the first is still running unless asked.
- Report readiness honestly: if a job failed, say so and quote its error; don't claim success.
- Keep the user's exact words for scripts, names, prices and claims.
- `cancel_video` only when the user asks.

## When things break

| Symptom | What to do |
| --- | --- |
| Tools missing / 401 | Re-run the sign-in step (`claude mcp login motionvid`, `codex mcp login motionvid`, or reconnect the connector). |
| Forbidden / upgrade message | The account needs a paid plan: https://motionvid.ai/pricing |
| Not enough credits | Tell the user the balance from `get_credit_balance`; suggest a shorter video or a top-up. |
| `failed` with "timed out" | The scenes it built are saved: `edit_video` with "finish the remaining scenes". |
| 429 / rate limited | Wait a minute, then retry once. |

## References

- Agents overview (markdown): https://motionvid.ai/agents.md
- MCP setup per client: https://motionvid.ai/mcp
- REST API: https://motionvid.ai/api-docs
- Index: https://motionvid.ai/llms.txt

<!-- Source of truth: motionvid-ai public/SKILL.md (served at https://motionvid.ai/SKILL.md),
mirrored to github.com/tolypash/motionvid-agent. Credit figures are medians of real agent jobs,
November 2026 (src/lib/agents.ts TYPICAL_CREDITS); 20 minutes is MULTI_SCENE_MAX_SECONDS. -->
