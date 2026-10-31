# dsh-plugin-mcp-codegraph

DSH 配置型 bundle：把本地 CodeGraph MCP 服务（`codegraph serve --mcp`，stdio）接入 Harness，
让智能体获得 `mcp__codegraph__codegraph_explore` 工具。不含运行时代码，只有一份 Cordis patch。

## 结构

```
package.json         # dsh.bundle.patch 指向 cordis.patch.yml
cordis.patch.yml     # 插入一条 @deepseek-ai/dsh-mcp-client 配置行（id: codegraph-mcp）
locale/en.json       # 插件面板标题与描述（英文）
locale/zh.json       # 插件面板标题与描述（中文）
icon.svg             # 插件面板图标
.gitignore
```

## 配置

| 字段 | 值 | 说明 |
|---|---|---|
| `serverName` | `codegraph` | 工具命名空间：`mcp__codegraph__<tool>` |
| `transport` | `stdio` | 本地子进程 |
| `command` | `codegraph` | 从 PATH 解析，假定已安装且对 Harness 进程可见 |
| `args` | `serve --mcp` | MCP stdio 服务 |
| `toolCallTimeoutMs` | `120000` | 单次调用超时 |
| `failOnStartupError` | `false` | 连接失败只报错，不阻断 harness 启动 |

> `command` 不写绝对路径是有意的：换机器、换 Node 版本管理器、codegraph 升级后都不会失效。
> 如果你是用 Finder/GUI 启动 Harness、PATH 里没有 `codegraph`，改成绝对路径
> （例如 `/Users/<you>/.local/bin/codegraph`）或在 `env` 里补 `PATH`。

## 前提

```bash
codegraph --version   # 能输出版本号即可
```

## 安装

用 `plugin_manager` 安装本目录（绝对路径）：

```
plugin_manager({ action: "install_bundle", target: "/Users/snow/projects/dsh-plugin-mcp-codegraph" })
```

修改配置后需要重新安装（或重启 App）让 profile 重新合成。

## 用法

服务按项目工作：`codegraph_explore` 需要 `projectPath` 指向一个已用 `codegraph init`
建过索引的项目（或其中任意子目录）。已在索引的项目可直接传路径。
