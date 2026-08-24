# Hexo 博客维护指南

本指南针对当前仓库，不是一份脱离项目配置的 Hexo 通用手册。

## 目录

- [项目约定](#项目约定)
- [首次准备](#首次准备)
- [常用命令](#常用命令)
- [文章与草稿流程](#文章与草稿流程)
- [Front Matter 字段](#front-matter-字段)
- [Markdown 与图片约定](#markdown-与图片约定)
- [构建与发布](#构建与发布)
- [常见问题](#常见问题)

## 项目约定

| 项目 | 当前值 | 作用 |
| --- | --- | --- |
| Hexo | `8.1.2`（`package.json` 使用 `^8.1.2`） | 把 Markdown 和主题模板生成静态站点；要求 Node.js `>=20.19.0` |
| 主题 | Butterfly `5.4.2`（锁文件版本，`package.json` 使用 `^5.4.2`） | 控制页面布局、文章封面、目录、侧栏等 |
| CI Node.js | `22.x` | GitHub Actions 构建环境 |
| 源码分支 | `main` | 保存文章、配置和部署工作流 |
| 发布分支 | `gh-pages` | 保存生成后的静态站点 |
| 站点地址 | `https://zrongqing.github.io/` | GitHub Pages 公开地址 |
| 文章目录 | `source/_posts/` | 已发布文章的 Markdown 源文件 |
| 草稿目录 | `source/_drafts/` | 默认不会公开的草稿 |
| 文章模板 | `scaffolds/post.md` | `hexo new post` 创建文章时使用 |
| 站点配置 | `_config.yml` | Hexo、URL、永久链接和部署配置 |
| 主题配置 | `_config.butterfly.yml` | Butterfly 的导航和外观配置 |

`source/`、scaffold 和配置文件是博客源文件。`public/`、`db.json` 与 `.deploy*/` 是本地生成物，已被 `.gitignore` 忽略，不应提交。根目录中仍有少量历史生成页面；不要通过编辑它们来维护博客。

当前没有 `.csproj`、`.nuspec`、`.nupkg` 或 NuGet 发布配置，所以本仓库的“上传”是发布到 GitHub Pages，不是上传到 NuGet。

## 首次准备

在仓库根目录运行：

```powershell
node --version
npm ci
```

- 优先使用 `npm ci`，它严格按照 `package-lock.json` 安装依赖，适合已有锁文件的项目。
- 使用 `npm run ...` 或 `npx hexo ...` 调用本地 Hexo，不要求全局安装 `hexo-cli`。
- GitHub Actions 使用 Node.js 22；本地遇到兼容问题时优先切换到 Node.js 22 复现。
- 如果 `node_modules/` 中的主题版本与 `package-lock.json` 不一致，删除陈旧依赖后重新运行 `npm ci`；不要以陈旧的本地安装判断 CI 使用的版本。
- 当前工作流会在 CI 中额外安装 `hexo-asset-img`，但它没有写入 `package.json`。涉及文章相对图片时，要同时检查本地预览和 CI 构建，不能只看 Markdown 编辑器预览。

## 常用命令

| 命令 | 简写/替代 | 作用 | 是否改变内容或远端 |
| --- | --- | --- | --- |
| `npm ci` | — | 按锁文件安装依赖 | 写入 `node_modules/`，不改远端 |
| `npx hexo new post "标题"` | `npx hexo new "标题"` | 按 `scaffolds/post.md` 新建文章 | 新增 `source/_posts/*.md` |
| `npx hexo new draft "标题"` | — | 按 `scaffolds/draft.md` 新建草稿 | 新增 `source/_drafts/*.md` |
| `npx hexo publish "标题"` | — | 把同名草稿移到 `_posts` | 移动草稿文件 |
| `npm run clean` | `npx hexo clean` | 删除缓存 `db.json` 和输出 `public/` | 只删本地生成物 |
| `npm run build` | `npx hexo generate` / `npx hexo g` | 生成静态站点到 `public/` | 生成本地文件；插件可能回写空 `abbrlink` |
| `npx hexo generate --watch` | `npx hexo g -w` | 监听文件并持续生成 | 持续更新 `public/` |
| `npm run server` | `npx hexo server` / `npx hexo s` | 启动本地站点，默认端口 4000，并监听源文件变化 | 启动本地服务 |
| `npx hexo server --draft` | `npx hexo s --draft` | 连同草稿一起预览 | 启动本地服务 |
| `npx hexo server -p 5000` | — | 端口 4000 被占用时改用 5000 | 启动本地服务 |
| `npm run deploy` | `npx hexo deploy` / `npx hexo d` | 按 `_config.yml` 直接推送到 `gh-pages` | **改变远端** |
| `npx hexo generate --deploy` | `npx hexo g -d` | 先生成再直接部署 | **改变远端** |

日常预览通常不需要先手动生成：`hexo server` 会监听并重新生成源文件。遇到缓存或输出异常时，再执行 `npm run clean` 后重启服务。

## 文章与草稿流程

### 新建正式文章

```powershell
npx hexo new post "文章标题"
```

然后编辑 `source/_posts/文章标题.md`：

1. 补齐标签、分类、关键词和摘要。
2. 不要保留 `tag1`、`category1`、`keyword1` 等模板占位值；空字段也应在发布前补齐或有意删除。
3. 按“问题背景 → 环境/前提 → 操作步骤 → 验证 → 常见错误 → 结论”组织技术文章。
4. 在引言后放置 `<!-- more -->`，明确首页摘要的结束位置。
5. 运行 `npm run clean` 和 `npm run build`。
6. 本地启动 `npm run server`，检查首页卡片、文章页、目录、代码块和图片。

### 使用草稿

```powershell
npx hexo new draft "文章标题"
npx hexo server --draft
npx hexo publish "文章标题"
```

`draft` 位于 `source/_drafts/`，默认不会生成到公开站点。`publish` 会把草稿移动到 `source/_posts/`，并采用对应 layout 的 scaffold 规则。

### 修改已发布文章

- 保留原来的 `date`，更新 `updated`。
- 保留非空 `abbrlink`，否则公开 URL 会变化，旧链接和搜索索引可能失效。
- 重命名 Markdown 文件或资源目录前，先搜索所有相对链接。
- 只修正文稿时，不顺手升级主题或重写站点配置。

## Front Matter 字段

Front Matter 是 Markdown 文件最前面的 YAML 区块，必须由两行 `---` 包住：

```yaml
---
title: "示例：在 Windows 上部署 Hexo"
date: 2026-08-15 10:30:00
updated: 2026-08-15 10:30:00
tags:
  - Hexo
  - GitHub Pages
categories:
  - Web
  - Hexo
keywords:
  - Hexo
  - GitHub Pages
description: "介绍本仓库从本地写作到 GitHub Pages 发布的完整流程。"
abbrlink:
toc: true
aside: true
highlight_shrink: true
---
```

标题中包含冒号、`#`、方括号或其他 YAML 特殊字符时，用引号包住值。

### Hexo 核心字段

| 字段 | 作用 | 本仓库建议 |
| --- | --- | --- |
| `layout` | 指定 `post`、`page`、`draft` 或自定义布局；`false` 表示不套主题布局 | 普通文章省略，使用 `_config.yml` 的 `default_layout: post` |
| `title` | 页面和文章标题；文章未填时通常回退到文件名 | 必填，使用清楚、可搜索的标题 |
| `date` | 发布时间，影响排序和归档 | 新建时保留 scaffold 生成值；已发布文章不要随意改 |
| `updated` | 最后更新时间 | 修改已发布文章时显式更新；未填时当前配置按文件修改时间计算 |
| `tags` | 并列标签，所有标签处于同一层级 | 使用 YAML 列表；控制数量并复用已有拼写 |
| `categories` | 分类路径 | 普通列表表示一条层级路径，不是多个并列分类 |
| `comments` | 是否允许主题渲染评论区 | `false` 可关闭；`true` 仍要求主题已配置评论提供方 |
| `permalink` | 为单篇内容覆盖站点永久链接 | 本仓库优先使用 `abbrlink`，一般不要设置 |
| `published` | 是否发布该文章 | `_posts` 默认 `true`；需要隐藏时优先使用草稿 |
| `lang` | 为单页覆盖站点语言 | 仅多语言文章使用 |
| `disableNunjucks` | 禁止处理 Nunjucks 表达式 | 文中展示大量 `{{ ... }}` 或 `{% ... %}` 且发生误解析时使用 |
| `excerpt` | 纯文本摘要字段 | 本仓库优先使用 `description` 与 `<!-- more -->` |

分类层级示例：

```yaml
# 一条层级路径：.NET > WPF
categories:
  - .NET
  - WPF
```

多个独立分类路径要使用嵌套列表：

```yaml
categories:
  - [.NET, WPF]
  - [UI, Desktop]
```

### 本仓库与 Butterfly 字段

| 字段 | 作用 | 注意事项 |
| --- | --- | --- |
| `description` | 首页摘要候选和 SEO 描述 | 建议用一两句话说明问题、方案和适用范围 |
| `keywords` | SEO 关键词 | 使用 YAML 列表，避免堆砌与正文无关的词 |
| `abbrlink` | `hexo-abbrlink` 生成的稳定短链接 | 当前 URL 格式是 `/posts/:abbrlink/`；新文章可留空，生成后不要改 |
| `cover` | 首页、归档、相关文章和分享信息使用的封面 | 可用 URL、资源路径或 `false`；必须验证实际渲染 |
| `top_img` | 文章页顶部横幅 | 未填时主题会回退到封面或默认图；`false` 可关闭顶部图 |
| `toc` | 是否显示文章目录 | 长技术文章建议 `true` |
| `toc_number` | 是否给目录项显示编号 | 省略时继承主题配置 |
| `toc_expand` | 是否默认展开完整目录 | 省略时继承主题配置 |
| `toc_style_simple` | 是否使用简洁目录样式 | 仅在需要覆盖主题默认样式时设置 |
| `aside` | 是否显示侧边栏 | 文章模板默认 `true`；需要宽内容区时可设 `false` |
| `highlight_shrink` | 是否默认折叠代码块 | 代码很长时设 `true`；短示例可设 `false` |
| `copyright` | 是否显示文章版权模块 | `false` 可针对单篇关闭 |
| `copyright_author` | 覆盖版权作者 | 仅单篇作者不同于站点作者时设置 |
| `copyright_author_href` | 覆盖版权作者链接 | 与自定义作者一起使用 |
| `copyright_url` | 覆盖版权文章链接 | 一般让主题使用文章永久链接 |
| `copyright_info` | 覆盖版权说明 | 仅有特殊授权条款时设置 |
| `mathjax` | 为单篇加载 MathJax | 主题数学功能和依赖也必须配置完成 |
| `katex` | 为单篇加载 KaTeX | 不要与 `mathjax` 同时开启，除非已验证配置 |
| `aplayer` | 为单篇加载 APlayer 资源 | 仅含播放器且主题相关功能已配置时开启 |
| `sticky` | Butterfly 可显示置顶标记 | 当前依赖中没有置顶排序生成器，不能只靠此字段保证排序 |

## Markdown 与图片约定

- 只使用一个一级标题来源：文章标题由 Front Matter 的 `title` 提供，正文从 `##` 开始。
- 代码块标注语言，例如 `powershell`、`bash`、`csharp`、`yaml`、`json`。
- 命令和输出分开；不要把提示符、解释文字复制进可直接执行的代码块。
- 写明操作系统、运行目录、关键版本和前置条件。
- 外部资料使用描述性链接文字，不使用裸链接充当正文。
- 不写入访问令牌、SSH 私钥、连接字符串、真实密码或可复用 Cookie。

当前 `post_asset_folder` 为 `false`，但已有文章使用以下资源布局，并依赖 CI 中额外安装的 `hexo-asset-img` 处理相对图片：

```text
source/_posts/
├── Deploy-Hexo-to-GitHub-Pages.md
└── Deploy-Hexo-to-GitHub-Pages/
    ├── github_page.png
    └── secrets-ssh.png
```

Markdown 中使用：

```markdown
![GitHub Pages 设置](Deploy-Hexo-to-GitHub-Pages/github_page.png)
```

新增图片后至少验证：文件名大小写、相对路径、本地文章页、生成后的 `public/` 资源，以及 Linux CI 中的大小写敏感行为。

## 构建与发布

### 发布前检查

```powershell
npm ci
npm run clean
npm run build
npm run server
git status --short
```

重点检查：

- 构建命令以成功状态退出，没有 YAML、渲染器或缺失资源错误。
- 首页摘要没有截断代码块，`<!-- more -->` 位置合理。
- 文章 URL 为预期的 `/posts/<abbrlink>/`。
- 目录、代码块、图片、外部链接和移动端布局可用。
- `git status` 中没有 `public/`、`db.json`、日志、依赖目录或密钥。

### 默认自动发布

1. 只提交文章源文件、必要资源、配置或工作流变更。
2. 推送到 `main`。
3. `.github/workflows/deploy.yml` 在 Node.js 22 环境安装依赖、执行 `npm run build`，然后运行 `npm run deploy`。
4. `hexo-deployer-git` 根据 `_config.yml` 把生成内容推送到 `gh-pages`。
5. 在 GitHub Actions 确认工作流成功，再访问公开页面。

工作流使用名为 `SSH_PRI` 的 GitHub Actions Secret。Secret 只存放在 GitHub 仓库设置中，不应出现在文章、日志、配置或提交里。

### 手动直接部署

只有在明确需要绕过自动发布、已经完成构建验证，并确认本机 SSH 能访问目标仓库时才运行：

```powershell
npm run clean
npm run build
npm run deploy
```

此操作会直接改变远端 `gh-pages`，不能当作普通的本地检查命令。

## 常见问题

### 页面还是旧内容

先停止本地服务，然后运行：

```powershell
npm run clean
npm run server
```

如果线上仍是旧内容，检查 GitHub Actions、`gh-pages` 最新提交和浏览器/CDN 缓存。

### `YAMLException` 或 Front Matter 解析失败

- 检查开头和结尾的 `---`。
- 检查缩进是否只用空格。
- 包含冒号、`#`、方括号的字符串加引号。
- 列表项保持相同缩进。

### 端口 4000 被占用

```powershell
npx hexo server -p 5000
```

### 图片本地或线上不显示

- 检查目录名和文件名大小写。
- 检查 Markdown 相对路径是否包含文章资源目录。
- 注意本地依赖中没有锁定 `hexo-asset-img`，而 CI 会临时安装它；本地与 CI 行为可能不同。
- 查看生成后的 `public/` 是否存在目标文件。

### 文章 URL 变化或 404

检查 `abbrlink` 是否被删除或修改。已发布文章应保留原值；若必须迁移 URL，需要额外设计重定向，而不是只改 Front Matter。

### GitHub Actions 无法推送 `gh-pages`

检查 `SSH_PRI` 是否存在、私钥是否与有写权限的公钥配对、Deploy Key 或账户权限是否有效，以及 `_config.yml` 中仓库地址和分支是否正确。不要把私钥打印到日志中排查。

## 官方资料入口

- [Hexo 写作](https://hexo.io/zh-cn/docs/writing)
- [Hexo Front Matter](https://hexo.io/zh-cn/docs/front-matter)
- [Hexo 配置](https://hexo.io/zh-cn/docs/configuration)
- [Hexo 生成](https://hexo.io/zh-cn/docs/generating)
- [Hexo 本地服务器](https://hexo.io/zh-cn/docs/server)
- [Hexo 一键部署](https://hexo.io/zh-cn/docs/one-command-deployment)
- [Butterfly 主题文档](https://butterfly.js.org/)
