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

## 免费 TTS — 不需要 API Key

| 平台 | 引擎 | 配置 |
|---|---|---|
| macOS | `say` 命令 | 零配置 |
| Windows | PowerShell `System.Speech` | 零配置 |
| Linux | `espeak-ng` | `sudo apt install espeak-ng` |

默认 `TTS_PROVIDER=auto` 自动检测平台。想用真人感中文配音，在 `.env` 中设为 `TTS_PROVIDER=volcano`。

## 出片前置条件

要用阶段 2（渲染出片），需要：

1. **Remotion 项目**：`git clone https://github.com/scarecrowang/remotion-video-publisher.git`
2. **Node.js** ≥ 18
3. **ffmpeg**（自动检测安装，**Windows 必装否则音频无声**）
4. **（可选）Pexels API Key**：https://www.pexels.com/api/ — 用于 B-roll 素材

> Agent 会自动检测并安装 ffmpeg，无需手动操作。详见 SKILL.md 第 7 步。

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