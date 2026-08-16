# zrongqing.github.io

这是 [ZhangRongqing 的个人博客](https://zrongqing.github.io/) 源码仓库，使用 Hexo 7 和 Butterfly 主题构建。

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

完整的 Hexo 命令、Front Matter 字段、图片规范、排错和发布说明见 [Hexo 博客维护指南](.agents/skills/maintain-hexo-blog/references/hexo-blog-guide.md)。在 Codex 中也可以直接使用 `$maintain-hexo-blog` 来编写或维护文章。
