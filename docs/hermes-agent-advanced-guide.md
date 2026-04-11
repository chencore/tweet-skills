# Hermes Agent 高级玩法完全指南

> 整理自官方文档、爱范儿、ByteIota、DEV Community、知乎等来源  
> 更新日期：2026-04-11

---

## 目录

1. [Hermes Agent 是什么](#1-hermes-agent-是什么)
2. [核心架构：学习循环](#2-核心架构学习循环)
3. [四层记忆系统](#3-四层记忆系统)
4. [技能系统深度解析](#4-技能系统深度解析)
5. [多模型编排](#5-多模型编排)
6. [多平台网关](#6-多平台网关)
7. [安全与生产就绪](#7-安全与生产就绪)
8. [高级配置](#8-高级配置)
9. [从 OpenClaw 迁移](#9-从-openclaw-迁移)
10. [高级使用场景](#10-高级使用场景)
11. [与 OpenClaw 对比](#11-与-openclaw-对比)
12. [参考资源](#12-参考资源)

---

## 1. Hermes Agent 是什么

Hermes Agent 是 **Nous Research** 团队开发的开源 AI Agent 框架（MIT 协议），GitHub 星标已超过 **5.5 万**。

与普通聊天机器人不同，它的核心理念是 **"the agent that grows with you"**——一个会随着使用不断进化的 Agent。

**关键特性：**

- 内置闭环学习系统（Learning Loop）
- 跨会话持久记忆
- 自动技能创建与改进
- 47+ 内置工具
- 15+ 消息平台支持
- 6 种终端后端（本地、Docker、SSH、Daytona、Singularity、Modal）
- 支持 200+ 模型（通过 OpenRouter）
- MCP 协议支持

**安装：**

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

**配置模型：**

```bash
hermes model   # 交互式选择模型提供商
hermes         # 启动终端 UI
```

---

## 2. 核心架构：学习循环

这是 Hermes 区别于所有其他 Agent 框架的核心能力。

### 学习循环的四个阶段

```
Observe（观察） → Plan（规划） → Act（执行） → Learn（学习）
```

### 自动技能创建触发条件

每次任务完成后，Hermes 自动检查是否值得记录。触发条件：

- 工具调用超过 **5 次**
- 中途出错后**自我修复**了
- 用户做了**纠正**
- 走了一条**不明显但有效**的路径

满足任意一条，就会在 `~/.hermes/skills/` 目录下自动生成一个 Skill 文件。

### 技能自我改进

技能文件不是一次写死。后续使用中发现更好路径时，Hermes 会用 **patch（补丁）** 方式修改：

- 只传入旧字符串和替换内容，不整体重写
- 全量覆写容易破坏已有的好部分
- patch 更安全，token 消耗也更少

### 周期性微调（Periodic Nudge）

在没有用户输入的情况下，系统定期自动向 Agent 发一条内部提示，要求回顾最近操作，判断哪些值得写入记忆。**完全无需用户触发**，Agent 自己决定保留什么。

---

## 3. 四层记忆系统

Hermes 的记忆不是简单的文件读写，而是精心设计的四层架构。

### 第一层：常驻提示记忆

- **MEMORY.md**（2,200 字符上限）— 环境信息、约定、经验教训
- **USER.md**（1,375 字符上限）— 用户偏好、沟通风格
- 总字符上限 3,575，**故意收窄**，强迫筛选
- 每次会话自动注入系统提示

### 第二层：会话归档

- 所有对话写入 **SQLite 数据库**（WAL + FTS5 全文索引）
- 需要历史上下文时主动发起查询
- 检索结果经 **LLM 摘要**，只注入与当前任务相关的部分
- 搜索延迟约 **10ms**，支持 10,000+ 技能检索

### 第三层：技能文件

- 学习循环的产出，存储在 `~/.hermes/skills/`
- 默认只加载名称和描述（约 3,000 tokens）
- 按需调入完整内容
- **技能库从 40 增长到 200，上下文成本几乎不变**

### 第四层：Honcho 用户建模

- 可选的跨会话用户画像层
- 被动积累偏好、沟通风格、领域知识
- 适合长期使用的个人助理场景

### 可插拔记忆后端（v0.7.0+）

```yaml
# config.yaml
memory:
  provider: honcho    # 或 sqlite、vector、custom
  honcho:
    api_key: ${HONCHO_API_KEY}
    profile_scope: true
  sqlite:
    db_path: ~/.hermes/memory.db
    fts5_enabled: true
```

---

## 4. 技能系统深度解析

### 渐进式加载（Progressive Disclosure）

| 层级 | 内容 | Token 开销 |
|------|------|-----------|
| Level 0 | 技能名称 + 简短描述列表 | ~3,000 tokens |
| Level 1 | 加载某个技能的完整 SKILL.md | 按需 |
| Level 2 | 加载技能内的特定参考文件 | 按需 |

### 技能文件结构

```
~/.hermes/skills/my-skill/
├── SKILL.md          # 主文件（YAML 前置 + Markdown）
├── references/       # 参考文件
├── templates/        # 模板
└── scripts/          # 脚本
```

### 技能格式（agentskills.io 开放标准）

```markdown
---
name: my-skill
description: 简短描述
triggers:
  - "触发词1"
  - "触发词2"
---

# 技能标题

## 步骤
1. ...
2. ...

## 注意事项
- ...

## 验证
- ...
```

### 技能获取方式

1. **自动生成** — 学习循环自动创建
2. **手写** — 按格式自己编写
3. **Skills Hub 安装** — 社区共享平台
4. **团队共享** — 通过外部技能目录

### 内置技能类别（20+）

Karpathy 的 LLM Wiki 笔记大法已被加入内置技能。

---

## 5. 多模型编排

### 主模型 + 辅助模型

Hermes 的 Auxiliary Models 模块自动将不同任务分配给不同模型：

| 任务类型 | 默认模型 |
|----------|---------|
| 主对话 | 用户选择的主模型 |
| 图像分析 | Gemini Flash |
| 网页提取 | Gemini Flash |
| Skill 匹配 | Gemini Flash |
| 记忆处理 | Gemini Flash |

### 模型切换

```bash
hermes model   # 一键切换，无需改代码
```

支持的模型提供商：

- **Nous Portal** — 订阅制，零配置
- **Anthropic** — Claude（API key 或 Claude Code 授权）
- **OpenRouter** — 200+ 模型路由
- **OpenAI** — Codex 模型
- **DeepSeek**
- **阿里云 DashScope**（Qwen 系列）
- **小米 MiMo-V2** — 限免至 2026-04-22
- **Hugging Face**
- **GitHub Copilot**
- **自定义端点** — Ollama、vLLM、SGLang 等

### 凭证轮换

```yaml
providers:
  openai:
    credentials:
      - api_key: ${OPENAI_KEY_1}
      - api_key: ${OPENAI_KEY_2}
    rotation: least_used   # 自动故障转移
```

---

## 6. 多平台网关

### 支持平台（15+）

Telegram、Discord、Slack、WhatsApp、Signal、Matrix、Mattermost、Email、SMS、钉钉、飞书、企业微信、BlueBubbles、Home Assistant、CLI

### 网关配置

```bash
hermes gateway setup          # 配置网关
hermes gateway telegram --token BOT_TOKEN
hermes gateway discord --token DISCORD_TOKEN
hermes gateway slack --webhook SLACK_WEBHOOK
hermes gateway start          # 启动网关
```

### 跨平台上下文同步

同一 Agent 实例在所有平台共享：
- 会话历史
- 技能库
- 记忆系统
- 审批流程

在终端开始调试，通勤时用 Telegram 查看进度，到公司用 Slack 接收结果——上下文完整保持。

### 平台特性

- **Discord** — 按钮式审批，减少操作摩擦
- **Telegram** — Slash 命令分页浏览技能
- **Slack** — 线程回复支持

---

## 7. 安全与生产就绪

### v0.7.0 安全加固（2026-04-03）

1. **凭证池轮换** — `least_used` 策略 + 自动 401 故障转移
2. **密钥泄露拦截** — 扫描浏览器 URL、LLM 响应中的凭证（base64/URL编码/prompt注入）
3. **沙箱输出脱敏** — 防止代码执行泄露凭证
4. **受保护目录** — 阻止文件工具访问 `.docker/`、`.azure/`、`.config/gh/`
5. **路径遍历防护** — profile 导入的 zip-slip 攻击保护

### 终端后端

| 后端 | 沙箱 | 持久化 | 适用场景 |
|------|------|--------|---------|
| local | ❌ | ✅ | 开发测试 |
| Docker | ✅ | ✅ | 生产部署 |
| SSH | ✅ | ✅ | 远程服务器 |
| Daytona | ✅ | ✅ | Serverless |
| Singularity | ✅ | ✅ | HPC 环境 |
| Modal | ✅ | ✅ | Serverless |

Daytona 和 Modal 支持 **serverless 持久化** — 空闲时休眠，几乎不花钱。

---

## 8. 高级配置

### 配置文件位置

```
~/.hermes/config.yaml
```

### 关键配置项

```yaml
# 模型配置
model:
  provider: anthropic
  model: claude-sonnet-4-20250514

# 记忆配置
memory:
  provider: sqlite
  sqlite:
    db_path: ~/.hermes/memory.db
    fts5_enabled: true

# 安全配置
security:
  protected_paths:
    - ~/.docker
    - ~/.azure
    - ~/.config/gh

# 辅助模型
auxiliary_models:
  default: gemini-flash
  image_analysis: gemini-flash
  web_extraction: gemini-flash

# MCP 服务器
mcp:
  servers:
    - name: github
      command: npx
      args: ["-y", "@modelcontextprotocol/server-github"]
      env:
        GITHUB_TOKEN: ${GITHUB_TOKEN}
```

### 定时任务（Cron）

```bash
hermes cron add --schedule "0 9 * * *" --task "总结今日 GitHub issues"
hermes cron list
hermes cron start
```

支持将结果投递到任意配置的平台。

### 子代理与并行执行

```bash
# 生成隔离子代理执行并行任务
hermes delegate "分析这个仓库的代码质量" --parallel
```

Programmatic Tool Calling 通过 `execute_code` 将多步流水线压缩为单次推理调用。

---

## 9. 从 OpenClaw 迁移

```bash
# 一键迁移
hermes claw migrate

# 会迁移：
# - OpenClaw Skills → Hermes Skills
# - 记忆文件
# - 配置设置
```

### 迁移注意事项

- Skill 格式兼容 agentskills.io 标准
- MEMORY.md 内容可直接迁移到 Hermes 的 MEMORY.md
- 消息平台配置需要重新设置（但过程类似）
- SOUL.md 可直接复制到 `~/.hermes/SOUL.md`

---

## 10. 高级使用场景

### 场景一：团队代码规范自动学习

1. 第一次让 Hermes 按团队规范重构代码
2. 纠正它的风格偏差
3. Hermes 自动创建「团队代码规范」Skill
4. 后续自动遵循，执行速度提升 2-3x

### 场景二：跨平台运维助手

1. 在 VPS 上部署 Hermes（Docker 模式）
2. 配置 Telegram + Slack 网关
3. 通过 Telegram 发送运维指令
4. 结果自动同步到 Slack 频道
5. 复杂操作自动沉淀为 Skill

### 场景三：研究助手

1. 配置 MCP 连接论文数据库
2. 让 Hermes 批量分析论文
3. 自动积累分析模式为 Skill
4. 利用 Atropos RL 环境优化研究流程

### 场景四：语音交互

```bash
hermes voice   # 启动语音模式
```

支持 CLI、Telegram、Discord、Discord VC 的实时语音交互。

### 场景五：Serverless Agent

```yaml
# Daytona 配置
terminal:
  backend: daytona
  daytona:
    api_key: ${DAYTONA_API_KEY}
    # 空闲时休眠，按使用计费
```

部署在 Modal/Daytona 上，通过手机 Telegram 操控云端 Agent。

---

## 11. 与 OpenClaw 对比

| 维度 | Hermes Agent | OpenClaw |
|------|-------------|----------|
| **核心理念** | 自我进化的单一 Agent | 多 Agent 协作平台 |
| **记忆系统** | 四层架构，自动学习 | 静态文件，需手动维护 |
| **技能系统** | 自动创建 + 自动改进 | 手动编写 + 社区安装 |
| **模型支持** | 200+ 模型，一键切换 | 多模型支持 |
| **部署方式** | 6种后端含 Serverless | 本地 + 节点 |
| **消息平台** | 15+ | 10+ |
| **学习曲线** | 中等（需理解学习循环） | 较低（配置即用） |
| **适用场景** | 重复性任务、长期使用 | 一次性任务、快速部署 |
| **安全** | v0.7.0 企业级安全 | 基础安全 |
| **开源协议** | MIT | MIT |

### 选择建议

- **选 Hermes** — 你有重复性工作流，希望 Agent 越用越懂你
- **选 OpenClaw** — 你需要快速部署、一次性任务、工具覆盖面广
- **两者共存** — 可以将 Hermes 作为高级规划器运行在 OpenClaw 之上

---

## 12. 参考资源

### 官方

- 🏠 官网：https://hermes-agent.nousresearch.com
- 📖 文档：https://hermes-agent.nousresearch.com/docs
- 💻 GitHub：https://github.com/nousresearch/hermes-agent
- 🛒 Skills Hub：https://agentskills.io

### 深度文章

- [爱范儿：取代龙虾的是爱马仕？](https://www.ifanr.com/1661725)
- [ByteIota：Build Self-Improving AI Agents](https://byteiota.com/hermes-agent-tutorial-build-self-improving-ai-agents-2026/)
- [DEV Community：A Self-Improving AI Agent That Runs Anywhere](https://dev.to/arshtechpro/hermes-agent-a-self-improving-ai-agent-that-runs-anywhere-2b7d)
- [知乎：拆解 Hermes Agent 闭环学习系统](https://zhuanlan.zhihu.com/p/2025619437139628484)
- [知乎：Hermes Agent 全面调研解读](https://zhuanlan.zhihu.com/p/2022015752258027715)
- [36氪：两个月4.7万星](https://36kr.com/p/3760771429958403)
- [Virtual Uncle：Complete Guide 2026](https://virtualuncle.com/hermes-agent-complete-guide-2026/)
- [AI.cc：Hermes Agent 2026 Self-Improving Guide](https://www.ai.cc/blogs/hermes-agent-2026-self-improving-open-source-ai-agent-vs-openclaw-guide/)

### 视频

- [Hermes AI Agent: Automate ANYTHING!](https://www.youtube.com/watch?v=YtfROZK1BDM)
- [How to Make Hermes Agent 100X Better in 1 Click](https://www.youtube.com/watch?v=zmSeuS0XjTc)
- [从 OpenClaw 到 Hermes Agent：安装、迁移、配置、实战](https://www.youtube.com/watch?v=SNzEM5ilwJ8)
- [哔哩哔哩：手把手教你零成本搭建 Hermes Agent](https://www.bilibili.com/video/BV1wDSSBKEro/)

---

*本文档基于 2026 年 4 月 11 日公开信息整理，Hermes Agent 当前版本 v0.8.0。*
