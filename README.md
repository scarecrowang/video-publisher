# 🎬 Video Publisher — From Topic to Vertical Short Video, All-in-One

> 🇨🇳 中文版请见 [README-ZH.md](./README-ZH.md)

An AI Agent Skill that turns any **topic** into a **9:16 vertical short video** — research, script writing, storyboard, B-roll, AI voiceover, and rendering, all in one pipeline.

## Architecture

```
User topic / Scheduled trigger
    │
    ▼
┌─ Global Dedup Guard ────────────────────────┐
│  Read history → dedup check → if unique → go│
│  If duplicate → reject/adjust/switch topic  │
└──────────────────────────────────────────────┘
    │
    ▼
┌─ Phase 1: Script Writing (Zero Deps) ─────┐
│  Pure Agent: research → fact-check → write │
│  5 content types, controlled word count    │
│  Output: scripts/<slug>-视频文稿.md         │
└──────────────────────────────────────────────┘
    │
    ▼
┌─ Phase 2: Video Rendering ─────────────────┐
│  Script → storyboard → B-roll → TTS → mp4  │
│  Output: out/<slug>/<slug>.mp4 + cover     │
└──────────────────────────────────────────────┘
```

## Core Features

### Global Dedup Guard (All Triggers)
Whether the user manually enters a topic, describes it in natural language, or a scheduled task fires — **dedup check runs before every script writing**:
- Auto-maintains `scripts/.publish-history.json`
- 5 dimensions: exact match, keyword overlap, entity overlap, semantic similarity, angle duplication
- Case study rules: same enterprise → 30-day gap, same track → 7-day gap
- On duplicate: auto-suggest alternative angles

### Scheduled Mode: Dual Trigger

| Trigger | Description |
|---|---|
| **A. Agent's own automation system** | WorkBuddy / Claude Code / Codex — just say "Run one video every day at 8 AM" and the Agent creates the scheduled task |
| **B. Natural language description** | Any Agent platform — say "Help me publish one video daily" and the Agent handles it conversationally |

> Both modes share the same history file and topic pool for global dedup consistency.

### Auto ffmpeg Detection & Installation
ffmpeg is required for audio processing in Remotion — **Windows users will get no audio without it**. The Agent automatically detects and installs ffmpeg:
- **macOS**: `brew install ffmpeg`
- **Windows**: `winget install FFmpeg`
- **Linux**: `sudo apt install ffmpeg -y` (or yum/pacman)

> See SKILL.md Step 7 for full details. No manual action needed.

## Repository Structure

```
video-publisher/
├── SKILL.md                          # Main skill file
├── README.md                         # English docs
├── README-ZH.md                      # Chinese docs
└── references/
    ├── script-template.md            # Script templates (5 types)
    ├── md-grammar.md                 # Storyboard grammar
    └── type-templates.md             # Theme/type templates
```

## Related Projects

- **[remotion-video-publisher](https://github.com/scarecrowang/remotion-video-publisher.git)** — The Remotion rendering engine used by Phase 2

## License

UNLICENSED — for personal/project use. Contact the author for commercial licensing.