# 仓库说明

## 项目

- 本仓库是 `https://zrongqing.github.io/` 的源码仓库，使用 Hexo 和 Butterfly 主题构建；版本以 `package*.json` 为准。
- 以 `source/`、`scaffolds/`、`_config.yml`、`_config.butterfly.yml` 和 `package*.json` 为准。
- 不得通过手动修改 `public/` 或已纳入 Git 跟踪的旧版生成 HTML/CSS/JS 文件来更改博客内容。

## 博客维护

- 撰写文章、配置 Front Matter、处理图片、预览、构建、排查问题及发布时，使用仓库技能 `.agents/skills/hexo-blog-local/SKILL.md`。
- 保留与当前任务无关的用户修改和未跟踪文件。开始工作前和完成工作后，都要检查 `git status --short --branch`。
- 除非用户要求使用其他语言，否则技术文章以中文为主。不得编造命令、输出、版本、引用来源或验证结果。
- 保留已发布文章中非空的 `abbrlink` 和原始 `date`。对现有文章进行实质性修改时，更新 `updated`。
- 不得提交机密信息、`node_modules/`、`public/`、`db.json`、`.deploy*/` 或日志。

## 验证

- 通过 `npm run ...` 或 `npx hexo ...` 使用项目本地命令，不得要求全局安装 Hexo。
- 修改内容或配置后，先运行 `npm run clean`，再运行 `npm run build`。
- 构建后再次检查差异，因为 abbrlink 插件可能会将缺失的值写入文章的 Front Matter。

## 发布

- 默认发布流程为 `main` -> GitHub Actions -> `gh-pages` -> GitHub Pages。
- 除非用户明确要求发布，否则不得运行 `git push`、`npm run deploy` 或任何其他会修改远端状态的命令。
- 绝不得读取、打印或提交 GitHub Actions 机密 `SSH_PRI` 对应的 SSH 私钥。
- 本仓库不包含 NuGet 包。除非另行明确指定要发布的包，否则不得执行 NuGet 发布命令。
