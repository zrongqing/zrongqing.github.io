---
title: CC Switch 使用教程：在 Codex、DeepSeek 与 ChatGPT 之间快速切换
tags:
  - CC Switch
  - Codex
  - DeepSeek
  - ChatGPT
  - AI 编程
categories:
  - AI
  - AI 编程工具
keywords:
  - CC Switch 使用教程
  - Codex 切换 DeepSeek
  - Codex ChatGPT 登录
  - AI 编程工具
  - VPN 代理冲突
description: >-
  介绍如何用 CC Switch 管理 Codex 等常用 AI 编程工具，在 ChatGPT 官方登录、OpenAI API、DeepSeek
  及其他兼容供应商之间切换，并排查 VPN、系统代理、本地路由和环境变量冲突。
abbrlink: 1aa774e3
toc: true
aside: true
highlight_shrink: true
date: 2026-08-26 23:55:29
updated: 2026-08-26 23:55:29
---

CC Switch 适合同时使用 Codex、Claude Code、Gemini CLI、OpenCode 等 AI 编程工具的人。它把散落在 JSON、TOML 和环境变量里的供应商配置集中到桌面界面中，帮助我们保存多套 API 地址、密钥和模型配置，再通过主界面或系统托盘快速切换。

本文重点讲清楚三个最容易混淆的问题：如何在 Codex 中切换 ChatGPT 官方账号与 DeepSeek，哪些情况需要 CC Switch 本地路由，以及它与 Clash、V2Ray、Surge 等 VPN/代理工具是否会冲突。

> 本文核对时间为 2026 年 8 月 26 日。CC Switch 更新较快，旧版本的菜单名称和 DeepSeek 路由方式可能不同；涉及路由时，应以供应商卡片上的“需要路由”标记为准。

<!-- more -->

## 先理解 CC Switch 到底切换什么

CC Switch 不是一个新的大模型，也不是 VPN。它主要做两件事：

1. 保存并切换 AI 编程工具的本地配置；
2. 在需要时启动本地 HTTP 路由，完成请求转发、格式转换、用量统计和故障转移。

普通切换的请求路径如下：

```text
Codex / Claude Code / Gemini CLI
              │
              │ 读取 CC Switch 写入的本地配置
              ▼
       OpenAI / DeepSeek / 其他供应商
```

开启本地路由后的请求路径如下：

```text
AI 编程工具
     │
     │ http://127.0.0.1:15721
     ▼
CC Switch 本地路由
     │
     │ 可选：再经过 VPN/系统代理
     ▼
OpenAI / DeepSeek / 其他供应商
```

CC Switch 当前可管理 Claude Code、Claude Desktop、Codex、Gemini CLI、Grok Build、OpenCode、OpenClaw 和 Hermes。它也可以统一管理部分 MCP、Prompts 和 Skills。支持范围会随版本变化，安装前可以查看 [CC Switch 官方中文说明](https://github.com/farion1231/cc-switch/blob/main/README_ZH.md)。

### “ChatGPT”在本文中代表什么

这里的“切换到 ChatGPT”通常是指：让 **Codex 使用 ChatGPT 账号的官方订阅额度**。它不等于修改 ChatGPT 网页版或独立客户端的模型线路。

OpenAI 官方文档说明，Codex 本地客户端支持两种官方认证方式：

- 使用 ChatGPT 账号登录，消耗对应套餐内的 Codex 额度；
- 使用 OpenAI API Key，按 API 用量计费。

两者的认证、计费和数据策略不同。不要把 ChatGPT 密码填入 CC Switch；ChatGPT 登录应通过 Codex 打开的官方浏览器授权页完成。详见 [OpenAI 官方 Codex 认证文档](https://learn.chatgpt.com/docs/auth)。

## 与常用 AI 桌面编程工具怎么配合

| 使用场景 | CC Switch 能否直接切换 | 说明 |
| --- | --- | --- |
| Codex 桌面应用 | 可以 | 重点管理 `~/.codex/` 下的用户配置和认证 |
| Codex CLI | 可以 | 普通切换后建议关闭并重新启动 Codex |
| Codex IDE 扩展 | 可以 | CLI 与 IDE 扩展共享配置层；必要时重载 IDE 窗口 |
| VS Code、Cursor、Windsurf、JetBrains 的集成终端 | 可以 | 在终端里运行受支持的 CLI 即可 |
| Cursor/Windsurf 自带的聊天与补全 | 通常不可以 | 它们有独立的账号和供应商设置，不读取 CC Switch 的 Codex 配置 |
| Claude Code、Gemini CLI、OpenCode 等 | 可以 | 在 CC Switch 中切到对应应用页分别配置 |

Codex 的 CLI 与 IDE 扩展共享用户级 `~/.codex/config.toml`，但项目内的 `.codex/config.toml`、启动参数或 profile 可能具有更高优先级。若“切换成功但当前项目仍使用旧模型”，不要反复覆盖用户配置，应先检查项目级覆盖。配置优先级可参考 [OpenAI 官方 Codex 配置说明](https://learn.chatgpt.com/docs/config-file/config-basic)。

## 安装与首次准备

### 1. 只从官方渠道下载

CC Switch 是免费开源软件。建议只使用以下入口：

- [CC Switch 官方仓库](https://github.com/farion1231/cc-switch)
- [GitHub Releases 下载页](https://github.com/farion1231/cc-switch/releases)

Windows 通常优先选择 MSI 安装包；macOS 选择与 Intel 或 Apple Silicon 对应的 DMG；Linux 按发行版选择 AppImage、Deb 或 RPM。不要从要求付费、充值或提交账号密码的同名网站下载。

### 2. 先安装并运行目标编程工具

如果主要使用 Codex，应先安装 Codex CLI、Codex 桌面应用或 IDE 扩展，并至少启动一次。CC Switch 需要发现实际配置目录，才能导入和切换配置。

已有 Codex 官方登录的用户，建议先确认官方模式能正常发起一次对话，再打开 CC Switch。这样首次导入时就有一份可恢复的官方配置。

### 3. 确认备份位置并保护密钥

CC Switch 会把供应商配置保存在 `~/.cc-switch/cc-switch.db`，并在 `~/.cc-switch/backups/` 保留自动备份。配置包含 API Key 等敏感信息，不要把数据库、`~/.codex/auth.json`、截图中的完整密钥或导出文件提交到 Git 仓库。

## 通用的供应商添加与切换方法

### 添加供应商

1. 在 CC Switch 顶部选择要管理的应用，例如 **Codex**；
2. 点击右上角的 `+`；
3. 优先从“预设”中选择 OpenAI 官方、DeepSeek、Kimi、智谱 GLM 等供应商；
4. 填写自己的 API Key，确认预设自动带出的端点和模型；
5. 点击“添加”。

预设会处理容易写错的 Base URL、协议类型和模型目录。除非供应商明确给出了自定义接入文档，否则不要手动拼接 `/v1`、`/responses` 或 `/chat/completions`。

### 切换供应商

有两种常用方式：

- 在主界面点击目标供应商卡片的“启用”；
- 右键系统托盘中的 CC Switch 图标，在 Codex、Claude 或 Gemini 子菜单中选择供应商。

普通配置切换后，Codex 通常需要完全退出并重新启动。开启 CC Switch 本地路由并接管 Codex 后，路由模式下切换供应商可以立即影响后续请求，但已开始的对话仍可能保留原模型上下文，重要任务建议新建会话验证。

## 重点一：在 Codex 中使用 ChatGPT 官方账号

### 单个 ChatGPT 账号

推荐顺序如下：

1. 先在 Codex 中完成一次“使用 ChatGPT 登录”；
2. 回到 CC Switch 的 Codex 页面，导入当前配置或添加“OpenAI 官方”供应商；
3. 启用该官方供应商；
4. 重启 Codex，确认账号和模型选择器正常。

如果以前使用的是 API Key，需要先在 Codex 中退出旧认证，再按界面提示选择 ChatGPT 登录。ChatGPT 套餐是否包含 Codex、额度多少和支持哪些模型会变化，应以账号页面与 OpenAI 官方文档为准。

### 多个 ChatGPT/Codex 官方账号

较新的 CC Switch 提供认证中心，可以保存多个已经完成 OAuth 授权的 ChatGPT/Codex 账号，并把不同账号绑定到不同的“OpenAI 官方”供应商卡片。切换卡片后重启 Codex，即可让 CLI 和桌面应用使用对应账号。

如果安装的版本还没有认证中心，可以为每个账号建立单独的官方供应商卡片，切到对应卡片后按提示执行 Codex 的退出/登录流程。不要复制浏览器 Cookie 或手工粘贴 OAuth Token。

### 同时保留官方登录与第三方模型

经常在 ChatGPT 官方订阅和 DeepSeek 之间往返时，建议打开：

```text
设置 → 通用 → Codex 应用增强 → 非接管切换时保留官方登录
```

旧版本的文字可能是“切换第三方时保留官方登录”。开启后，官方登录缓存继续保留在 `~/.codex/auth.json`，第三方供应商密钥由 `config.toml` 中对应 provider 的认证配置承载。路由接管期间，较新版本会始终保留官方登录。

这项设置能减少“从 DeepSeek 切回官方后出现 401，只能重新登录”的情况，也有助于 Codex 桌面应用继续识别官方身份。它不代表第三方模型可以消耗 ChatGPT 订阅额度：使用 DeepSeek 时仍由 DeepSeek Key 计费。

## 重点二：在 Codex 中使用 DeepSeek

### 新建 DeepSeek 供应商

1. 打开 CC Switch 的 **Codex** 页面；
2. 点击 `+`，选择 **DeepSeek** 预设；
3. 填入在 DeepSeek 平台申请的 API Key；
4. 保留预设给出的端点、协议和模型目录；
5. 添加后点击“启用”；
6. 按 CC Switch 的提示重启 Codex。

DeepSeek 官方 OpenAI 兼容 Base URL 是 `https://api.deepseek.com`。模型名和支持的协议会更新，优先使用 CC Switch 预设自动获取的列表，不要照抄过期教程中的固定模型 ID。可参考 [DeepSeek API 官方文档](https://api-docs.deepseek.com/)。

### DeepSeek 是否需要开启本地路由

这是版本差异最大的地方：

- CC Switch 3.19.1 之后**新建**的 DeepSeek 预设已使用原生 Responses 接口，通常可以直连，不需要本地路由；
- 升级前已经保存的 DeepSeek 卡片不会自动改变协议，如果仍显示“需要路由”，可以编辑供应商，把“上游格式”改为原生 Responses，或继续使用本地路由；
- 某个特定 DeepSeek 模型若只开放 Chat Completions 接入，仍要选择 Chat 格式并开启 Codex 路由；
- Kimi、GLM、MiniMax 或部分聚合服务若只提供 Chat Completions，也要通过 CC Switch 将 Codex 的 Responses 请求转换为 Chat 请求。

不要只根据供应商名称判断。**卡片有“需要路由”徽标，就按提示开启路由；没有徽标，优先直连。** 具体版本差异见 [CC Switch 官方 Codex + DeepSeek 路由指南](https://github.com/farion1231/cc-switch/blob/main/docs/guides/codex-deepseek-routing-guide-zh.md)。

### 开启 Codex 本地路由

界面名称可能随版本调整，常见路径是：

```text
设置 → 路由服务（或高级 → 路由服务）
```

然后完成两步：

1. 启动 CC Switch 本地路由服务，默认监听 `127.0.0.1:15721`；
2. 在“应用路由”中开启 **Codex**。

接管后，Codex 的 API 地址会暂时指向类似 `http://127.0.0.1:15721/v1` 的本地地址，CC Switch 再把请求转换并转发到当前供应商。关闭 Codex 路由或停止服务时，CC Switch 会尝试恢复接管前的配置，因此不要在路由运行期间同时用其他配置工具覆盖 `~/.codex/config.toml`。

## 重点三：ChatGPT、DeepSeek 和其他供应商怎样来回切换

可以按下面的表格操作：

| 目标 | CC Switch 操作 | 是否需要本地路由 | 切换后动作 |
| --- | --- | --- | --- |
| ChatGPT 官方订阅 | 启用绑定相应账号的“OpenAI 官方”卡片 | 通常不需要 | 重启 Codex，检查账号和模型 |
| OpenAI API Key | 启用保存了 API Key 的 OpenAI 供应商 | 通常不需要 | 重启 Codex，确认按 API 计费 |
| DeepSeek 原生 Responses | 启用新版 DeepSeek 预设 | 通常不需要 | 重启 Codex，检查模型目录 |
| DeepSeek Chat 或旧配置 | 启用 DeepSeek 卡片 | 需要 | 启动路由并开启 Codex 接管 |
| 仅支持 Chat Completions 的其他供应商 | 启用对应预设 | 通常需要 | 查看“需要路由”徽标并按提示操作 |

日常使用可以保留三张卡片：

```text
Codex
├── ChatGPT 官方账号
├── OpenAI API Key（可选）
└── DeepSeek API
```

写代码前从托盘选择目标供应商，随后重启 Codex 或新建会话。切回官方时若遇到 401，先确认当前卡片确实是“OpenAI 官方”，再检查官方登录是否仍在；不要先删除 `auth.json`。

## CC Switch 会和 VPN 工具冲突吗

### 结论

**通常不会天然冲突，但两者都涉及代理时可能形成错误的代理链。**

VPN/代理工具负责“这台电脑怎样访问外网”；CC Switch 本地路由负责“AI 请求交给哪个供应商、是否转换协议”。只要保留本地回环地址、上游代理地址和 CC Switch 监听端口之间的边界，两者可以同时工作。

### 推荐方案 A：VPN 使用 TUN 模式

TUN 模式通常会透明接管出站流量。此时建议：

1. CC Switch 的“全局代理”先留空；
2. 在 VPN 规则中让 `127.0.0.1` 和 `localhost` 直连，不要把本机回环流量送进远端代理；
3. 让 OpenAI、DeepSeek 等真实上游流量按 VPN 规则访问；
4. 开启 CC Switch 路由后测试一次实际请求。

如果 TUN 的 Fake IP、DNS 劫持或 HTTPS 检查导致 TLS 错误，应在 VPN 中为目标 API 和登录域名增加合理规则。不要通过关闭证书校验来“解决”问题。

### 推荐方案 B：VPN 开启系统代理

如果 VPN 在本机提供 HTTP 或 SOCKS5 端口，例如 `127.0.0.1:7890` 或 `127.0.0.1:1080`，可以在 CC Switch 的“全局代理”里显式填写：

```text
http://127.0.0.1:7890
```

或：

```text
socks5://127.0.0.1:1080
```

保存前使用 CC Switch 的“测试连接”。显式配置通常比依赖应用启动时自动读取系统代理更容易排查。

> 不要把 CC Switch 的全局上游代理填写成它自己的本地路由地址 `127.0.0.1:15721`，否则可能产生代理自环。`15721` 是 AI 工具进入 CC Switch 的入口，不是 CC Switch 访问外网的出口。

### 推荐方案 C：能够直连供应商

如果当前网络可以直接访问目标供应商，把 CC Switch 全局代理留空即可。界面当前提示留空表示直连；部分旧版本或底层网络库可能仍继承启动时的系统代理状态，因此从“开代理”切到“完全直连”后若持续出现 502，可保存一次空代理设置并重启 CC Switch。

### VPN 开关后出现 502 或超时

某些版本会在启动时建立并缓存 HTTP 客户端。若 CC Switch 启动时 VPN 的系统代理是 `127.0.0.1:7890`，随后直接退出 VPN，CC Switch 可能仍尝试访问已经关闭的端口。

按以下顺序恢复：

1. 确认 VPN 的本地代理端口是否仍在监听；
2. 在 CC Switch 中清空或改正“全局代理”，再次测试连接；
3. 停止并重新启动 CC Switch 路由；
4. 完全退出并重新打开 CC Switch；
5. 最后再重启 Codex。

新版 CC Switch 已支持显式全局代理的运行时更新，但“系统代理在应用外部突然变化”仍可能因操作系统、VPN 模式和版本不同而表现不一致。

## Windows 上的快速排查命令

以下命令都可以在 PowerShell 中运行，不会显示 API Key 的值。

### 检查 CC Switch 默认端口

```powershell
Test-NetConnection 127.0.0.1 -Port 15721
Get-NetTCPConnection -LocalPort 15721 -State Listen -ErrorAction SilentlyContinue
```

如果卡片显示“需要路由”，但 15721 没有监听，应先启动 CC Switch 路由服务。若端口被其他程序占用，可以停止路由后在设置里更换端口，再重新开启接管。

### 检查上游网络

```powershell
Resolve-DnsName api.deepseek.com
Test-NetConnection api.deepseek.com -Port 443
```

DNS 或 443 端口不通属于网络/VPN/防火墙问题，不是供应商切换本身的问题。

### 检查会覆盖配置的环境变量

```powershell
Get-ChildItem Env: |
  Where-Object Name -Match '^(OPENAI|ANTHROPIC|GEMINI|HTTP|HTTPS|ALL|NO)_' |
  Select-Object -ExpandProperty Name
```

重点检查旧的 `OPENAI_API_KEY`、`ANTHROPIC_BASE_URL`、`HTTP_PROXY`、`HTTPS_PROXY` 和 `ALL_PROXY`。不要直接删除不理解的系统变量；先记录来源，确认它是否属于旧教程、公司网络或当前 VPN，再在对应的系统设置、Shell 配置或启动脚本中修改。

CC Switch 自带环境变量冲突检测。环境变量通常可能覆盖应用配置，导致“界面显示 DeepSeek，实际请求却发往旧地址”。

## 按错误现象定位问题

| 现象 | 更可能的原因 | 处理方式 |
| --- | --- | --- |
| `Connection refused 127.0.0.1:15721` | Codex 仍指向本地路由，但路由服务未运行 | 启动路由，或从 CC Switch 正常关闭接管以恢复配置 |
| CC Switch 返回 502/503 | 上游代理关闭、VPN 规则错误、供应商超时或协议不匹配 | 测试全局代理和上游 443；检查供应商是否要求路由 |
| 401/403 | API Key、官方登录或账号权限问题 | 确认当前卡片和认证方式；重新执行官方登录或更新 Key |
| 404 `/responses` | 上游不支持 Responses，或 Base URL 拼错 | 改用正确预设；必要时选 Chat 格式并开启路由转换 |
| 切换后仍是旧供应商 | Codex 未重启、项目配置/profile/启动参数覆盖用户配置 | 重启 Codex，检查 `.codex/config.toml` 和启动参数 |
| VPN 关闭后持续失败 | CC Switch 或 Codex 仍保留旧代理连接/配置 | 清空全局代理，依次重启路由、CC Switch 和 Codex |
| 浏览器登录后无法回到 Codex | OAuth 回调的 localhost 被 VPN、浏览器或防火墙拦截 | 放行本机回环，暂时改用兼容的代理模式后重试 |

## 怎样确认切换真的生效

不要只问模型“你是谁”。第三方模型可能沿用 Codex 的系统提示词，自报身份并不可靠。

更稳妥的验证方法是：

1. 检查 CC Switch 中目标供应商卡片的启用状态；
2. 普通切换后重启 Codex，路由切换后新建会话；
3. 在 Codex 的账号、模型或状态界面查看当前配置；
4. 开启本地路由时，在 CC Switch 请求日志中核对供应商、模型、状态码和延迟；
5. 到对应供应商控制台查看是否产生了新的用量记录。

验证时使用简单、低成本的请求即可。不要为了确认模型而提交包含公司源码、客户数据或密钥的测试内容。

## 安全与费用注意事项

- 每个供应商有独立的价格、速率限制、日志保留和数据条款；切换供应商也等于切换数据处理方。
- ChatGPT 订阅额度不能自动抵扣 DeepSeek 或第三方 API 费用。
- 不要在聊天、截图、Git 提交和公共 Issue 中暴露 API Key、OAuth Token 或 `auth.json`。
- 中转服务能看到经它转发的内容。涉及私有代码时，应先确认组织政策和供应商的数据条款。
- VPN 的 HTTPS 解密、公司代理证书和零信任网关可能检查请求。遇到证书错误时应正确部署受信任 CA 或调整网络策略，而不是关闭 TLS 校验。

## 恢复到官方 Codex 的稳妥步骤

如果配置已经混乱，可以按下面的顺序回到官方模式：

1. 在 CC Switch 中停止 Codex 路由接管；
2. 启用“OpenAI 官方”供应商；
3. 完全退出并重新启动 Codex；
4. 若官方登录确实失效，再通过 Codex 的官方登录流程重新登录 ChatGPT；
5. 仍有问题时，先查看 `~/.cc-switch/backups/`，不要直接删除整个 `~/.codex/`。

CC Switch 以自动备份和原子写入降低配置损坏风险，但它不能替代对密钥和账号的正常管理。关闭路由或切换供应商时，尽量从 CC Switch 界面完成，不要同时使用多个配置切换器。

## 总结

CC Switch 最适合把 **Codex 作为统一编程客户端，再按任务在 ChatGPT 官方订阅、OpenAI API、DeepSeek 和其他供应商之间切换**：

- ChatGPT 官方模式优先走 Codex 官方登录；
- DeepSeek 新预设优先用原生 Responses 直连；
- 只有 Chat Completions 的供应商通过 CC Switch 本地路由转换；
- VPN 负责外网链路，CC Switch 负责供应商与协议，两者通过正确的回环和上游代理规则配合；
- 出现异常时先区分 401、404、502 和本地端口错误，再检查环境变量和项目级 Codex 配置。

把“官方账号”“API Key”“本地路由”“VPN 出口”分成四层理解，切换和排障就会清晰很多。

## 参考资料

- [CC Switch 官方中文说明](https://github.com/farion1231/cc-switch/blob/main/README_ZH.md)
- [CC Switch 用户手册：快速上手](https://github.com/farion1231/cc-switch/blob/main/docs/user-manual/zh/1-getting-started/1.4-quickstart.md)
- [CC Switch 用户手册：添加供应商](https://github.com/farion1231/cc-switch/blob/main/docs/user-manual/zh/2-providers/2.1-add.md)
- [CC Switch 用户手册：本地路由服务](https://github.com/farion1231/cc-switch/blob/main/docs/user-manual/zh/4-proxy/4.1-service.md)
- [CC Switch：Codex + DeepSeek 路由指南](https://github.com/farion1231/cc-switch/blob/main/docs/guides/codex-deepseek-routing-guide-zh.md)
- [OpenAI 官方文档：Codex 认证](https://learn.chatgpt.com/docs/auth)
- [OpenAI 官方文档：Codex 配置基础](https://learn.chatgpt.com/docs/config-file/config-basic)
- [DeepSeek API 官方文档](https://api-docs.deepseek.com/)
