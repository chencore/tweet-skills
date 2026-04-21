---
name: hot-topics
description: |
  采集多平台热点选题，筛选AI/科技/加密货币/创业相关内容，整理成结构化选题列表。
  触发词：「采集热点」「找选题」「热点」「今日选题」「hot topics」。
triggers:
  - 采集热点
  - 找选题
  - 热点
  - 今日选题
  - hot topics
output_schema:
  type: object
  properties:
    topics:
      type: array
      items:
        type: object
        properties:
          title: { type: string }
          collected_at: { type: string }
          source: { type: string }
          source_url: { type: string }
          description: { type: string }
        required: [title, collected_at, source]
      description: 10-20个选题，按热度排序
    total_count: { type: number }
tools:
  - web_fetch
---

# 热点选题采集

## 采集源

### 英文（优先级高）

| 来源 | 类型 | URL |
|------|------|-----|
| TechCrunch | 科技 | techcrunch.com |
| The Verge | 科技/AI | theverge.com |
| Hacker News | 技术/创业 | news.ycombinator.com |
| CoinDesk | 加密货币 | coindesk.com |
| Cointelegraph | 区块链 | cointelegraph.com |

### 中文

| 来源 | 类型 | URL |
|------|------|-----|
| 36氪 | 科技/创业 | 36kr.com |
| 知乎热榜 | 综合 | zhihu.com |
| 今日头条 | 热点 | toutiao.com |

## 执行步骤

1. 逐个抓取信源（用 web_fetch）
2. 筛选 AI/加密货币/科技/创业 相关内容
3. 过滤：只保留当日新闻，超过24小时的不采用
4. 整理 10-20 个选题，按热度排序

## 输出格式

```json
{
  "topics": [
    {
      "title": "Muse Spark发布新版本",
      "collected_at": "2026-04-21",
      "source": "TechCrunch",
      "source_url": "https://techcrunch.com/...",
      "description": "Muse Spark发布v2版本，抽象推理能力提升40%"
    }
  ],
  "total_count": 15
}
```

## 发送格式（用户友好版）

```
🔥 1. [标题]
   时间：YYYY-MM-DD
   来源：XXX
   链接：URL
   说明：一句话描述

🔥 2. [标题]
   ...
```

## 保存

保存到：`~/.openclaw/workspace-self_media/topics/hot-topics-YYYY-MM-DD.md`

## 示例

### 输入

```
采集热点：AI编程工具相关
```

### 输出

```
🔥 1. GitHub Copilot X 发布新功能
   时间：2026-04-21
   来源：TechCrunch
   链接：https://techcrunch.com/...
   说明：AI编程工具进入工作流集成阶段

🔥 2. AI编程工具使用率年增300%但中小企业采纳率不足20%
   时间：2026-04-21
   来源：Hacker News
   链接：https://news.ycombinator.com/...
   说明：工具爆发与采纳率低的矛盾数据

...（共15个选题）
```
