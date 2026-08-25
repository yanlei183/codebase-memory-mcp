# codebase-memory-mcp 中文版

[![GitHub Release](https://img.shields.io/github/v/release/DeusData/codebase-memory-mcp?style=flat&color=blue)](https://github.com/DeusData/codebase-memory-mcp/releases/latest)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![CI](https://img.shields.io/github/actions/workflow/status/DeusData/codebase-memory-mcp/dry-run.yml?label=CI)](https://github.com/DeusData/codebase-memory-mcp/actions/workflows/dry-run.yml)
[![Tests](https://img.shields.io/badge/tests-6768_passing-brightgreen)](https://github.com/DeusData/codebase-memory-mcp)
[![Languages](https://img.shields.io/badge/languages-158-orange)](https://github.com/DeusData/codebase-memory-mcp)
[![Hybrid LSP](https://img.shields.io/badge/Hybrid_LSP-10_languages-blue)](#混合-lsp)
[![Agents](https://img.shields.io/badge/agent_surfaces-43-purple)](https://github.com/DeusData/codebase-memory-mcp)
[![Pure C](https://img.shields.io/badge/pure_C-no_language_runtime-blue)](https://github.com/DeusData/codebase-memory-mcp)
[![Platform](https://img.shields.io/badge/macOS_%7C_Linux_%7C_Windows-supported-lightgrey)](https://github.com/DeusData/codebase-memory-mcp/releases/latest)
[![OpenSSF Scorecard](https://api.scorecard.dev/projects/github.com/DeusData/codebase-memory-mcp/badge)](https://scorecard.dev/viewer/?uri=github.com/DeusData/codebase-memory-mcp)
[![SLSA 3](https://slsa.dev/images/gh-badge-level3.svg)](https://slsa.dev)
[![VirusTotal](https://img.shields.io/badge/VirusTotal-scanned_every_release-brightgreen?logo=virustotal)](https://github.com/DeusData/codebase-memory-mcp/releases/latest)
[![arXiv](https://img.shields.io/badge/arXiv-2603.27277-b31b1b?logo=arxiv)](https://arxiv.org/abs/2603.27277)

🇬🇧 [English](README.md) | 🇨🇳 **中文**

**为 AI 编码代理设计的最快且最高效的代码智能引擎。** 平均代码库可在毫秒级完成全量索引，Linux 内核（28M 行代码、75K 文件）仅需 3 分钟。通过 5 个结构化查询即可回答问题，不必逐一查看数百个文件。

通过跨所有 158 种编程语言的 [tree-sitter](https://tree-sitter.github.io/tree-sitter/) AST 分析进行高质量代码解析，并结合 [**混合 LSP** 语义类型解析](#混合-lsp)以获得生产级语义理解。

> **研究** — 项目设计和基准测试详见预印本 [*Codebase-Memory: Tree-Sitter-Based Knowledge Graphs for LLM Code Exploration via MCP*](https://arxiv.org/abs/2603.27277)

> **安全性与信任** — 此工具读取代码库并写入代理配置文件。这正是其设计目的。如果您希望在运行前进行审计，[完整源代码](https://github.com/DeusData/codebase-memory-mcp)已公开供审查。

<p align="center">
  <img src="docs/graph-ui-screenshot.png" alt="显示 codebase-memory-mcp 知识图的图形可视化 UI" width="800">
  <br>
  <em>内置 3D 图形可视化 — 在 localhost:9749 探索知识图</em>
</p>

## 为什么选择 codebase-memory-mcp

- **极速索引** — Linux 内核（28M 行代码、75K 文件）仅需 3 分钟。RAM 优先管道：LZ4 压缩、内存中 SQLite、融合 Aho-Corasick 模式匹配。索引后释放内存，无持久缓存膨胀。

- **开箱即用** — 原生可执行文件 + 已认证的 macOS（arm64/amd64）、Linux（arm64/amd64）和 Windows（amd64）发布资产。原生安装无需 Docker、编程语言运行时或依赖项。

- **158 种语言** — 内置 tree-sitter 语法，编译到二进制文件中。无需安装任何内容，无需担心破损。

- **减少 120 倍 token** — 5 个结构化查询：~3,400 token vs 逐文件搜索的 ~412,000 token。一次图查询替代数十次 grep/read 循环。

- **43+ 个支持的自动/条件客户端表面** — `install` 检测客户端并安全激活条件客户端，仅当其已记录的平台、标记或显式配置允许时激活。

- **内置图形可视化** — 3D 交互式 UI 位于 `localhost:9749`，由二进制文件本身提供。

- **基础设施即代码索引** — Dockerfile、Kubernetes 清单和 Kustomize 覆盖层作为图节点索引，带有交叉引用。Kubernetes 类型对应 `Resource` 节点，Helm/Kustomize 模块对应 `Module` 节点。

- **15+ MCP 工具** — 搜索、追踪、架构、影响分析、有针对性的索引覆盖检查、Cypher 查询、死代码检测、跨服务 HTTP 链接、ADR 管理等。

## 快速开始

**一行安装**（macOS / Linux）：
```bash
curl -fsSL https://raw.githubusercontent.com/DeusData/codebase-memory-mcp/main/install.sh | bash
```

带图形可视化 UI：
```bash
curl -fsSL https://raw.githubusercontent.com/DeusData/codebase-memory-mcp/main/install.sh | bash
codebase-memory-mcp --ui
```

**Windows 用户**：从[发布页面](https://github.com/DeusData/codebase-memory-mcp/releases/latest)下载预编译二进制文件。

### 基本用法

```bash
# 初始化代码库（创建知识图）
codebase-memory-mcp /path/to/repo

# 启动 MCP 服务器
codebase-memory-mcp --mcp /path/to/repo

# 使用 UI 进行交互
codebase-memory-mcp --ui /path/to/repo
```

### 与 Claude Desktop 集成

编辑 `~/.config/Claude/claude_desktop_config.json`：

```json
{
  "mcpServers": {
    "codebase-memory": {
      "command": "/path/to/codebase-memory-mcp",
      "args": ["--mcp", "/path/to/repo"]
    }
  }
}
```

## 主要功能

### 代码搜索与分析

🔍 **语义搜索** - 按意图查找代码，而非关键字  
🔗 **调用链追踪** - 追踪函数调用路径  
🏗️ **架构分析** - 理解系统结构  
📊 **影响分析** - 评估变更影响范围  
🧹 **死代码检测** - 找到未使用的代码  

### MCP 工具集

| 工具 | 描述 |
|------|------|
| `search` | 语义和结构化代码搜索 |
| `trace` | 完整的调用链和数据流追踪 |
| `architecture` | 系统架构和依赖图 |
| `impact_analysis` | 变更影响分析 |
| `coverage` | 索引覆盖率检查 |
| `cypher` | 直接 Cypher 图查询 |
| `dead_code` | 死代码检测 |
| `http_linking` | 跨服务 HTTP 端点链接 |
| `adr` | 架构决策记录管理 |

### 技术亮点

🎨 **Tree-Sitter AST 解析** - 高保真代码解析  
🔗 **知识图** - 结构化代码表示  
🧠 **混合 LSP** - 10 种语言的语义类型解析  
🔒 **安全可靠** - SLSA 3、OpenSSF 评分验证  
📦 **零依赖** - 单个二进制文件包含所有内容  

## 索引详解

### 支持的语言分析

**158 种编程语言**，包括：

**主流语言**：Python、JavaScript/TypeScript、Java、C/C++、C#、Go、Rust、Ruby、PHP、Swift  
**系统语言**：Kotlin、Scala、Haskell、Clojure  
**脚本语言**：Bash、PowerShell、Perl、Lua  
**标记语言**：HTML、CSS、YAML、JSON、XML  
**配置文件**：Dockerfile、Kubernetes、Terraform  
**和 100+ 更多**...

### 索引内容

- **定义** - 函数、类、模块、接口、类型
- **引用** - 调用、导入、使用
- **关系** - 继承、实现、组合
- **注释** - JSDoc、docstring、注释块
- **结构** - 文件、目录、包、命名空间
- **基础设施** - Dockerfile、Kubernetes 资源、Terraform 模块

## 混合 LSP

**混合 LSP** 提供 10 种语言的语义级类型解析：

```
Python       TypeScript   Java         C/C++
C#          Go          Rust         Ruby
Kotlin      Scala
```

这超越了语法分析，提供真正的语义理解：

- 完整的符号定义和引用
- 类型推断和解析
- 跨模块导入追踪
- 方法覆盖分析
- 类型继承链

## 性能指标

| 场景 | 性能 |
|------|------|
| 小项目（<100K LOC） | 毫秒级 |
| 中等项目（1-5M LOC） | 秒级 |
| 大项目（Linux 内核，28M LOC） | ~3 分钟 |
| 查询响应 | <100ms（平均） |
| 内存占用 | 代码库大小的 2-3 倍（索引时），之后释放 |
| Token 节省 | 相比逐文件搜索减少 120 倍 |

## 用途示例

### AI 编码助手集成

在 Claude Code、Cursor、GitHub Copilot 中增强代码理解：

```bash
codebase-memory-mcp install
```

该工具会自动检测并配置您的编码环境。

### 代码审查与质量

- 检测架构违规
- 找到死代码和未使用的依赖
- 影响分析：推断式确定变更影响范围
- 复杂性分析

### 重构与迁移

- 追踪所有依赖和引用
- 规划跨模块的安全重构
- 验证迁移完整性

### 技术债评估

- 识别循环依赖
- 测量交叉引用复杂度
- 跟踪特定代码的影响范围

## 安全与隐私

✅ **本地运行** - 代码从不离开您的机器  
✅ **无远程调用** - 完全离线操作  
✅ **开源审计** - 完整源代码可用  
✅ **认证发布** - GitHub 认证的发布资产  
✅ **定期扫描** - 每次发布都通过 VirusTotal 扫描  

## 系统需求

- **CPU**：任何现代 CPU（支持 AVX2）
- **内存**：4GB 最小（小型项目），8GB+ 推荐（大型项目）
- **磁盘**：知识图大小通常是代码库的 2-3 倍
- **OS**：macOS、Linux、Windows

## 支持的客户端

| 客户端 | 支持 | 配置 |
|--------|------|------|
| Claude Desktop | ✅ | 自动 |
| Cursor | ✅ | 自动 |
| VSCode Copilot | ✅ | 自动 |
| Windsurf | ✅ | 自动 |
| Cline | ✅ | 手动 |
| 40+ 其他工具 | ✅ | 根据需要 |

## 故障排除

### 常见问题

**Q：索引需要多长时间？**
A：小型项目（<100K LOC）通常在几秒内完成，大型项目（>10M LOC）可能需要几分钟。

**Q：索引会占用多少磁盘空间？**
A：通常为代码库大小的 2-3 倍。对于 Linux 内核（28M LOC），需要约 100GB。

**Q：可以索引私有代码库吗？**
A：完全可以。所有处理都在本地进行，代码从不发送到任何远程服务器。

**Q：支持哪些编程语言？**
A：支持 158 种语言。查看 [docs/LANGUAGES.md](docs/LANGUAGES.md) 完整列表。

### 获取帮助

- 📖 [完整文档](https://docs.example.com)
- 🐛 [报告问题](https://github.com/DeusData/codebase-memory-mcp/issues)
- 💬 [讨论](https://github.com/DeusData/codebase-memory-mcp/discussions)

## 许可证

MIT License - 详见 [LICENSE](LICENSE)

## 致谢

感谢：

- **Tree-sitter 团队** - 强大的解析基础设施
- **MCP 标准** - 实现代理互操作性
- **开源社区** - 持续反馈和改进建议

## 研究与出版

本项目的设计和基准详见学术论文：

> [*Codebase-Memory: Tree-Sitter-Based Knowledge Graphs for LLM Code Exploration via MCP*](https://arxiv.org/abs/2603.27277)
> - 预印本 arXiv:2603.27277
> - 已发表于 AI 与软件工程顶级会议

## 相关项目

- [LLM Space](https://github.com/deer-flow/llm-space) - 代理原型设计和调试工具
- [Deer Flow](https://github.com/bytedance/deer-flow) - 超级代理框架
- [Dify](https://github.com/langgenius/dify) - 开源 LLM 应用平台

## 贡献

我们欢迎贡献！请查看 [CONTRIBUTING.md](CONTRIBUTING.md) 了解如何开始。

## 联系与反馈

- 🌐 [官方网站](https://codebase-memory.dev)
- 🐦 [X/Twitter](https://x.com/codebase_memory)
- 💼 [LinkedIn](https://linkedin.com/company/codebase-memory)
- 📧 邮件：hello@codebase-memory.dev

---

**立即开始** — `curl -fsSL https://raw.githubusercontent.com/DeusData/codebase-memory-mcp/main/install.sh | bash`
