---
name: hot-topics
description: |
  采集多平台热点选题，筛选AI/科技/加密货币/创业相关内容，整理成结构化选题列表。
  触发词：「采集热点」「找选题」「热点」「今日选题」「hot topics」。
---

# 热点选题采集

## 采集源（按优先级）

### 英文
1. TechCrunch - 科技
2. The Verge - 科技/AI
3. Hacker News - 技术/创业
4. CoinDesk - 加密货币
5. Cointelegraph - 区块链

### 中文
1. 36氪 (36kr.com) - 科技/创业
2. 知乎热榜 - 综合
3. 今日头条 - 热点

## 执行步骤

1. 逐个抓取信源（用 web_fetch）
2. 筛选 AI/加密货币/科技/创业 相关内容
3. 过滤：只保留当日新闻，超过24小时的不采用
4. 整理 10-20 个选题，按热度排序

## 输出格式

每个选题包含 5 项：
1. **标题** - 选题名称
2. **采集时间** - YYYY-MM-DD
3. **来源** - 媒体名称
4. **来源链接** - 原始URL
5. **简单描述** - 一句话说明（20-50字）

## 发送格式

```
🔥 1. [标题]
- 时间：YYYY-MM-DD
- 来源：XXX
- 链接：URL
- 说明：一句话描述
```

## 保存
保存到：`~/.openclaw/workspace-self_media/topics/hot-topics-YYYY-MM-DD.md`
