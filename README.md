# 🎬 Video Publisher — From Topic to Vertical Short Video, All-in-One

> 🇨🇳 中文版请见 [README-ZH.md](./README-ZH.md)

A WorkBuddy Skill that turns any **topic** into a **9:16 vertical short video** — research, script writing, storyboard, B-roll, AI voiceover, and rendering, all in one pipeline.

## Two-Phase Architecture

### Phase 1: Script Writing (Zero Dependencies)
- **No Node.js, no Remotion, no API keys required**
- Pure Agent-driven: research → fact-check → write structured script
- 5 content types: Case Study / Science Pop / Opinion / Tutorial / Custom
- Output: `scripts/<slug>-视频文稿.md` (11-part structured script)

### Phase 2: Video Rendering (Requires Remotion Project)
- Converts script → storyboard → renders MP4
- Automatic B-roll download (Pexels)
- AI voiceover + karaoke subtitles
- Output: `out/<slug>/<slug>.mp4` + cover image

## Quick Start

### Install the Skill

This Skill is designed for **WorkBuddy** (a conversational AI IDE). Install it by placing the `video-publisher` directory under `~/.workbuddy/skills/`.

### Use It

```
User: "写一期国家电网光明大模型的案例视频"
→ AI: 确定内容类型 → 全网搜索 → 事实核查 → 写口播稿 → 交付确认

User: "写一期XX，自动出片"
→ AI: 写稿 → 自动分镜 → 渲染出片
```

## Content Types

| Type | Structure | Use Case |
|---|---|---|
| Case Study | 3-Hook opening + 6-part body | Enterprise/tech case analysis |
| Science Pop | Suspense opening + 4-part body | Knowledge explanation |
| Opinion | Opinion-first opening + 3-part argument | Commentary, analysis |
| Tutorial | Problem opening + step-by-step body | How-to, guide |
| Custom | Flexible | User-defined structure |

## Free TTS — No API Key Needed

| Platform | Engine | Config |
|---|---|---|
| macOS | `say` command | Zero config |
| Windows | PowerShell `System.Speech` | Zero config |
| Linux | `espeak-ng` | `sudo apt install espeak-ng` |

Default `TTS_PROVIDER=auto` auto-detects your platform. For premium human-like Chinese voiceover, set `TTS_PROVIDER=volcano` in `.env`.

## Prerequisites for Video Rendering

To use Phase 2 (video rendering), you need:

1. **Remotion project**: `git clone https://github.com/scarecrowang/remotion-video-publisher.git`
2. **Node.js** ≥ 18
3. **(Optional) Pexels API Key**: https://www.pexels.com/api/ — for B-roll footage

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