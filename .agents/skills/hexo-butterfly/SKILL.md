---
name: hexo-butterfly
description: 配置本仓库的 Butterfly 主题导航、侧栏、代码块、搜索、评论和特殊页面，使用 Note、Tabs 等标签外挂并排查主题配置问题。普通文章写作与发布使用 hexo-blog-local；模板和插件代码开发使用 hexo-theme-development。
---

# Butterfly 主题配置

## 开始工作

1. 读取根目录 `AGENTS.md`，运行 `git status --short --branch`，保留已有改动。
2. 按任务读取 `_config.yml`、`_config.butterfly.yml`、`package.json` 与锁文件。版本以锁文件为准；涉及渲染实现时再检查本地安装版本，避免把陈旧依赖当成项目现状。
3. 普通文章、Front Matter、图片与构建发布，使用同仓库的 [hexo-blog-local](../hexo-blog-local/SKILL.md)。不重复维护另一套发布流程。

## 按问题读取参考

- 导航、侧栏、代码块、搜索、评论、样式注入或配置不生效：读取 [references/configuration.md](references/configuration.md)。
- 标签页、分类页、友链页和文章中的 Note、Tabs、折叠块：读取 [references/pages-and-tags.md](references/pages-and-tags.md)。
- 需要新增模板、Helper、渲染钩子或页面交互：使用 [hexo-theme-development](../hexo-theme-development/SKILL.md)。

## 修改原则

- 项目通过 npm 使用主题。`node_modules/hexo-theme-butterfly/` 仅用于核对默认项与实现；持久改动写入仓库跟踪的配置、`source/` 或扩展代码。
- `_config.butterfly.yml` 保存需要覆盖的设置，不用整份社区示例覆盖现有配置。先核对相同父键，避免创建重复 YAML 键。
- 区分站点配置、主题配置和单页 Front Matter。文章级设置不应通过修改全站默认值实现。
- 不因一项配置问题顺便升级主题、安装依赖或接入评论、统计等外部服务。需要外部服务时沿用用户指定的提供方与现有配置。
- 文章链接沿用 `abbrlink`，保留原始 `date`；页面路径和菜单大小写需同时兼容 Windows 与 Linux CI。

## 验证

修改内容或配置后，先 `npm run clean`，再 `npm run build`。重新检查状态和差异，确认 Front Matter 回写属于任务范围。需要视觉检查时使用 `npm run server`，检查受影响页面、移动端与深浅色模式；只报告实际检查过的结果。

发布仍走 `main` → GitHub Actions → `gh-pages`，只有用户明确要求推送或发布时才改变远端状态。

## 资料依据

2026-10-27 参考 [daojiAnime/DaojiBlog 的 hexo-butterfly](https://github.com/daojiAnime/DaojiBlog/tree/df3ddb0bf35e7df3a1b292a9bbfd182f6ce7f364/.claude/skills/hexo-butterfly)，结合本仓库 Butterfly 5.4.2 代码与 [官方主题文档](https://butterfly.js.org/) 重新编写。后续版本的字段应重新核对，不能直接套用此处示例。
