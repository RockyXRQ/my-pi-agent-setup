# Rocky 的 Pi Agent 环境快照

> 快照日期：2026-08-18  
> 目标：在新电脑上恢复与当前环境相同的 **Pi 扩展、Skills、MCP 和辅助 CLI 能力**；**不复制模型、API Key、OAuth、账号登录态或其他密钥**。

## 给新电脑上的 Agent

如果用户让你读取本仓库并恢复环境：

1. 完整阅读本 README。
2. 检查操作系统、Node.js、npm、Git 和现有 Pi，缺什么再安装；不要无条件重复安装。
3. 严格使用下文固定版本，依次安装 Pi、Pi packages、辅助 CLI、Lark Skills，并配置 Chrome DevTools MCP。
4. 将“非模型设置”合并进 `~/.pi/agent/settings.json`，不要覆盖已安装 package 列表。
5. **禁止复制或提交** `auth.json`、`models.json`、`models-store.json`、`.env`、访问令牌、API Key、Lark 凭据和浏览器 Cookie。
6. 需要账号能力时，让用户在新电脑上重新登录或授权。
7. 执行最后的验收命令，并向用户报告版本差异、未完成的登录项和剩余风险。

---

## 1. 基础环境

当前快照：

| 项目 | 版本 | 安装命令 / 说明 | GitHub |
|---|---:|---|---|
| Node.js | `24.18.0` | 建议安装 Node.js 24；必须满足下方工具的 engine 要求 | [nodejs/node](https://github.com/nodejs/node) |
| npm | `11.16.0` | 随 Node.js 安装 | [npm/cli](https://github.com/npm/cli) |
| Git | `2.55.0.windows.3` | Windows 可用 `winget install --id Git.Git -e` | [git-for-windows/git](https://github.com/git-for-windows/git) |
| GitHub CLI | `2.96.0` | Windows：`winget install --id GitHub.cli --version 2.96.0 -e`；安装后执行 `gh auth login` | [cli/cli](https://github.com/cli/cli) |

Node.js 版本不是 Pi 配置，但当前的 `agent-browser@0.34.0` 要求 Node.js `>=24`，所以恢复时直接使用 Node.js 24 最省事。

## 2. 安装原生 Pi

当前版本：`@earendil-works/pi-coding-agent@0.84.2`

```bash
npm install -g --ignore-scripts @earendil-works/pi-coding-agent@0.84.2
pi --version
```

预期输出：

```text
0.84.2
```

仓库：[earendil-works/pi](https://github.com/earendil-works/pi)

> 模型和 Provider 不在本仓库中恢复。安装结束后由用户运行 `pi`，再通过 `/login` 和 `/model` 单独配置。

## 3. 安装 Pi 扩展包

下面 7 个 package 是 `pi list` 的完整快照。固定版本安装可避免新电脑拿到不兼容的新版本。

```bash
pi install npm:pi-web-access@0.23.0
pi install npm:pi-mcp-adapter@2.26.0
pi install npm:pi-subagents@0.50.0
pi install npm:@narumitw/pi-goal@0.52.0
pi install npm:pi-markdown-preview@0.14.1
pi install npm:@dietrichgebert/ponytail@4.9.0
pi install npm:pine-of-glass@0.10.1
```

| Package | 版本 | 主要能力 | GitHub |
|---|---:|---|---|
| `pi-web-access` | `0.23.0` | `web_search`、`source_check`、`fetch_content`、`get_search_content`；网页、GitHub、PDF、YouTube/视频访问 | [nicobailon/pi-web-access](https://github.com/nicobailon/pi-web-access) |
| `pi-mcp-adapter` | `2.26.0` | MCP server 接入、`mcp`、`mcpScript`；附带 `mcp-scripting` Skill | [nicobailon/pi-mcp-adapter](https://github.com/nicobailon/pi-mcp-adapter) |
| `pi-subagents` | `0.50.0` | 单/多子 Agent、并行工作流、后台运行、missions/schedules、review 流程；附带 Skill 和 prompts | [nicobailon/pi-subagents](https://github.com/nicobailon/pi-subagents) |
| `@narumitw/pi-goal` | `0.52.0` | 自主单目标 `/goal` 模式 | [narumiruna/pi-extensions](https://github.com/narumiruna/pi-extensions/tree/main/packages/pi-goal) |
| `pi-markdown-preview` | `0.14.1` | Markdown/LaTeX 的终端、浏览器、PDF/HTML/PNG 预览与 `preview_export` | [omaclaren/pi-markdown-preview](https://github.com/omaclaren/pi-markdown-preview) |
| `@dietrichgebert/ponytail` | `4.9.0` | 最小实现/YAGNI 编码模式及审计、review、debt、gain、help Skills | [DietrichGebert/ponytail](https://github.com/DietrichGebert/ponytail) |
| `pine-of-glass` | `0.10.1` | Pi 可观测性和上下文管理：Contextimate、Traceline、Cachemire、Meantime | [tmustier/pine-of-glass](https://github.com/tmustier/pine-of-glass) |

### 包内自带的 Skills

这些 Skills 会随上面的 package 自动安装，不要重复复制：

| 来源 | Skill | 版本 |
|---|---|---:|
| `pi-mcp-adapter` | `mcp-scripting` | 跟随 package `2.26.0` |
| `pi-subagents` | `pi-subagents` | 跟随 package `0.50.0` |
| `@dietrichgebert/ponytail` | `ponytail`、`ponytail-audit`、`ponytail-debt`、`ponytail-gain`、`ponytail-help`、`ponytail-review` | 跟随 package `4.9.0` |

`pi-subagents` 还会安装 `gather-context-and-clarify`、`parallel-cleanup`、`parallel-research`、`parallel-review`、`review-loop` prompts。

## 4. 安装 Lark/飞书 CLI 与 Skills

当前使用官方 [`larksuite/cli`](https://github.com/larksuite/cli) 的 `v1.0.87` 快照。

```bash
npm install -g @larksuite/cli@1.0.87
npx --yes skills@1.5.22 add larksuite/cli@v1.0.87 -g -y
lark-cli --version
```

预期 CLI 输出：

```text
lark-cli version 1.0.87
```

当前共安装 27 个 Lark Skills：

| Skill | Skill frontmatter 版本 |
|---|---:|
| `lark-approval` | `1.2.0` |
| `lark-apps` | `1.0.0` |
| `lark-attendance` | `1.0.0` |
| `lark-base` | `1.2.6` |
| `lark-calendar` | `1.0.0` |
| `lark-contact` | `1.0.0` |
| `lark-doc` | 未声明；以 `larksuite/cli@v1.0.87` 为准 |
| `lark-drive` | `1.0.0` |
| `lark-event` | `1.0.0` |
| `lark-im` | `1.0.0` |
| `lark-mail` | `1.0.0` |
| `lark-markdown` | `1.2.2` |
| `lark-minutes` | `1.0.0` |
| `lark-note` | `1.0.0` |
| `lark-okr` | `1.0.0` |
| `lark-openapi-explorer` | `1.0.0` |
| `lark-shared` | `1.0.0` |
| `lark-sheets` | `3.1.2` |
| `lark-skill-maker` | `1.0.0` |
| `lark-slides` | `1.0.0` |
| `lark-task` | `1.0.0` |
| `lark-vc` | `1.0.0` |
| `lark-vc-agent` | `1.0.0` |
| `lark-whiteboard` | `1.0.0` |
| `lark-wiki` | `1.0.3` |
| `lark-workflow-meeting-summary` | `1.0.0` |
| `lark-workflow-standup-report` | `1.0.0` |

Skills 应出现在：

```text
~/.agents/skills/lark-*/SKILL.md
```

### Lark 登录（必须在新电脑重新完成）

不要复制旧电脑的 `.lark-cli`、Token 或 App Secret。让 Agent 加载 `lark-shared` Skill 后协助执行：

```bash
lark-cli config init --new
lark-cli auth login --recommend
lark-cli auth status --json --verify
```

配置和授权过程可能要求用户打开浏览器、扫码或批准权限。

## 5. 配置 Chrome DevTools MCP

当前 MCP server：

| 工具 | 版本 | 安装命令 | GitHub |
|---|---:|---|---|
| `chrome-devtools-mcp` | `1.7.0` | `npm install -g chrome-devtools-mcp@1.7.0` | [ChromeDevTools/chrome-devtools-mcp](https://github.com/ChromeDevTools/chrome-devtools-mcp) |

```bash
npm install -g chrome-devtools-mcp@1.7.0
```

在 `~/.pi/agent/mcp.json` 中合并以下 server。`command` 必须替换为新电脑上可执行文件的实际绝对路径：Windows 先运行 `where chrome-devtools-mcp`，macOS/Linux 运行 `command -v chrome-devtools-mcp`。

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "<chrome-devtools-mcp 的绝对路径；Windows 通常以 .cmd 结尾>",
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

不要覆盖文件中已有的其他 `mcpServers`。配置完成后重启 Pi 或执行 `/reload`。

## 6. 辅助浏览器 CLI

当前机器还安装了以下 CLI。它不是 `pi list` 中的 Pi package，但保留后可获得与当前环境一致的浏览器自动化命令行能力。

| 工具 | 版本 | 安装命令 | GitHub |
|---|---:|---|---|
| `agent-browser` | `0.34.0` | `npm install -g agent-browser@0.34.0` | [vercel-labs/agent-browser](https://github.com/vercel-labs/agent-browser) |

```bash
npm install -g agent-browser@0.34.0
agent-browser --version
```

## 7. 合并非模型设置

当前 `~/.pi/agent/settings.json` 中与模型无关、需要恢复的设置为：

```json
{
  "theme": "dark",
  "defaultProjectTrust": "always",
  "hideThinkingBlock": true
}
```

将这些键合并进现有 settings；**不要删除 `packages`**，也不要从旧电脑复制以下模型相关字段：

```text
defaultProvider
defaultModel
defaultThinkingLevel
models
providers
apiKeys
```

安全提示：`"defaultProjectTrust": "always"` 会自动信任项目本地 Pi 配置和扩展。这是为了复刻当前环境；如果新电脑会打开不可信仓库，应改回 `"ask"`。

## 8. 需要单独重新配置的账号/密钥

这些内容不进入 Git：

- Pi 模型 Provider、模型选择和 API Key：运行 Pi 后用 `/login`、`/model`。
- GitHub CLI：`gh auth login`，随后用 `gh auth status` 验证。
- Lark/飞书：按上文重新执行 `config init` 和 `auth login`。
- `pi-web-access` 的搜索 Provider API Key、Google 登录或其他订阅凭据：按实际需要在新电脑重新配置；没有某个 Provider 的 Key 不影响扩展安装，但会影响对应后端。
- 浏览器 Cookie、会话、SSH 私钥、Git credential 等一律重新登录或安全迁移，不放入本仓库。

## 9. 验收

执行：

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

预期核心结果：

- Pi：`0.84.2`
- `pi list`：7 个 package，名称和第 3 节完全一致
- package 自带 Skills：8 个
- Lark Skills：27 个
- 合计可发现 Skills：35 个
- Lark CLI：`1.0.87`
- Chrome DevTools MCP：`1.7.0`
- agent-browser：`0.34.0`
- GitHub CLI：`2.96.0`

然后启动 Pi，执行 `/reload`，确认：

1. `web_search`、`source_check`、`fetch_content`、`get_search_content` 可用；
2. `mcp`、`mcpScript` 可用，`chrome-devtools` server 可连接；
3. `subagent` 相关工具和 `pi-subagents` Skill 可用；
4. `preview_export` 可用；
5. Ponytail 的 6 个 Skills 可发现；
6. 27 个 `lark-*` Skills 可发现，登录后能调用 `lark-cli`；
7. `/goal`、Pine of Glass UI/可观测性能力正常加载。

## 10. 更新本快照

这是“可复现快照”，所以安装命令都固定版本。需要升级时：

1. 在当前机器有意执行升级；
2. 验证功能；
3. 用 `pi --version`、`pi list`、各 package 的 `package.json`、`lark-cli --version` 重新盘点；
4. 同步修改本 README 中的版本和命令；
5. 提交 README。

不要只把安装命令改成 `latest`，否则新电脑无法复现同一套能力。
