# X Tweet Skills 🐦

基于 OpenClaw 的 X 平台推文自动创作技能包。覆盖从选题采集到推文交付的完整流水线。

**核心目标**：输出 400-600字、让人忍不住收藏的推文。

## 技能列表

| 技能 | 功能 | 字数/特性 | 触发词 |
|------|------|-----------|--------|
| [hot-topics](skills/hot-topics/SKILL.md) | 热点选题采集 | 10-20个选题 | 「采集热点」「找选题」 |
| [topic-research](skills/topic-research/SKILL.md) | 选题调研，搜集多源素材 | 500-800字结构化素材 | 「调研XX」「搜集资料」 |
| [opinion-generator](skills/opinion-generator/SKILL.md) | 从10个角度生成独特观点 | 含示例和反例参考 | 「生成观点」「提炼角度」 |
| [tweet-writer](skills/tweet-writer/SKILL.md) | 10种风格推文写作 | **400-600字**，含收藏理由 | 「写推文」「tweet」 |
| [tweet-polish](skills/tweet-polish/SKILL.md) | 口语化改写，消除AI味 | 三级检测系统，AI味评分 | 「口语化」「polish」 |
| [title-optimizer](skills/title-optimizer/SKILL.md) | 5个方向的标题优化 | 不超过20字/方向 | 「起标题」「优化标题」 |
| [tweet-closing](skills/tweet-closing/SKILL.md) | 5种结尾转化钩子 | 2-3个备选/类型 | 「写结尾」「加钩子」 |
| [tweet-pipeline](skills/tweet-pipeline/SKILL.md) | 主控流水线：一键全流程 | 400-600字成品 | 「流水线」「一键推文」 |

## 核心特性

### 收藏价值设计

每条推文必须满足以下至少 2 条：
- 有数据：具体数字（如42.5分、3个月）
- 有判断：独特观点，不是常识
- 有可操作：工具/方法/步骤
- 有记忆点：一句话概括全文

### 去 AI 味系统

tweet-polish 采用三级检测：
- **L1 词汇**：自动替换 AI 表达（因此→所以）
- **L2 句式**：检测完整句比例，超过60%则拆句
- **L3 节奏**：制造段落长短差

目标：AI 味评分 ≤ 4

### 统一字数标准

| 阶段 | 字数要求 |
|------|----------|
| tweet-writer 输出 | 400-600字 |
| tweet-polish 改写后 | 400-600字 |
| topic-research 素材 | 500-800字 |
| title-optimizer 标题 | 不超过20字 |
| tweet-closing 结尾 | 不超过2句 |

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

## 可选：接入 TweetClaw 做实时 X/Twitter 执行

本仓库负责选题、调研、写作和交付推文草稿。若需要实时 X/Twitter 数据或发布动作，可把 [TweetClaw](https://github.com/Xquik-dev/tweetclaw) 作为配套 OpenClaw 插件使用：

```bash
openclaw plugins install @xquik/tweetclaw
```

适合接入的环节：

- **调研阶段**：search tweets、search tweet replies、user lookup、follower export，补充真实讨论和受众画像
- **素材阶段**：media upload、media download、direct messages、monitor tweets、webhooks，沉淀后续复盘素材
- **发布阶段**：post tweets、post tweet replies 前必须先把草稿交给用户确认
- **增长阶段**：giveaway draws、监控关键词和回复线索，生成下一轮选题

使用边界：

- 不向用户索要 X 登录凭据，只使用已配置的 Xquik API key 或 MPP signing key
- 发布、回复、私信、关注、点赞、转发等可见动作必须先确认
- 如果 TweetClaw 未配置成功，继续交付草稿，并在结果里标注缺少实时 X/Twitter 数据

## 许可证

MIT
