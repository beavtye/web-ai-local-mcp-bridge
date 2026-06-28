# Web AI Local MCP Bridge

让网页端 AI 访问本地文件的开源模板。

English summary: A Windows-first template for connecting web ChatGPT, Gemini, DeepSeek, and similar browser-based AI tools to local files through MCP-compatible bridges.

目标：

- ChatGPT 网页端通过 DevSpace Local / MCP 访问本地工作区。
- Gemini、DeepSeek 等网页端通过 MCP SuperAssistant 访问本地文件。
- 可选：用一个终端中控网页 GPT / Gemini / DeepSeek / Zhipu 等网页模型，并组织成 agent 工作流。

本仓库只提供通用模板，不包含任何个人路径、账号、域名、token 或本地数据。

## 适合谁

- 想让网页 ChatGPT 读取本地项目文件。
- 想让 Gemini / DeepSeek 网页端通过 MCP SuperAssistant 读取本地文件。
- 想把网页 AI 协作流程沉淀成可复用目录。

## 前置条件

必需：

- Windows 10/11
- PowerShell
- Node.js 20 或更高版本
- 一个本地 MCP 文件系统服务
- ChatGPT 支持自定义 MCP / Developer Mode / DevSpace 类型连接

按需要安装：

- DevSpace Local，给 ChatGPT 网页端使用
- MCP SuperAssistant 浏览器扩展，给 Gemini / DeepSeek 网页端使用
- Cloudflare Tunnel 或固定域名隧道，给 ChatGPT 从公网访问本地 DevSpace
- Playwright，如果需要终端中控网页

## 基本目录结构

```text
workspace/
  START.ps1
  config.example.json
  docs/
    chatgpt-devspace.md
    gemini-mcp-superassistant.md
    fixed-domain.md
    troubleshooting.md
  scripts/
    start-gemini-backend.example.ps1
    start-chatgpt-devspace.example.ps1
    start-agent-console.example.ps1
```

## 快速理解

ChatGPT 网页端和 Gemini 网页端不是同一种接法。

ChatGPT：

```text
ChatGPT 网页 -> 公网 HTTPS MCP URL -> 本地 DevSpace -> 本地文件
```

Gemini / DeepSeek：

```text
网页模型 -> MCP SuperAssistant 扩展 -> 本地 SSE MCP 服务 -> 本地文件
```

MCP SuperAssistant 通常不是“模型原生工具”。它会让模型输出工具调用格式，然后由浏览器扩展执行，再把结果插回网页对话。

## 安全提醒

不要把这些内容提交到 Git：

- 本机绝对路径
- 个人用户名
- 微信、QQ、浏览器缓存路径
- ngrok token
- Cloudflare token
- DevSpace 私有配置
- 真实项目敏感数据
- 临时公网地址如果能暴露你的机器，也不建议提交

请复制：

```text
config.example.json -> config.local.json
```

然后在 `config.local.json` 里写自己的路径。`config.local.json` 默认应被 `.gitignore` 忽略。

## 推荐流程

1. 配置允许访问的本地目录。
2. 启动 Gemini / DeepSeek 本地 MCP 后端。
3. 在 MCP SuperAssistant 中连接：

```text
http://127.0.0.1:3006/sse
```

4. 启动 ChatGPT DevSpace 服务。
5. 把终端显示的公网 MCP URL 填到 ChatGPT。
6. 如果需要长期稳定，使用自己的域名配置固定 HTTPS 地址。

## 文档

- [ChatGPT + DevSpace](docs/chatgpt-devspace.md)
- [Gemini / DeepSeek + MCP SuperAssistant](docs/gemini-mcp-superassistant.md)
- [固定域名方案](docs/fixed-domain.md)
- [终端 Agent 中控](docs/agent-console-workflows.md)
- [排错](docs/troubleshooting.md)
- [隐私检查清单](docs/privacy-checklist.md)

## License

MIT
