# Graphify 知识图谱输出目录

此目录包含思源笔记（SiYuan）项目的代码知识图谱分析结果，由 [Graphify](https://github.com/safishamsi/graphify) 工具生成。

---

## 目录说明

| 文件 | 说明 |
|------|------|
| `GRAPH_REPORT.md` | 完整的知识图谱分析报告，包含架构概述、实体清单、依赖关系等 |
| `graph.json` | 结构化图谱数据，可用于进一步分析或自定义可视化 |
| `graph.html` | 交互式可视化图谱，在浏览器中打开即可浏览 |

---

## 如何使用图谱工具

### 安装 Graphify

```bash
# 使用 uv (推荐)
uv tool install graphifyy && graphify install

# 或使用 pipx
pipx install graphifyy && graphify install

# 或使用 pip
pip install graphifyy && graphify install
```

> **注意**: PyPI 包名为 `graphifyy`（两个 y），但 CLI 命令是 `graphify`。

### 基本用法

```bash
# 分析当前目录
cd /path/to/your/project
graphify .

# 深度分析模式（更详细的提取）
graphify . --mode deep

# 增量更新（只重新提取变化的文件）
graphify . --update

# 查询知识图谱
graphify query "块级编辑器是如何实现的？"

# 查找两个实体之间的路径
graphify path "Block" "Database"

# 解释概念
graphify explain "Kernel"
```

### 排除文件

创建 `.graphifyignore` 文件来排除不需要分析的文件：

```
node_modules/
*.log
*.min.js
dist/
```

---

## 分析结果说明

### 统计概览

- **总文件数**: 1,273 个
- **代码文件**: 674 个
- **文档文件**: 417 个
- **总词数**: ~250 万词
- **图谱节点**: 25,107 个
- **图谱边数**: 59,306 条
- **社区数量**: 339 个

### 核心架构

```
┌─────────────────────────────────────────────────────────┐
│              Frontend (TypeScript + Electron)            │
│              Desktop / Web / Mobile                      │
├─────────────────────────────────────────────────────────┤
│ - Block-level editor                                     │
│ - Bidirectional linking & Graph view                     │
│ - Local-first data storage                               │
│ - Plugin system                                          │
└────────────────────────┬────────────────────────────────┘
                         │
┌────────────────────────▼────────────────────────────────┐
│              Kernel (Go)                                 │
│              Core backend service                        │
├─────────────────────────────────────────────────────────┤
│ - Block tree management                                  │
│ - File system sync                                       │
│ - Database abstraction                                   │
│ - WebSocket / HTTP API                                   │
└────────────────────────┬────────────────────────────────┘
                         │
┌────────────────────────▼────────────────────────────────┐
│         Storage Layer                                    │
├─────────────────────────────────────────────────────────┤
│ - Local Markdown files                                   │
│ - SQLite (metadata & index)                              │
│ - Assets (images, attachments)                           │
└─────────────────────────────────────────────────────────┘
```

### 技术栈

| 层级 | 技术 |
|------|------|
| 前端 | TypeScript + Electron + ProseMirror |
| 内核 | Go 1.20+ |
| 数据库 | SQLite + 本地文件系统 |
| 同步 | WebDAV / S3 / 自建同步 |
| 编辑器 | ProseMirror (块级编辑器) |
| 插件 | JavaScript plugin API |

### 核心概念

| 概念 | 说明 |
|------|------|
| **Block (块)** | 内容的基本单位，段落、标题、列表等都是块 |
| **Node (节点)** | 块的内部表示，包含类型、属性、内容 |
| **Kernel (内核)** | Go 编写的后端服务，处理数据持久化 |
| **Notebook (笔记本)** | 顶级容器，对应一个文件夹 |
| **Attribute View** | 属性视图，类似数据库表格视图 |
| **Backlink (双向链接)** | 块与块之间的引用关系 |

---

## 浏览交互式图谱

直接在浏览器中打开 `graph.html`：

```bash
# macOS
open graph.html

# Linux
xdg-open graph.html

# Windows
start graph.html
```

### 图谱操作

- **缩放**: 鼠标滚轮或右上角按钮
- **移动**: 拖拽画布
- **查看详情**: 点击节点，详情显示在左侧面板
- **搜索**: 使用左上角搜索框过滤节点
- **筛选**: 点击类型按钮按类别筛选

---

## 核心节点 (God Nodes)

图谱中连接度最高的节点（核心抽象）：

| 排名 | 节点 | 连接数 | 说明 |
|------|------|--------|------|
| 1 | `join()` | 414 | 核心工具函数 |
| 2 | `JsonArg()` | 376 | JSON 参数处理 |
| 3 | `push()` | 324 | 数据推送操作 |
| 4 | `fetchPost()` | 323 | 网络请求 |
| 5 | `hasClosestByClassName()` | 177 | DOM 操作 |

---

## 关键架构连接

- `main()` → `HandleSignal()` - 信号处理流程
- `StartKernelFast()` → `Serve()` - 内核启动到服务
- `changeAttrViewLayout()` → `LayoutType` - 属性视图布局

---

## 集成到 Claude Code

在 Claude Code 中加载报告，让 AI 基于图谱回答问题：

```bash
# 查看分析报告
cat graphify-out/GRAPH_REPORT.md

# 然后在对话中询问
"根据知识图谱，解释一下块级编辑器的实现"
"双向链接是如何工作的？"
```

---

## 与 Open Notebook 的对比

| 特性 | SiYuan (思源笔记) | Open Notebook |
|------|-------------------|---------------|
| 架构 | 本地优先 (Local-first) | 客户端-服务器 |
| 内核 | Go | Python (FastAPI) |
| 前端 | Electron + ProseMirror | Next.js 16 |
| 数据库 | SQLite + 本地文件 | SurrealDB (图数据库) |
| 同步 | 自托管 / WebDAV / S3 | 服务端同步 |
| AI 集成 | 插件扩展 | 内置 LangGraph |
| 部署 | 桌面应用为主 | Web + API |
| 存储格式 | Markdown + JSON | 数据库存储 |

---

## 贡献

此目录由自动化工具生成，不建议手动修改其中的文件。如需更新分析结果，请重新运行 `graphify` 命令。

---

*生成时间: 2026-05-17*  
*工具版本: Graphify v1.0*
