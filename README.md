# Motionvid for AI agents

Give your agent a video studio. With this skill and the Motionvid MCP server, Claude Code, Cursor, Codex, Hermes and other agents make **motion graphics and explainer videos** from a prompt: script, animated scenes, voiceover, music and sound effects, rendered to MP4, from a few seconds up to 20 minutes.

![Motionvid](https://motionvid.ai/banner.jpg)

## Install

**Skill** (Claude Code, Cursor, Codex and other agents that read skills):

```sh
npx skills add tolypash/motionvid-agent
```

**MCP server** (if your agent doesn't have it yet):

```sh
# Claude Code
claude mcp add --transport http motionvid https://motionvid.ai/mcp
claude mcp login motionvid

# Codex
codex mcp add motionvid --url https://motionvid.ai/mcp
codex mcp login motionvid
```

**Claude Code plugin** (skill and server together):

```sh
/plugin marketplace add tolypash/motionvid-agent
/plugin install motionvid@motionvid
```

**Hermes:** `hermes plugins install motionvid` once listed, then `hermes mcp login motionvid`.

Claude (web and desktop), ChatGPT, Cursor and VS Code setup: [motionvid.ai/agents](https://motionvid.ai/agents).

## What your agent can do

| Tool | What it does |
| --- | --- |
| `create_video` | Generate a full video from a text prompt (async job) |
| `get_video_status` | Poll a job: progress, scenes, fresh download link |
| `preview_video` | See the result: one frame per scene |
| `edit_video` | Change a finished video by describing the change |
| `render_video` | Encode to MP4, or re-render at a new size |
| `approve_video` | Answer a paused storyboard review |
| `list_animation_styles`, `suggest_animation_styles` | Visual style packs |
| `list_voices`, `suggest_voices` | Narration voices |
| `list_brand_kits` | Your saved logo, colours and fonts |
| `list_models`, `get_credit_balance`, `whoami` | Costs, balance, account |
| `enhance_prompt`, `list_videos`, `update_video`, `cancel_video` | Prompt help and job management |

Try: *"Make a 30 second explainer on how solar panels work, with voiceover and music, and save the MP4 to ./media."*

## What it touches (disclosure)

- **Network:** the hosted Motionvid MCP server at `https://motionvid.ai/mcp`. Your prompts, any files or URLs you pass, and the generated videos are processed by Motionvid ([privacy policy](https://motionvid.ai/privacy-policy)).
- **Account and cost:** needs a Motionvid account on a paid plan (from $19/month, [pricing](https://motionvid.ai/pricing)). Creating, editing and rendering videos spends your Motionvid credits. The skill tells the agent to show the cost before long builds.
- **Credentials:** OAuth sign-in in your browser, handled by your agent's MCP client. No API key or secret is stored in this repo. Headless setups can use an `mvk_` API key from an environment variable.
- **No code runs locally:** this package is markdown and JSON only (a skill and an MCP server declaration).

## Links

- [Motionvid for AI agents](https://motionvid.ai/agents) · [as markdown](https://motionvid.ai/agents.md)
- [MCP setup per client](https://motionvid.ai/mcp)
- [REST API](https://motionvid.ai/api-docs)
- Support: nick@motionvid.ai

License: MIT
