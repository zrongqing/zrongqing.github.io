---
name: maintain-hexo-blog
description: 维护本仓库的 Hexo 与 Butterfly 静态博客。用于新建、续写、校对或重构 Markdown 文章，配置 Front Matter 和文章图片，排查本地预览或构建问题，准备或执行 GitHub Pages 发布，以及解释本仓库常用 Hexo 命令和字段；不用于 NuGet 包发布，也不用于直接维护 Hexo 生成的 HTML。
---

# 维护 Hexo 博客

## 先确认仓库状态

1. 读取根目录 `AGENTS.md`、`package.json`、`_config.yml`、`_config.butterfly.yml` 和相关 scaffold。
2. 运行 `git status --short --branch`，保留用户已有修改和未跟踪文件。
3. 涉及命令、Front Matter、图片、构建或发布时，先完整读取 [references/hexo-blog-guide.md](references/hexo-blog-guide.md)。
4. 使用项目内安装的 Hexo：优先运行 `npm run ...` 或 `npx hexo ...`，不要依赖全局 `hexo`。

## 编写或维护文章

1. 新文章优先用 `npx hexo new post "<标题>"` 创建，随后编辑 `source/_posts/` 中的 Markdown；草稿使用 `npx hexo new draft`。
2. 延续仓库以中文为主的技术博客风格：先说明问题和适用环境，再给步骤、代码、验证结果与结论。
3. 不编造版本、命令输出、测试结果、引用或故障原因。信息不足时明确标记待确认内容。
4. 写有效 YAML Front Matter。新文章至少设置 `title`、`date`、`tags`、`categories`、`keywords`、`description`，并保留仓库模板中的阅读选项。
5. 让新文章的 `abbrlink` 保持为空，由现有插件在构建时生成；已有文章的非空 `abbrlink` 视为稳定公开 URL，除非用户明确要求迁移，否则不要修改。
6. 使用 `<!-- more -->` 控制首页摘要；使用带语言名的 fenced code block；命令示例注明运行目录和平台差异。
7. 编辑已发布文章时更新 `updated`。不要改变原 `date`，除非用户明确要求重设发布日期。
8. 文章图片沿用仓库现有的 `source/_posts/<文章文件名>/` 目录和相对链接写法；移动或重命名文章时同步检查图片路径。

## 校验

1. 检查 Front Matter 的 YAML 语法、标题层级、链接、图片路径、代码块闭合和是否泄露密钥或个人敏感信息；不得保留 `tag1`、`category1`、`keyword1` 等 scaffold 占位值。
2. 运行 `npm run clean`，再运行 `npm run build`。首次安装依赖时使用 `npm ci`。
3. 构建后重新检查 `git status` 和差异；`hexo-abbrlink` 可能为缺少链接的文章回写 `abbrlink`，确认这些改动属于目标文章。
4. 需要视觉确认时运行 `npm run server`，访问 `http://localhost:4000`；预览草稿时使用 `npx hexo server --draft`。
5. 不提交 `node_modules/`、`public/`、`db.json` 或 `.deploy*/`。

## 发布

1. 默认发布链路为：提交博客源码到 `main`，推送后由 `.github/workflows/deploy.yml` 构建并将静态站点部署到 `gh-pages`。
2. 只有用户明确要求推送或发布时才执行 `git push`、`npm run deploy` 或任何会改变远端状态的操作。
3. 优先采用推送 `main` 的自动发布链路。仅在用户明确要求直接部署且本机 SSH 凭据可用时运行 `npm run deploy`。
4. 不读取、打印或提交 SSH 私钥。工作流只引用 GitHub Actions Secret `SSH_PRI`。
5. 发布后检查 GitHub Actions 结果、`gh-pages` 更新和 `https://zrongqing.github.io/` 的目标页面。

## 边界

- 把 `source/`、scaffold 和 Hexo 配置视为内容源；不要手工编辑 `public/` 或根目录中遗留的生成页面来修改博客。
- 这是 GitHub Pages 静态站点，不是 NuGet 包。除非用户另行提供 `.csproj`、`.nuspec`、包 ID 和 NuGet 源，否则不要设计或执行 `dotnet nuget push`。
- 未经明确请求，不升级 Hexo、Butterfly、Node.js 或依赖，不改部署凭据与 GitHub Pages 分支。
