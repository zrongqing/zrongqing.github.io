# zrongqing.github.io

这是 [ZhangRongqing 的个人博客](https://zrongqing.github.io/) 源码仓库，使用 Hexo 和 Butterfly 主题构建，版本以 `package*.json` 为准。

- `main`：文章、配置和 GitHub Actions 源码。
- `gh-pages`：Hexo 生成的静态网站，由工作流自动更新。
- `source/_posts/`：已发布文章。
- `scaffolds/`：新文章、页面和草稿模板。

## 常用命令

```powershell
npm ci
npx hexo new post "文章标题"
npm run clean
npm run build
npm run server
```

本地预览默认地址为 `http://localhost:4000`。推送 `main` 后，`.github/workflows/deploy.yml` 会构建并发布到 `gh-pages`。

Hexo 通用实践、本仓库命令、Front Matter、图片与 GitHub 发布流程见 [Hexo 本地维护指南](.agents/skills/hexo-blog-local/references/hexo-blog-guide.md)。在 Codex 中也可以直接使用 `$hexo-blog-local` 来编写或维护文章。
