# tmux 使用指南

tmux 是一个终端复用器（Terminal Multiplexer），允许你在一个终端窗口中管理多个会话、窗口和面板，即使断开连接，后台任务依然持续运行。

---

## 目录

- [基本概念](#基本概念)
- [安装](#安装)
- [会话管理](#会话管理)
- [窗口管理](#窗口管理)
- [面板管理（分屏）](#面板管理分屏)
- [复制模式](#复制模式)
- [配置文件](#配置文件)
- [常用快捷键总览](#常用快捷键总览)
- [实用技巧](#实用技巧)

---

## 基本概念

tmux 的层级结构如下：

```
Server（服务进程）
  └── Session（会话）
        └── Window（窗口）
              └── Pane（面板）
```

- **Session（会话）**：一组窗口的集合，可以在后台持续运行。
- **Window（窗口）**：类似浏览器的标签页，每个窗口占满整个终端。
- **Pane（面板）**：窗口可以分割成多个面板，每个面板独立运行命令。

> 所有快捷键需要先按**前缀键** `Ctrl+b`，松开后再按对应键。

---

## 安装

```bash
# Ubuntu / Debian
sudo apt install tmux

# CentOS / RHEL
sudo yum install tmux

# macOS（使用 Homebrew）
brew install tmux

# 查看版本
tmux -V
```

---

## 会话管理

### 创建会话

```bash
tmux                      # 创建并进入一个匿名会话
tmux new -s 会话名        # 创建并进入一个命名会话
```

### 查看会话

```bash
tmux ls                   # 列出所有会话
tmux list-sessions        # 同上
```

### 进入（恢复）会话

```bash
tmux attach               # 进入最近一次的会话
tmux attach -t 会话名     # 进入指定会话
tmux a -t 会话名          # 简写
```

### 离开会话（后台保留）

```bash
# 在 tmux 内按快捷键：
Ctrl+b → d               # detach，会话在后台继续运行
```

### 删除会话

```bash
tmux kill-session -t 会话名    # 删除指定会话
tmux kill-server               # 删除所有会话
```

### 在会话内切换

| 快捷键 | 说明 |
|--------|------|
| `Ctrl+b` → `s` | 列出并切换会话（交互式） |
| `Ctrl+b` → `$` | 重命名当前会话 |

---

## 窗口管理

窗口类似于浏览器标签页，每个窗口可以运行不同的任务。

| 快捷键 | 说明 |
|--------|------|
| `Ctrl+b` → `c` | 创建新窗口 |
| `Ctrl+b` → `n` | 切换到下一个窗口 |
| `Ctrl+b` → `p` | 切换到上一个窗口 |
| `Ctrl+b` → `0~9` | 切换到对应编号的窗口 |
| `Ctrl+b` → `w` | 列出所有窗口（交互式选择） |
| `Ctrl+b` → `,` | 重命名当前窗口 |
| `Ctrl+b` → `&` | 关闭当前窗口（需确认） |
| `Ctrl+b` → `f` | 按名称搜索窗口 |

---

## 面板管理（分屏）

面板允许你在同一个窗口内同时查看多个终端。

### 创建面板

| 快捷键 | 说明 |
|--------|------|
| `Ctrl+b` → `%` | 左右分屏（垂直分割） |
| `Ctrl+b` → `"` | 上下分屏（水平分割） |

### 切换面板

| 快捷键 | 说明 |
|--------|------|
| `Ctrl+b` → `方向键` | 切换到指定方向的面板 |
| `Ctrl+b` → `o` | 按顺序切换到下一个面板 |
| `Ctrl+b` → `q` | 显示面板编号（按编号跳转） |

### 调整面板大小

| 快捷键 | 说明 |
|--------|------|
| `Ctrl+b` → `Ctrl+方向键` | 按方向调整面板大小（每次1格） |
| `Ctrl+b` → `Alt+方向键` | 按方向调整面板大小（每次5格） |
| `Ctrl+b` → `z` | 当前面板全屏/恢复（zoom） |

### 其他面板操作

| 快捷键 | 说明 |
|--------|------|
| `Ctrl+b` → `x` | 关闭当前面板（需确认） |
| `Ctrl+b` → `{` | 将当前面板与上一个交换位置 |
| `Ctrl+b` → `}` | 将当前面板与下一个交换位置 |
| `Ctrl+b` → `Space` | 循环切换面板布局 |

---

## 复制模式

复制模式允许你滚动查看历史输出并复制文本。

```
Ctrl+b → [       进入复制模式
方向键 / PgUp    滚动浏览历史
Space            开始选择文本（vi 模式）
Enter            复制选中文本并退出
Ctrl+b → ]       粘贴复制的文本
q                退出复制模式
```

> 提示：在 `~/.tmux.conf` 中添加 `setw -g mode-keys vi` 可启用 vi 风格的复制操作。

---

## 配置文件

tmux 的配置文件位于 `~/.tmux.conf`，修改后执行以下命令生效：

```bash
tmux source-file ~/.tmux.conf
# 或在 tmux 内按：Ctrl+b → : 输入 source-file ~/.tmux.conf
```

### 常用配置示例

```bash
# 修改前缀键为 Ctrl+a（更符合习惯）
unbind C-b
set -g prefix C-a
bind C-a send-prefix

# 启用鼠标支持（滚轮、点击切换面板）
set -g mouse on

# 设置窗口编号从 1 开始（默认从 0 开始）
set -g base-index 1
setw -g pane-base-index 1

# 使用 vi 模式
setw -g mode-keys vi

# 分屏快捷键更直观
bind | split-window -h    # Ctrl+b → | 左右分屏
bind - split-window -v    # Ctrl+b → - 上下分屏

# 增加历史记录行数
set -g history-limit 10000

# 状态栏美化
set -g status-bg colour235
set -g status-fg colour136
```

---

## 常用快捷键总览

| 分类 | 快捷键 | 说明 |
|------|--------|------|
| **会话** | `Ctrl+b` → `d` | 离开会话（后台保留） |
| | `Ctrl+b` → `s` | 列出并切换会话 |
| | `Ctrl+b` → `$` | 重命名会话 |
| **窗口** | `Ctrl+b` → `c` | 新建窗口 |
| | `Ctrl+b` → `n` / `p` | 下一个/上一个窗口 |
| | `Ctrl+b` → `w` | 窗口列表 |
| | `Ctrl+b` → `,` | 重命名窗口 |
| | `Ctrl+b` → `&` | 关闭窗口 |
| **面板** | `Ctrl+b` → `%` | 左右分屏 |
| | `Ctrl+b` → `"` | 上下分屏 |
| | `Ctrl+b` → `方向键` | 切换面板 |
| | `Ctrl+b` → `z` | 面板全屏/恢复 |
| | `Ctrl+b` → `x` | 关闭面板 |
| **其他** | `Ctrl+b` → `[` | 进入复制/滚动模式 |
| | `Ctrl+b` → `?` | 查看所有快捷键帮助 |
| | `Ctrl+b` → `:` | 进入命令模式 |

---

## 实用技巧

### 1. 典型工作流

```bash
# 开始工作
tmux new -s myproject

# 分屏：左边写代码，右边看日志
Ctrl+b → %

# 离开（任务继续在后台运行）
Ctrl+b → d

# 第二天回来继续
tmux attach -t myproject
```

### 2. 在脚本中使用 tmux

```bash
# 在后台会话中运行命令
tmux new-session -d -s background "python3 script.py"

# 向指定会话发送命令
tmux send-keys -t 会话名 "ls -la" Enter
```

### 3. 多窗口开发环境一键启动

```bash
#!/bin/bash
tmux new-session -d -s dev -n editor
tmux send-keys -t dev:editor "vim ." Enter

tmux new-window -t dev -n server
tmux send-keys -t dev:server "npm run dev" Enter

tmux new-window -t dev -n git
tmux attach -t dev
```

### 4. 查看所有快捷键

在 tmux 内按 `Ctrl+b` → `?` 可以查看完整的快捷键列表，按 `q` 退出。
