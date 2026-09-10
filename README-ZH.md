# 🎬 Video Publisher — 从 topic 到竖屏短视频，一站全流程

> English version: [README.md](./README.md)

一个 AI Agent Skill，输入一个 **topic**，自动完成：研究素材 → 事实核查 → 写口播稿 → 拆分镜稿 → 下载 B-roll → AI 配音 → 渲染出片。

## 架构

```
用户输入 topic / 定时任务触发
    │
    ▼
┌─ 前置步骤：全局去重保障 ──────────────────┐
│  读取历史记录 → 执行去重判断 → 不重复则继续 │
│  重复 → 拒绝/调整/换题                      │
└──────────────────────────────────────────────┘
    │
    ▼
┌─ 阶段 1：写稿（零依赖） ──────────────────┐
│  纯 Agent 驱动：研究 → 核查 → 按模板写稿  │
│  5 种内容类型，字数可控                     │
│  产出：scripts/<slug>-视频文稿.md           │
└──────────────────────────────────────────────┘
    │
    ▼
┌─ 阶段 2：出片（需部署 Remotion 项目） ────┐
│  口播稿 → 分镜稿 → 下载 B-roll → TTS → 渲染│
│  产出：out/<slug>/<slug>.mp4 + 封面图       │
└──────────────────────────────────────────────┘
```

## 核心特性

### 全局去重保障（所有触发方式通用）
无论用户手动输入 topic、自然语言描述需求，还是定时任务自动触发，**每次写稿前必须执行去重检查**，确保不产出雷同内容：
- 自动维护 `scripts/.publish-history.json` 历史记录
- 5 个判断维度：完全匹配、关键词重叠、企业/实体重叠、语义相似度、角度雷同
- 案例型更严格：同一企业间隔 30 天，同一赛道间隔 7 天
- 判定重复时自动提供差异化建议

### 定时任务：双触发方式

| 触发方式 | 说明 |
|---|---|
| **A. Agent 自身自动化系统** | WorkBuddy / Claude Code / Codex 等内置自动化功能的 Agent，用户说"每天早8点出一期"即可自动创建定时任务 |
| **B. 自然语言描述** | 任何 Agent 平台通用，用户说"帮我每天出一期"即可，Agent 在对话中主动按日执行 |

> 两种方式共享同一套历史记录和储备池，去重全局一致。

## 内容类型

| 类型 | 结构 | 适用场景 |
|---|---|---|
| 案例型 | 三钩子开头 + 六段式主体 | 企业/技术案例分析 |
| 科普型 | 悬念开头 + 四段式主体 | 知识讲解、概念解读 |
| 观点型 | 观点前置 + 三段式论据 | 评论、分析 |
| 教程型 | 问题引入 + 分步式主体 | How-to、操作指南 |
| 自定义 | 灵活适配 | 用户描述结构 |

## 本地真人 TTS（免 API Key）— 推荐

| 方案 | 说明 |
|---|---|
| **MOSS-TTS-Nano（本地部署，auto 首选）** | 开源 Apache-2.0 · CPU 实时 · 中文标准普通话 · 免 Key。一次性部署：`git clone https://github.com/OpenMOSS/MOSS-TTS-Nano.git && pip install -r requirements.txt && pip install -e .` 然后 `moss-tts-nano serve`（默认 http://127.0.0.1:18083，首启约 5 分钟下载模型）。可选 `MOSS_PROMPT_AUDIO=<参考音频>` 做 3 秒语音克隆 |
| 火山引擎 Seed TTS | 真人感最强、中文最佳。`.env` 填 `TTS_PROVIDER=volcano` + `VOLCANO_API_KEY` + `VOLCANO_SPEAKER`（按量付费） |
| 免费 TTS（最后兜底） | 平台自带：macOS `say` / Windows PowerShell `System.Speech`（均零配置）/ Linux `espeak-ng`（`sudo apt install espeak-ng`） |

> 默认 `TTS_PROVIDER=auto`：本地 MOSS 服务可达 → 用 MOSS（真人感、免 Key）；否则回落平台免费 TTS。

## 出片前置条件

要用阶段 2（渲染出片），需要：

1. **Remotion 项目**：`git clone https://github.com/scarecrowang/remotion-video-publisher.git`
2. **Node.js** ≥ 18
3. **ffmpeg**（自动检测安装，**Windows 必装否则音频无声**）
4. **（可选）Pexels API Key**：https://www.pexels.com/api/ — 用于 B-roll 素材

> Agent 会自动检测并安装 ffmpeg，无需手动操作。详见 SKILL.md 第 7 步。

## 安装（关键：必须装到技能目录，否则定时任务检索不到）

**Skill 必须安装到对应 Agent 的技能目录**，而不是任意文件夹。若安装到别处，`search_capabilities` 不会把技能编入索引，定时任务第二天运行时会出现"找不到 skill"。

| 平台 / Agent | 技能目录 |
|---|---|
| WorkBuddy（macOS/Linux） | `~/.workbuddy/skills/` |
| WorkBuddy（Windows） | `%USERPROFILE%\.workbuddy\skills\` |
| Claude Code | `~/.claude/skills/` |
| Codex | `~/.codex/skills/` |

安装后自检：

```bash
# macOS / Linux
ls ~/.workbuddy/skills/video-publisher/SKILL.md

# Windows（PowerShell）
ls $env:USERPROFILE\.workbuddy\skills\video-publisher\SKILL.md
```

> 若刚安装就创建了定时任务，建议重启 Agent 或刷新技能索引后再触发第一次任务。定时任务 prompt 应包含 `video-publisher` 关键词并指向技能文件路径，详见 SKILL.md「定时任务找不到 Skill 的自愈流程」。

## 仓库结构

```
video-publisher/
├── SKILL.md                          # 核心技能文件
├── README.md                         # 英文文档
├── README-ZH.md                      # 中文文档
└── references/
    ├── script-template.md            # 口播稿模板（5 种类型）
    ├── md-grammar.md                 # 分镜稿语法
    └── type-templates.md             # 题材模板
```

## 相关项目

- **[remotion-video-publisher](https://github.com/scarecrowang/remotion-video-publisher.git)** — 阶段 2 用到的 Remotion 渲染引擎

## License

UNLICENSED — 个人/项目使用。商业授权请联系作者。