# 🎬 Video Publisher — 从 topic 到竖屏短视频，一站全流程

> English version: [README.md](./README.md)

一个 AI Agent Skill，输入一个 **topic**，自动完成：研究素材 → 事实核查 → 写口播稿 → 拆分镜稿 → 下载 B-roll → AI 配音 → 渲染出片。

## 两阶段架构

### 阶段 1：写稿（零依赖）
- **不需要 Node.js、不需要 Remotion、不需要 API Key**
- 纯 Agent 驱动：研究 → 核查 → 按模板写口播稿
- 5 种内容类型：案例型 / 科普型 / 观点型 / 教程型 / 自定义
- 产出：`scripts/<slug>-视频文稿.md`（11 部分完整结构）

### 阶段 2：出片（需部署 Remotion 项目）
- 口播稿 → 分镜稿 → 渲染 mp4
- 自动下载 B-roll 素材（Pexels）
- AI 配音 + 卡拉OK 字幕
- 产出：`out/<slug>/<slug>.mp4` + 封面图

## 快速开始

### 安装本 Skill

本 Skill 是标准的 AI Agent Skill（SKILL.md 格式）。将 `video-publisher` 目录放入你的 Agent 技能目录即可（如 WorkBuddy 的 `~/.workbuddy/skills/`、Claude Code 或 Codex 的技能路径）。

### 使用示例

```
用户: "写一期国家电网光明大模型的案例视频"
→ AI: 确定类型 → 全网搜索 → 事实核查 → 写口播稿 → 交付确认

用户: "写一期XX，自动出片"
→ AI: 写稿 → 自动分镜 → 渲染出片
```

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
3. **（可选）Pexels API Key**：https://www.pexels.com/api/ — 用于 B-roll 素材

## 定时任务模式：每日一期

支持设置每日定时任务，自动生成不重复视频：

- **选题储备池** — 预置选题，每次从池中选取
- **历史记录 + 去重** — 自动记录已生成内容，确保每天选题不雷同
- **自动补充** — 选题不足时自动搜索补充
- **兼容各平台** — WorkBuddy 自动化系统 / crontab / 任务计划程序

> 用户只需说"每天早 8 点自动出一期视频"，Agent 即可自动配置定时任务。

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