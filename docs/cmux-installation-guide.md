# cmux 安装与使用指南

> 基于 Ghostty 的 macOS 终端，专为 AI 编程 Agent 设计  
> GitHub: https://github.com/manaflow-ai/cmux  
> ⭐ 13,600+ Stars | 语言: Swift | 仅 macOS

---

## 目录

1. [cmux 是什么](#cmux-是什么)
2. [核心功能](#核心功能)
3. [安装](#安装)
4. [首次配置](#首次配置)
5. [基础使用](#基础使用)
6. [AI Agent 工作流](#ai-agent-工作流)
7. [内置浏览器](#内置浏览器)
8. [SSH 远程开发](#ssh-远程开发)
9. [快捷键速查](#快捷键速查)
10. [自定义配置](#自定义配置)
11. [常见问题](#常见问题)

---

## cmux 是什么

cmux 是一个**专为 AI 编程 Agent 打造的 macOS 原生终端**。

它基于 Ghostty（当前最快的终端模拟器之一）构建，增加了：

- 📌 **垂直标签页 + 通知系统** — 当 Claude Code、Codex、OpenCode 等 Agent 需要你注意时，窗格自动亮蓝环
- 🌐 **内置浏览器** — 在终端旁分割出浏览器，Agent 可以直接操作
- 👥 **Claude Code Teams 支持** — 一条命令启动多 Agent 协作
- 🔌 **可脚本化** — CLI + Socket API，可以自动化一切

简单说：**tmux + 浏览器 + Agent 通知，原生 macOS 应用，不用 Electron**。

---

## 核心功能

### 1. 通知提示环
当 AI Agent 需要你注意时：
- 窗格边框出现**蓝色光环**
- 侧边栏标签**高亮提示**
- 一键跳转到最新未读通知（⌘⇧U）

### 2. 通知面板
所有 Agent 的通知集中展示，不用一个个翻标签。

### 3. 侧边栏垂直标签
每个标签显示：
- Git 分支名
- 关联的 PR 状态和编号
- 工作目录
- 监听端口
- 最新通知文本

### 4. 内置浏览器
- 从 Chrome、Firefox、Arc 等 20+ 浏览器导入 Cookie
- 浏览器窗格可以和终端并排
- Agent 可通过 API 操控浏览器（截图、点击、填表、执行 JS）

### 5. SSH 远程开发
```bash
cmux ssh user@remote
```
- 自动创建远程工作区
- 浏览器通过远程网络路由（localhost 直接可用）
- 拖拽图片到远程会话自动 scp 上传

### 6. Claude Code Teams
```bash
cmux claude-teams
```
一条命令启动 Claude Code 队友模式，多个 Agent 以原生分割窗格运行。

---

## 安装

### 方式一：DMG（推荐）

1. 下载最新版本：
   https://github.com/manaflow-ai/cmux/releases/latest/download/cmux-macos.dmg

2. 打开 DMG，将 cmux 拖到「应用程序」文件夹

3. 首次启动时，macOS 会提示确认打开。点击**打开**

4. cmux 内置自动更新（Sparkle），只需下载一次

### 方式二：Homebrew

```bash
# 添加 tap
brew tap manaflow-ai/cmux

# 安装
brew install --cask cmux

# 后续更新
brew upgrade --cask cmux
```

### 系统要求

- macOS（仅支持 Mac）
- 不支持 Windows / Linux

---

## 首次配置

### 1. Ghostty 配置兼容

cmux **自动读取**你现有的 Ghostty 配置：

```
~/.config/ghostty/config
```

如果你已经配置过 Ghostty 的主题、字体、颜色，cmux 会直接继承。

### 2. 浏览器数据导入

首次使用内置浏览器时，可以导入：

- Cookie
- 浏览历史
- 登录会话

支持从 Chrome、Firefox、Arc、Safari 等 20+ 浏览器导入。

这样浏览器窗格启动时就已经是登录状态。

### 3. 自定义命令

在项目根目录创建 `cmux.json`：

```json
{
  "commands": [
    {
      "name": "Start Dev Server",
      "action": "npm run dev"
    },
    {
      "name": "Run Tests",
      "action": "npm test"
    },
    {
      "name": "Open Claude Code",
      "action": "claude"
    }
  ]
}
```

通过命令面板（⌘⇧P）快速执行。

---

## 基础使用

### 创建工作区

```
⌘ N          新建工作区
⌘ 1-8        跳转到工作区 1-8
⌘ 9          跳转到最后一个工作区
⌃⌘ ]         下一个工作区
⌃⌘ [         上一个工作区
⌘⇧ W         关闭工作区
⌘⇧ R         重命名工作区
⌘ B          切换侧边栏显示
```

### 管理标签页

```
⌘ T          新建标签页
⌘⇧ ]         下一个标签页
⌘⇧ [         上一个标签页
⌃ Tab        下一个标签页
⌃⇧ Tab       上一个标签页
⌃ 1-8        跳转到标签页 1-8
⌘ W          关闭标签页
```

### 分割窗格

```
⌘ D          右侧分割
⌘⇧ D        下方分割
⌥⌘ ←→↑↓    方向切换窗格焦点
⌘⇧ H        闪烁当前窗格（高亮定位）
```

---

## AI Agent 工作流

### 场景一：运行 Claude Code

```bash
# 在新分割窗格中启动 Claude Code
claude
```

当 Claude Code 需要你输入时：
1. 该窗格自动出现**蓝色光环**
2. 侧边栏标签**高亮**
3. 按 `⌘⇧ U` 跳转到最新未读

### 场景二：运行多个 Codex 会话

```bash
# 左侧窗格
codex --task "实现用户登录功能"

# 右侧分割（⌘ D）
codex --task "编写单元测试"
```

两个 Agent 并行运行，通知系统帮你追踪哪个需要回应。

### 场景三：Claude Code Teams

```bash
cmux claude-teams
```

自动启动 Claude Code 队友模式：
- 每个 Agent 作为原生分割窗格运行
- 侧边栏显示每个 Agent 的元数据和状态
- 无需 tmux

### 自定义 Agent 通知

通过 CLI 发送通知：

```bash
# 发送通知（可写入 Agent hooks）
cmux notify "构建完成，3个测试失败"

# 查看 Agent hooks 配置
# 在 Claude Code 的 settings.json 中配置
```

---

## 内置浏览器

### 打开浏览器

```
⌘⇧ L         在分割窗格中打开浏览器
⌘ L          聚焦地址栏
⌘ [          后退
⌘ ]          前进
⌘ R          刷新
⌥⌘ I        开发者工具
⌥⌘ C        JavaScript 控制台
```

### 可脚本化 API

浏览器窗格支持来自 agent-browser 的 API：

- 截取无障碍树快照
- 获取元素引用
- 点击元素
- 填写表单
- 执行 JavaScript

这意味着 Agent 可以直接在浏览器中交互，不需要外部工具。

---

## SSH 远程开发

### 基本用法

```bash
cmux ssh user@your-server
```

自动创建远程工作区，浏览器窗格通过远程网络路由。

### 拖拽上传

直接将文件/图片拖入远程会话窗口，自动通过 scp 上传。

### localhost 直接可用

因为浏览器通过远程网络路由，所以远程服务器上的 localhost:3000 可以直接在浏览器窗格中访问。

---

## 快捷键速查

| 功能 | 快捷键 |
|------|--------|
| 新建工作区 | ⌘ N |
| 切换侧边栏 | ⌘ B |
| 新建标签页 | ⌘ T |
| 右侧分割 | ⌘ D |
| 下方分割 | ⌘⇧ D |
| 方向切换窗格 | ⌥⌘ ←→↑↓ |
| 打开浏览器 | ⌘⇧ L |
| 显示通知面板 | ⌘ I |
| 跳转最新未读 | ⌘⇧ U |
| 关闭标签页 | ⌘ W |
| 关闭工作区 | ⌘⇧ W |

---

## 自定义配置

### cmux.json（项目级配置）

在项目根目录创建 `cmux.json`：

```json
{
  "commands": [
    {
      "name": "Start Dev",
      "action": "npm run dev"
    },
    {
      "name": "Run Agent",
      "action": "claude --task '$(pbpaste)'"
    }
  ]
}
```

通过命令面板（⌘⇧P）执行。

### Ghostty 配置继承

cmux 读取 `~/.config/ghostty/config`：

```
# 示例 Ghostty 配置
theme = catppuccin-mocha
font-size = 14
font-family = JetBrains Mono
background-opacity = 0.95
```

cmux 会自动应用这些设置。

### CLI + Socket API

```bash
# 创建工作区
cmux workspace create "My Project"

# 分割窗格
cmux split --right

# 发送按键
cmux send-keys "claude" Enter

# 打开 URL
cmux browser open http://localhost:3000

# 发送通知
cmux notify "任务完成"
```

---

## 常见问题

### Q: cmux 支持 Windows/Linux 吗？
**不支持。** cmux 是原生 macOS 应用（Swift/AppKit），目前只有 Mac 版本。

### Q: 需要先安装 Ghostty 吗？
**不需要。** cmux 内置了 libghostty，独立运行。但如果你有 Ghostty 配置文件，cmux 会自动读取。

### Q: 和 tmux 有什么区别？
cmux 不需要 tmux。它提供了原生 GUI 的标签页、分割、通知等功能，性能更好，体验更接近原生 macOS 应用。

### Q: 支持哪些 AI Agent？
- Claude Code ✅（深度集成，支持 Teams 模式）
- Codex ✅
- OpenCode ✅
- Gemini CLI ✅
- AMP ✅
- 任何终端中运行的 AI Agent ✅

### Q: 浏览器数据安全吗？
浏览器数据（Cookie、历史记录）只存储在本地，不会发送到任何服务器。

### Q: 如何更新？
DMG 安装的用户通过 Sparkle 自动更新。Homebrew 用户运行 `brew upgrade --cask cmux`。

---

## 参考链接

- 🏠 官网：https://cmux.com
- 📖 文档：https://cmux.com/docs/getting-started
- 💻 GitHub：https://github.com/manaflow-ai/cmux
- 🐦 Twitter：https://x.com/manaflowai
- 💬 Discord：https://discord.gg/xsgFEVrWCZ
- 🎬 演示视频：https://www.youtube.com/watch?v=i-WxO5YUTOs

---

*基于 2026-04-12 公开信息整理，cmux 当前版本 13,600+ Stars*
