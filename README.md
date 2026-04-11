# X Tweet Skills 🐦

基于 OpenClaw 的 X 平台推文自动创作技能包。覆盖从选题采集到推文交付的完整流水线。

## 技能列表

| 技能 | 功能 | 触发词 |
|------|------|--------|
| [hot-topics](skills/hot-topics/SKILL.md) | 热点选题采集 | 「采集热点」「找选题」 |
| [topic-research](skills/topic-research/SKILL.md) | 选题调研，搜集多源素材 | 「调研XX」「搜集资料」 |
| [opinion-generator](skills/opinion-generator/SKILL.md) | 从10个角度生成独特观点 | 「生成观点」「提炼角度」 |
| [tweet-writer](skills/tweet-writer/SKILL.md) | 10种风格推文写作 | 「写推文」「tweet」 |
| [tweet-polish](skills/tweet-polish/SKILL.md) | 口语化改写，消除AI味 | 「口语化」「polish」 |
| [title-optimizer](skills/title-optimizer/SKILL.md) | 5个方向的标题优化 | 「起标题」「优化标题」 |
| [tweet-closing](skills/tweet-closing/SKILL.md) | 5种结尾转化钩子 | 「写结尾」「加钩子」 |
| [tweet-pipeline](skills/tweet-pipeline/SKILL.md) | 主控流水线：一键全流程 | 「流水线」「一键推文」 |

## 安装

将 `skills/` 目录下的文件夹复制到 `~/.openclaw/skills/` 即可：

```bash
# 克隆仓库
git clone https://github.com/chencore/tweet-skills.git

# 复制到 OpenClaw skills 目录
cp -r tweet-skills/skills/* ~/.openclaw/skills/
```

## 使用流程

### 方式一：流水线模式（推荐）

直接说「流水线写XX选题」，自动走完全流程：

选题采集 → 信息调研 → 观点生成 → 推文写作 → 口语化改写 → 标题优化 → 结尾钩子 → 交付

### 方式二：分步使用

1. **「采集热点」** → 自动采集今日热点选题
2. **「调研 Muse Spark」** → 搜集素材
3. **「生成观点」** → 10个角度的观点
4. **「写推文」** → 10种风格的推文
5. **「口语化」** → 改写成真人说话风格
6. **「起标题」** → 5个方向的标题
7. **「加钩子」** → 5种结尾转化

## 流水线架构

```
cron 定时触发
    ↓
hot-topics（采集热点）
    ↓
topic-research（选题调研）
    ↓
opinion-generator（生成观点）
    ↓
tweet-writer（写推文）
    ↓
tweet-polish（口语化改写）
    ↓
title-optimizer（标题优化）
    ↓
tweet-closing（结尾钩子）
    ↓
交付成品 / 发布
```

## 依赖

- [OpenClaw](https://github.com/openclaw/openclaw) 运行环境
- web_fetch / agent-reach（用于信息采集）

## 许可证

MIT
