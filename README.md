# MY PI AGENT SETUP

用于在新电脑复现当前 Pi Agent 环境。只恢复工具和非模型设置；账号、模型及密钥需重新配置。

快照日期：`2026-08-19`

## 版本

| 组件 | 版本 |
|---|---:|
| Node.js | `24.18.0` |
| npm | `11.16.0` |
| Git | `2.55.0.windows.3` |
| GitHub CLI | `2.96.0` |
| Pi | `0.84.2` |
| Lark CLI | `1.0.87` |
| Chrome DevTools MCP | `1.7.0` |
| agent-browser | `0.34.0` |

## 安装

先安装 Node.js 24、Git 和 GitHub CLI。Windows 可使用：

```bash
winget install --id Git.Git -e
winget install --id GitHub.cli --version 2.96.0 -e
```

### Pi

```bash
npm install -g --ignore-scripts @earendil-works/pi-coding-agent@0.84.2

pi install npm:pi-web-access@0.24.0
pi install npm:pi-mcp-adapter@2.26.1
pi install npm:pi-subagents@0.51.0
pi install npm:pi-markdown-preview@0.14.1
pi install npm:@dietrichgebert/ponytail@4.9.0
pi install npm:pine-of-glass@0.10.1
pi install npm:@yeungkc/pi-codex-compact@0.0.5
```

### Lark

```bash
npm install -g @larksuite/cli@1.0.87
npx --yes skills@1.5.22 add larksuite/cli@v1.0.87 -g -y
```

该命令安装 27 个 `lark-*` Skills。

### 浏览器工具

```bash
npm install -g chrome-devtools-mcp@1.7.0
npm install -g agent-browser@0.34.0
```

## 配置

### Pi

将以下字段合并到 `~/.pi/agent/settings.json`，不要覆盖 `packages`：

```json
{
  "theme": "dark",
  "defaultProjectTrust": "always",
  "hideThinkingBlock": true
}
```

`defaultProjectTrust: "always"` 会自动信任项目配置。处理不可信仓库时改为 `"ask"`。

### Chrome DevTools MCP

将以下内容合并到 `~/.pi/agent/mcp.json`。先用 `where chrome-devtools-mcp`（Windows）或 `command -v chrome-devtools-mcp`（macOS/Linux）获取绝对路径。

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "<chrome-devtools-mcp 的绝对路径>",
      "args": [
        "--no-usage-statistics",
        "--screenshot-format=webp",
        "--screenshot-quality=80",
        "--screenshot-max-width=1600",
        "--screenshot-max-height=1200"
      ],
      "lifecycle": "lazy"
    }
  }
}
```

## 登录

```bash
gh auth login
lark-cli config init --new
lark-cli auth login --recommend
lark-cli auth status --json --verify
```

启动 Pi 后使用 `/login` 和 `/model` 配置 Provider 与模型。按需重新配置 `pi-web-access` 的搜索服务。

不要复制或提交以下内容：

- `auth.json`、`models.json`、`models-store.json`、`.env`
- API Key、Token、Lark 凭据、浏览器 Cookie
- SSH 私钥、Git 凭据、Pi 会话文件

## 验证

```bash
node --version
npm --version
git --version
gh --version
pi --version
pi list
lark-cli --version
npx --yes skills@1.5.22 list -g --json
chrome-devtools-mcp --version
agent-browser --version
```

预期结果：

- `pi list` 显示 7 个 package。
- Lark Skills 共 27 个；package 自带 Skills 共 8 个。
- `/reload` 后 Web、MCP、Subagents、Markdown Preview、Ponytail、Pine of Glass 和 Lark 能力可用。
- 使用 OpenAI Codex 模型执行 `/compact`，显示 `✓ OpenAI compaction complete`。

升级组件后，同步更新本文件中的版本和安装命令；不要使用 `latest`。
