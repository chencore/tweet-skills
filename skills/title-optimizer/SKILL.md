---
name: title-optimizer
description: |
  为推文生成多个方向的标题。5个方向：结果感、冲突感、身份感、场景感、悬念感。
  触发词：「起标题」「优化标题」「标题」。
triggers:
  - 起标题
  - 优化标题
  - 标题
output_schema:
  type: array
  items:
    type: object
    properties:
      direction: { type: string }
      title: { type: string }
      word_count: { type: number }
    required: [direction, title]
tools:
  - web_fetch
---

# 标题优化器

为给定推文生成 5 个标题，覆盖以下方向：

## 5 个方向

| # | 方向 | 思路 | 示例 |
|---|------|------|------|
| 1 | 结果感 | 具体的数字或结果 | "Muse Spark抽象推理只有42.5分" |
| 2 | 冲突感 | 挑战常识或预期 | "Arena第三名的模型，核心能力只有第一名一半" |
| 3 | 身份感 | "如果你是XX" | "做X平台内容的，这个变化你必须知道" |
| 4 | 场景感 | "当XX发生时" | "当所有人都在吹某个模型的时候" |
| 5 | 悬念感 | 不说答案 | "Muse Spark排名第三，但有个数据没人提" |

## 规则

- 每个标题**不超过 20 字**
- 优先具体，不要空泛
- 不用陈词滥调：震惊、重磅、牛逼、炸裂
- 不做标题党，标题必须与内容一致
- 5 个标题必须是**不同方向**，不是同义改写
- 标题党不可作为默认选项

## 输出格式

```json
[
  { "direction": "结果感", "title": "AI编程工具300%增长背后：采纳率只有20%", "word_count": 18 },
  { "direction": "冲突感", "title": "工具爆发不等于效率提升", "word_count": 11 },
  { "direction": "身份感", "title": "如果你在做AI产品，这个数据别忽略", "word_count": 15 },
  { "direction": "场景感", "title": "当所有人都在追新工具的时候", "word_count": 13 },
  { "direction": "悬念感", "title": "Muse Spark排名第三，但有个问题", "word_count": 14 }
]
```

## 示例

### 输入

```
推文核心观点：AI编程工具爆发，但团队采纳率只有20%，工具爆发不等于效率提升
```

### 输出

```json
[
  { "direction": "结果感", "title": "AI编程工具300%增长背后：采纳率只有20%", "word_count": 18 },
  { "direction": "冲突感", "title": "工具爆发不等于效率提升", "word_count": 11 },
  { "direction": "身份感", "title": "做AI产品的，这个数据别忽略", "word_count": 12 },
  { "direction": "场景感", "title": "当所有人都在追新工具的时候", "word_count": 13 },
  { "direction": "悬念感", "title": "工具爆发，但为什么团队不用？", "word_count": 12 }
]
```
