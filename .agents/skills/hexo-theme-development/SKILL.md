---
name: hexo-theme-development
description: 为本仓库开发 Hexo 模板、Helper、Filter、Generator、自定义标签和浏览器交互，处理模板变量、国际化及扩展代码问题。Butterfly 现成配置使用 hexo-butterfly；普通文章写作、构建与发布使用 hexo-blog-local。
---

# Hexo 主题与扩展开发

## 确定扩展位置

1. 读取根目录 `AGENTS.md`，检查 `git status --short --branch`，保留已有修改。
2. 读取相关配置、`package.json` 与锁文件，确认 Hexo、主题、渲染器版本。仅检查当前任务涉及的模板与扩展实现。
3. 优先使用已有主题配置；需要加载 CSS 或浏览器 JS 时先检查 [hexo-butterfly](../hexo-butterfly/SKILL.md) 的注入方式。
4. 确需代码时读取 [references/development.md](references/development.md)，选择最小扩展点。将代码存放在仓库源文件中，不通过编辑生成物或 npm 安装目录实现功能。

## 本仓库约束

- Butterfly 来自 npm，默认模板位于 `node_modules/hexo-theme-butterfly/layout/`，主要使用 Pug。不要因为通用示例使用 EJS 而替换现有渲染体系。
- 构建期扩展写入根目录 `scripts/`；浏览器执行的资源放到 `source/` 下并显式加载。两种运行环境不能混用。
- 必须改变模板结构时，先确认主题或 Hexo 提供的可维护扩展机制。若只能采用主题分支或受版本控制的补丁，说明变更范围与升级成本，按用户任务选择方案；不要直接修改 `node_modules/`，也不要默默复制整套主题。
- 页面 URL 沿用 `posts/:abbrlink/`。Generator、Filter 和导航改造不能意外重写既有 URL、原始日期或批量文章内容。
- 不因编写一个扩展而自动安装测试框架、升级渲染器、发布 npm 包或发布主题。

## 验证与交付

- 内容、配置或扩展发生改变后，先 `npm run clean`，再 `npm run build`；随后检查状态和差异，确认没有无关 Front Matter 回写。
- 根据改动检查生成路由、资源与受影响页面。浏览器交互另需验证事件、移动端及启用 PJAX 时的页面切换。
- 对有分支逻辑、内容转换或路由生成的代码，验证真实边界与失败情况；简单配置或样式不要求新增测试框架。
- 说明实际改动、验证结果和未验证项。构建、预览与发布流程使用 [hexo-blog-local](../hexo-blog-local/SKILL.md)；发布仅在用户明确要求时执行。

## 资料依据

2026-10-27 参考 [hsiangfeng/hexo-skills](https://github.com/hsiangfeng/hexo-skills/tree/602e4fbf9be83aff344f4c3b3c515014d14d4415/hexo-skills)，按本仓库 npm 主题结构与本地命令重新编写，保留对模板、变量、Helper 和扩展 API 的按需指导。
