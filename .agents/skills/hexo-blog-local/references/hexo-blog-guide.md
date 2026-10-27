# Hexo 本地维护与 GitHub 发布指南

核对日期：2026-10-27。依据 Hexo 官方 GitHub 仓库、GitHub Pages 文档及本仓库源码整理。下面分别标明通用做法、仓库现状与待改进项；版本和配置变更后，应重新读取实际文件，不能把本文快照当成配置源。

## 目录

- [通用做法与仓库现状](#通用做法与仓库现状)
- [依赖与本地命令](#依赖与本地命令)
- [文章与草稿](#文章与草稿)
- [Front Matter 与稳定链接](#front-matter-与稳定链接)
- [图片与静态资源](#图片与静态资源)
- [构建检查](#构建检查)
- [GitHub Pages 发布](#github-pages-发布)
- [排错与资料](#排错与资料)

## 通用做法与仓库现状

[Hexo 官方 starter](https://github.com/hexojs/hexo-starter) 将文章、配置与模板作为源码，用 npm 脚本调用项目依赖，并忽略生成物。官方文档和 GitHub Actions 的具体示例会随时间变化，复用时应核对运行环境与发布方式。

| 方面 | 通用做法 | 本仓库现状 |
| --- | --- | --- |
| 源文件 | 维护 `source/`、`scaffolds/`、配置与依赖清单 | 同时保留部分历史生成页面，不能用它们修改博客 |
| 依赖 | 提交 `package.json` 和锁文件；已有锁文件时用 `npm ci` 复现 | 本地可按锁文件安装；现有 CI 使用 `npm install` 并临时加装图片插件 |
| 主题配置 | 将站点设置与主题覆盖分开管理 | 站点用 `_config.yml`，主题覆盖用 `_config.butterfly.yml` |
| 草稿 | 用 `source/_drafts/`，本地按需显示 | `render_drafts: false`；草稿不等于私密内容 |
| 图片 | 公共资源目录或配置完整的文章资源目录，选定后验证生成 URL | `post_asset_folder: false`，历史文章仍有同名资源目录 |
| 发布 | 源码提交与静态产物分离；构建成功后部署 | `main` → GitHub Actions → `gh-pages` → GitHub Pages |

### 当前环境快照

| 项目 | 核对值 | 依据 |
| --- | --- | --- |
| Hexo | `8.1.2` | `package-lock.json`；清单声明 `^8.1.2` |
| Butterfly | `5.4.2` | 锁文件；清单声明 `^5.4.2` |
| Markdown 渲染器 | `hexo-renderer-marked 7.0.1` | 锁文件 |
| Node.js | 项目要求 `>=20.19.0`；CI 选择 `22.x` | `package.json` 与工作流 |
| 站点与链接 | `https://zrongqing.github.io/`；`posts/:abbrlink/` | `_config.yml` |
| 日期 | `timezone: UTC`，`updated_option: mtime` | `_config.yml` |

遇到旧文档写“Hexo 7”或其他版本时，以清单、锁文件和实际安装结果核对。CI 设置系统时区不等于改写 Hexo 的 `timezone`；日期格式与时区调整需单独评估，保留已发布文章的原始 `date`。

## 依赖与本地命令

以下命令均从仓库根目录运行，示例使用 PowerShell，调用项目内 Hexo，不要求全局安装 CLI。

首次准备或需要重建依赖时：

```powershell
node --version
npm --version
npm ci
```

[`npm ci`](https://docs.npmjs.com/cli/v11/commands/npm-ci/) 会重新安装锁定依赖，不更新清单和锁文件；二者不一致时会失败。应先定位差异，不用删除锁文件、升级依赖或切换全局 Hexo 绕过错误。只有有意新增、升级依赖时才更新清单与锁文件。日常写文章不必每次重装。

| 操作 | 本仓库命令 | 作用 |
| --- | --- | --- |
| 新文章 | `npx hexo new post "文章标题"` | 使用 post scaffold 创建源文件 |
| 草稿 | `npx hexo new draft "文章标题"` | 创建到 `source/_drafts/` |
| 独立页面 | `npx hexo new page "about"` | 创建页面；已有页面不要重复创建 |
| 草稿转文章 | `npx hexo publish "草稿文件名"` | 文件名不含扩展名；移动到正式文章目录 |
| 清理 | `npm run clean` | 删除缓存和 `public/` |
| 构建 | `npm run build` | 生成静态站点，插件也可能回写源文件 |
| 预览 | `npm run server` | 默认 `http://localhost:4000`，监听源文件 |
| 预览草稿 | `npx hexo server --draft` | 在本地显示草稿 |
| 改预览端口 | `npx hexo server -p 5000` | 端口冲突时使用 |
| 直接部署 | `npm run deploy` | 按 deploy 配置修改远端，仅用于明确授权的发布 |

`hexo publish` 的“发布”是草稿转正式文章，不会推送 Git，也不会发布网站。站点部署是另一项操作。该语义依据 [Hexo 写作说明](https://github.com/hexojs/site/blob/master/source/docs/writing.md) 及本地命令实现。

## 文章与草稿

1. 开始前检查 `git status --short --branch`，识别用户已有修改。
2. 新文优先由 Hexo 和 scaffold 创建，再编辑实际生成的 Markdown 路径；不要根据标题猜测文件名。
3. 补齐真实标题、日期、适用分类、标签与摘要。清理占位导语、示例标签和无关模板字段。
4. 正文从二级标题开始，代码块写语言名；用 `<!-- more -->` 划定首页摘要。技术文章说明环境、步骤与实际验证结果，结构按题材安排。
5. 更新既有文章时保留原 `date` 和非空 `abbrlink`，实质修改显式更新 `updated`。不要在纯文字修改中顺便迁移 URL、升级主题或更换部署方式。
6. 草稿转为正式文章后重新检查 Front Matter，尤其是日期、URL 与图片；本仓库的 draft scaffold 很简略，不能假定它已补齐正式文章字段。

默认生成不展示草稿；`--draft` 或 `render_drafts` 可改变结果。不要把 `published: false`、未加入导航或草稿目录当作访问控制：源码可能公开，错误的部署参数也可能输出草稿。

想法与会话提炼按需参考 [文章写作指南](article-writing.md)，主题特殊页面按需参考 [Butterfly 技能](../../hexo-butterfly/SKILL.md)。

## Front Matter 与稳定链接

Hexo 支持多种元数据格式，本仓库使用 YAML。标题中有冒号、井号等字符时加普通引号；布尔值使用 `true` / `false`，不要误用字符串或中文弯引号。

下例是本仓库的新文章示例，不是 Hexo 对所有字段的强制要求。日期应填写文章真实时间：

```yaml
---
title: "示例：使用 Hexo 维护博客"
date: 2026-10-27 10:00:00
updated: 2026-10-27 10:00:00
tags:
  - Hexo
categories:
  - Web
  - Hexo
description: "说明本地写作、检查和发布流程。"
abbrlink:
toc: true
aside: true
highlight_shrink: true
---
```

字段分为三类，避免把主题和插件扩展当成核心功能：

| 类别 | 常用字段 | 注意事项 |
| --- | --- | --- |
| Hexo 核心 | `title`、`date`、`updated`、`layout`、`tags`、`categories`、`permalink`、`published`、`disableNunjucks` | 具体默认值见 [官方 Front Matter](https://github.com/hexojs/site/blob/master/source/docs/front-matter.md) |
| 内容描述与主题消费 | `description`、`keywords` | 根据文章填写；不能只靠填关键词保证搜索排名 |
| Butterfly / 插件扩展 | `cover`、`top_img`、`toc`、`aside`、`highlight_shrink`、`abbrlink` | 按安装版本、主题配置和实际渲染核对，不在每篇文章重复完整默认配置 |

`categories: [Web, Hexo]` 是一条“Web → Hexo”分类路径；多个独立路径使用嵌套列表。标签是并列关系：

```yaml
categories:
  - [Web, Hexo]
  - [Tools, Writing]
tags:
  - Hexo
  - GitHub Pages
```

`updated_option: mtime` 会在未显式填写 `updated` 时使用文件修改时间，Git 检出可能影响这一值，因此既有文章的重要更新时间应显式记录。`disableNunjucks: true` 会同时影响标签外挂；仅在需要展示原始模板语法时考虑，不作为全站排错开关。

### 本仓库的 abbrlink 行为

`abbrlink` 来自 `hexo-abbrlink`，不是 Hexo 内建字段。当前算法是 crc32，输出十六进制值；新文章可留空，生成后保留。不要随意设置单篇 `permalink` 覆盖稳定链接。

按已安装的 `hexo-abbrlink 2.2.1` 的 `lib/logic.js` 核对：文章缺少 abbrlink 或开启 `force` 时，插件会在渲染阶段生成并写回 Markdown；目录分类处理也位于该写回分支。因此：

- 构建与预览都可能改变源文件，不能把生成视作完全只读。
- 当前 `auto_category.enable` 和 `over_write` 均为 true，新链接生成时也需检查分类变化。
- 不应承诺移动目录会让全部既有文章自动重算分类，也不应把 `auto_add` 注释当作插件行为的依据。
- 不启用 `force` 来修复普通排版问题；链接迁移需要逐篇评估旧链接与重定向。

## 图片与静态资源

[Hexo 官方资源目录说明](https://github.com/hexojs/site/blob/master/source/docs/asset-folders.md) 提供公共目录和文章资源目录两种方案。不要混用路径规则后只凭 Markdown 编辑器预览判断成功。

### 公共目录：适合当前配置的新图片

本仓库站点位于域名根路径。无需启用文章资源目录即可使用：

```text
source/images/hexo-local/screenshot.png
```

```markdown
![本地预览截图](/images/hexo-local/screenshot.png)
```

生成后应存在 `public/images/hexo-local/screenshot.png`。使用 `source/images/<文章标识>/` 能避免文件名冲突；CSS 与浏览器脚本可同样放在 `source/css/`、`source/js/`。若站点将来迁到项目子路径，需要同时核对 `url`、`root` 和资源路径处理。

### 文章资源目录：需要完整配置后采用

若决定使用文章同名资源目录，可评估官方方案：

```yaml
post_asset_folder: true
marked:
  prependRoot: true
  postAsset: true
```

这些是待评估的站点配置，当前仓库未启用。启用后，Hexo 可为新文章创建同名目录，marked 可将文章图片相对路径转换为文章资源 URL；也可使用核心 `asset_img` 标签。具体选项需对照 [hexo-renderer-marked](https://github.com/hexojs/hexo-renderer-marked#options) 的安装版本。

采用前要验证已有嵌套文章、首页摘要、文章页和自定义永久链接，不能只改一个开关或批量改路径。

### 历史图片的已知差异

现有文章有 `source/_posts/<文章文件名>/` 和相对链接，但 `post_asset_folder` 为 false；CI 另行安装 `hexo-asset-img`，清单与锁文件没有该依赖。不能据此认定本地、线上图片均已正常，也不能把 CI 临时安装当作完整资源方案。

维护旧文时先检查源文件、生成后的文件与 HTML 图片 URL，保留已验证的链接。若需统一方案，应另行处理依赖、配置和历史资源迁移，并提交锁文件；本指南更新本身不执行迁移。

## 构建检查

本仓库要求内容或配置修改后执行：

```powershell
npm run clean
npm run build
git status --short --branch
git diff --check
git diff
```

检查构建退出状态和相关警告；确认 abbrlink / 分类回写属于目标文章，且 `public/`、`db.json`、`.deploy*/`、日志和依赖目录不进入源码提交。GitHub 上的通用做法是提交源文件，由 CI 重新生成产物。

需要视觉检查时再运行 `npm run server`，验证首页摘要、文章页、目录、代码块、图片和移动端。构建成功只证明生成完成，不能单独证明资源 URL、浏览器交互或线上部署正确。不要把未运行的验证写成“已通过”。

## GitHub Pages 发布

只有用户明确要求推送或发布时才执行远端操作。日常文稿整理和本地预览不会自动授权发布。

### 两种流程分别理解

| 流程 | 产物如何上线 | 配置重点 |
| --- | --- | --- |
| GitHub Actions 直接部署 | 构建 `public/` → 上传 Pages artifact → 部署 Pages | Pages 来源选 GitHub Actions；部署作业设置对应权限与环境 |
| 生成物分支发布 | 构建 `public/` → 推送到 `gh-pages` 等分支 → Pages 从分支发布 | Pages 来源选择实际发布分支；配置分支写权限与凭据 |

[Hexo 官方 GitHub Pages 指南](https://github.com/hexojs/site/blob/master/source/docs/github-pages.md) 展示 artifact 部署，也提供 Git deployer 方案。[GitHub Pages 自定义工作流说明](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages) 列出 Pages 权限与环境要求。选择其中一套完整流程，不把两套配置拼接使用，也不把历史示例中的 Action 主版本永久写成项目要求。

### 本仓库当前流程

1. 在本地完成检查，只提交任务相关源码。
2. 获得推送或发布授权后推送 `main`。
3. `.github/workflows/deploy.yml` 使用 Node.js 22，执行 `npm install`、临时安装图片插件，再运行 `npm run build` 和 `npm run deploy`。
4. `hexo-deployer-git` 按 `_config.yml` 推送到 `gh-pages`。这是分支发布流程，不能仅因“使用 Actions”就推断 Pages 来源应改为 GitHub Actions。
5. 发布后核查工作流、目标分支和线上目标页面；仓库文件只能证明配置意图，Pages 设置与线上结果需要发布时实际确认。

工作流引用 `SSH_PRI` Secret。排错可检查 Secret 是否配置、Deploy Key 权限和错误日志，不读取、打印或提交私钥。

### 直接部署与后续改进

明确需要本机直接部署时，先 clean/build 并检查差异，再运行 `npm run deploy`。不能假定该命令总会重新构建：本地 Hexo 的 deploy 命令在已有 `public/` 且未指定生成选项时可能复用旧产物。

[`hexo-deployer-git`](https://github.com/hexojs/hexo-deployer-git) 会用生成物更新发布分支；直接在发布分支编辑的文件可能被后续部署覆盖。持久静态文件应写在 `source/`；分支发布需要自定义域名时，按对应流程维护 `source/CNAME` 与 Pages 设置。

可单独改进的事项包括：把运行依赖及其锁定版本纳入清单，让本地与 CI 使用一致的安装方式，以及需要时迁到 Pages artifact 部署。这些是后续选择，不是当前已完成的改动。

## 排错与资料

| 现象 | 优先检查 |
| --- | --- |
| YAML 异常 | 引号、冒号、缩进、重复键和实际值类型；语法通过不代表主题识别该值 |
| 新文章或草稿不显示 | 实际路径、layout、published、draft 参数和日期，不先重置日期或 URL |
| 图片不显示 | 大小写、源文件、生成物和 HTML URL，以及本地 / CI 的插件差异 |
| 链接 404 | 原 abbrlink 是否变更、站点 root 与部署位置，不重新批量生成链接 |
| 页面仍是旧内容 | 停止旧预览进程，clean 后重建；线上再核查发布结果和缓存 |
| CI 部署失败 | 定位失败步骤，区分构建、推送、Pages 发布与权限问题，不打印凭据 |

按需继续阅读：

- [Hexo 官方配置说明](https://github.com/hexojs/site/blob/master/source/docs/configuration.md)：目录、URL、时区和配置覆盖。
- [Butterfly 配置技能](../../hexo-butterfly/SKILL.md)：主题选项、特殊页面和标签外挂。
- [Hexo 主题与扩展开发](../../hexo-theme-development/SKILL.md)：模板、Helper、Filter、Generator 与浏览器交互。
