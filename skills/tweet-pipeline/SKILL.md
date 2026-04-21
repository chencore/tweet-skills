---
name: tweet-pipeline
description: |
  推文创作主控流水线：选题→调研→观点→推文→优化→交付。
  一键完成从选题到400-600字可发布推文的全流程，强调收藏价值。
  触发词：「流水线」「一键推文」「pipeline」「完整推文」。
triggers:
  - 流水线
  - 一键推文
  - pipeline
  - 完整推文
output_schema:
  type: object
  properties:
    title_options:
      type: array
      items: { type: string }
      description: 3个标题备选
      maxItems: 3
    tweet_versions:
      type: array
      items:
        type: object
        properties:
          style: { type: string }
          content: { type: string }
          word_count: { type: number }
          collection_point: { type: string }
        required: [style, content, word_count]
      description: 3个最优版本（默认模式）或10个版本（完整模式）
    closing_hooks:
      type: array
      items: { type: object }
      description: 5种结尾钩子
    publish_suggestion:
      type: object
      properties:
        best_time: { type: string }
        hashtags: { type: array }
        image_suggestion: { type: string }
  required: [title_options, tweet_versions, closing_hooks, publish_suggestion]
tools:
  - web_fetch
---

# 推文创作流水线

收到用户输入的选题后，按顺序执行以下流程。

## Phase 1: 调研

调用 topic-research skill 的逻辑：
- 搜索 X、新闻源、Reddit/HN 上的相关信息
- 提取核心事实、各方观点、数据、争议点
- 整理成结构化素材

## Phase 2: 生成观点

调用 opinion-generator skill 的逻辑：
- 从10个角度（反常识、二阶效应、利益分析等）生成观点
- 每个观点配支撑论据
- 展示给用户选择

## Phase 3: 写推文

调用 tweet-writer skill 的逻辑：
- 根据用户选择的角度，生成10个风格版本
- **字数严格控制在 400-600字**
- 每个版本必须有收藏理由（实用/独特/有增量）

## Phase 4: 优化

- 调用 title-optimizer 逻辑生成 5 个标题，从中选择最好的 3 个
- 调用 tweet-polish 逻辑进行口语化检查，确保 AI 味评分 ≤ 4
- 调用 tweet-closing 逻辑生成 5 种结尾钩子

## Phase 5: 交付

最终输出包含：

| 项目 | 说明 |
|------|------|
| 📌 标题 | 3 个备选（从5个中选最好的3个） |
| 📝 推文正文 | 默认 3 个最优版本（完整模式输出 10 个） |
| 🎣 结尾钩子 | 5 种类型 |
| 📅 发布建议 | 时间、配图、标签建议 |

**所有推文字数严格控制在 400-600字**

## 保存

保存到：`~/.openclaw/workspace-self_media/tweets/YYYY-MM-DD-选题名.md`

## 快速模式

如果用户说「快速版」或「只要成品」：
- 跳过 Phase 2 的选择环节
- 直接选最有传播力的 3 个角度
- 只输出 3 个最优版本
- 不展示中间过程

## 输出格式

```json
{
  "title_options": [
    "工具爆发≠团队升级，这个判断3年后依然有效",
    "AI编程工具300%增长背后：采纳率卡在20%",
    "为什么90%的团队用不好AI编程工具"
  ],
  "tweet_versions": [
    {
      "style": "震撼型",
      "content": "推文正文...",
      "word_count": 523,
      "collection_point": "存下这个判断，3个月后验证"
    }
  ],
  "closing_hooks": [
    { "type": "提问型", "content": "你怎么看？" },
    { "type": "开放型", "content": "你身边有这种例子吗？" }
  ],
  "publish_suggestion": {
    "best_time": "工作日早上9-10点",
    "hashtags": ["#AI编程", "#效率工具"],
    "image_suggestion": "数据图表：300% vs 20% 对比"
  }
}
```

## 示例

### 输入

```
选题：AI编程工具爆发但团队采纳率低
模式：快速模式
```

### 输出

```json
{
  "title_options": [
    "工具爆发≠团队升级，这个判断3年后依然有效",
    "AI编程工具300%增长背后：采纳率卡在20%",
    "为什么90%的团队用不好AI编程工具"
  ],
  "tweet_versions": [
    {
      "style": "震撼型",
      "content": "AI编程工具爆发了。GitHub数据显示年增300%。但我调研了20家中型公司，18家还在用老方法。\n\n300%和20%，这两个数字放在一起很奇怪。原因很简单：不是工具不行，是团队的工作流改不动。\n\n很多公司买AI编程工具的方式是——买一个license，给所有人用，然后等着效率提升。结果发现没人用。\n\n真正跑起来的团队只有一种模式：找一个懂工具的人，给他2周时间，把工作流改一遍，然后其他人自然跟上来。\n\n工具爆发，但采纳率卡在20%。不是技术问题，是组织问题。\n\n这个数字值得收藏。3年后回头看。",
      "word_count": 198,
      "collection_point": "工具爆发≠团队升级，这个判断3年后依然有效"
    }
  ],
  "closing_hooks": [
    { "type": "提问型", "content": "你身边有这种例子吗？", "goal": "reply" },
    { "type": "转发型", "content": "转给需要提升团队效率的朋友", "goal": "retweet" },
    { "type": "关注型", "content": "这个话题我会持续跟踪", "goal": "follow" }
  ],
  "publish_suggestion": {
    "best_time": "工作日早上9-10点",
    "hashtags": ["#AI编程", "#效率工具", "#团队管理"],
    "image_suggestion": "对比图：300% 增长 vs 20% 采纳率"
  }
}
```
