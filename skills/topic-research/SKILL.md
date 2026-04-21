---
name: topic-research
description: |
  给定一个选题，自动搜集多源信息，整理成结构化素材。
  触发词：「调研XX」「搜集XX的资料」「research XX」「调研选题」。
triggers:
  - 调研
  - 搜集资料
  - research
  - 调研选题
output_schema:
  type: object
  properties:
    topic: { type: string }
    core_facts: { type: array }
   各方观点: { type: object }
    key_data: { type: array }
   争议点: { type: array }
    background: { type: string }
    sources: { type: array }
tools:
  - web_fetch
---

# 选题调研

收到选题后，按以下步骤执行。

## Step 1: 多源搜索

搜索顺序：
1. **X/Twitter** - 搜索相关讨论，提取观点和情绪
2. **新闻源** - web_fetch 抓取核心文章，提取事实和数据
3. **Reddit/HN** - 搜索深度讨论
4. **中文平台** - 知乎、36氪等（如适用）

## Step 2: 提取关键信息

从搜索结果中提取：
- **核心事实**：这件事到底发生了什么？
- **各方观点**：支持方说什么？反对方说什么？
- **数据支撑**：有没有具体数字？
- **争议点**：最大的分歧在哪里？
- **背景信息**：这件事的前因是什么？

## Step 3: 整理输出

保存到 `~/.openclaw/workspace-self_media/research/YYYY-MM-DD-选题名.md`

## 输出格式

```json
{
  "topic": "AI编程工具爆发",
  "core_facts": [
    "GitHub数据显示AI编程工具年增300%",
    "中小企业采纳率不足20%"
  ],
  "各方观点": {
    "支持方": ["工具提升效率", "开发者工作方式改变"],
    "反对方": ["学习成本高", "团队工作流难改"]
  },
  "key_data": [
    "300%年增",
    "20%采纳率",
    "42.5分抽象推理（vs 头部76分）"
  ],
  "争议点": [
    "工具爆发≠团队升级",
    "技术采纳率低于预期"
  ],
  "background": "AI编程工具从2023年开始爆发...",
  "sources": [
    {"name": "GitHub年报", "url": "https://github.blog/..."},
    {"name": "TechCrunch文章", "url": "https://techcrunch.com/..."}
  ]
}
```

## 注意

- 信息需要交叉验证，不采用单一来源
- 区分事实和观点
- 标注信息来源
- 字数控制在 500-800字

## 示例

### 输入

```
调研：AI编程工具爆发但团队采纳率低
```

### 输出

```json
{
  "topic": "AI编程工具爆发但团队采纳率低",
  "core_facts": [
    "GitHub数据显示AI编程工具使用率年增300%",
    "中小企业采纳率不足20%",
    "Arena评测Muse Spark排第三但抽象推理仅42.5分"
  ],
  "各方观点": {
    "支持方": [
      "工具是未来，不接受会被淘汰",
      "已经采纳的团队效率提升显著"
    ],
    "反对方": [
      "学习曲线太陡，团队不愿意改",
      "现有工作流稳定，没必要换"
    ]
  },
  "key_data": [
    "300%年增",
    "20%采纳率",
    "头部模型抽象推理76分",
    "Muse Spark 42.5分"
  ],
  "争议点": [
    "排名vs具体能力",
    "工具爆发vs组织采纳"
  ],
  "background": "AI编程工具从2023年开始爆发，GitHub Copilot引领...",
  "sources": [
    {"name": "GitHub年报2025", "url": "https://github.blog/ai-report-2025"},
    {"name": "Arena评测报告", "url": "https://arena.com/q1-2026"}
  ]
}
```
