# Butterfly 配置与排错

## 配置位置

| 文件或位置 | 负责内容 |
| --- | --- |
| `_config.yml` | 站点 URL、语言、永久链接、渲染高亮器和生成器设置 |
| `_config.butterfly.yml` | 导航、侧栏、目录、代码块外观、搜索入口、评论和注入资源 |
| 单篇 Markdown 的 Front Matter | 本页封面、顶图、目录、侧栏和评论等覆盖项 |
| `source/_data/link.yml` | 友链数据 |
| `node_modules/hexo-theme-butterfly/_config.yml` | 当前安装版本的默认项，只读参考 |

根目录主题覆盖配置会与默认配置合并，未覆盖的项可继承默认值。若站点 `_config.yml` 另有 `theme_config`，还需检查其中更高优先级的覆盖。诊断生效值时检查所有层，而不只看一个 YAML 文件。

## 本仓库易混淆的字段

以下结构按本地 Butterfly 5.4.2 核对，升级后需检查新版本默认配置和实现。

| 需求 | 配置入口 | 核对事项 |
| --- | --- | --- |
| 导航与固定导航 | `menu`、`nav.fixed` | 保留现有菜单；站内路径必须有对应页面 |
| 代码块外观与工具栏 | `code_blocks` | 主题、高度、折叠和复制功能均位于此对象；旧示例的 `highlight_theme`、`code_copy` 不可直接使用 |
| 高亮引擎 | 站点的 `syntax_highlighter`、`highlight`、`prismjs` | 与主题代码块外观设置分开检查 |
| 全站目录与侧栏 | `toc`、`aside` | 单篇 Front Matter 的 `toc`、`aside` 也可能覆盖显示结果 |
| 搜索 | `search.use`、`search.local_search` | 选择 `local_search`；旧示例的顶层 `local_search.enable` 不适用于此结构 |
| 评论 | `comments.use` 与所选系统的配置段 | 文章 `comments: true` 不等于已完成服务配置 |
| 深色模式与局部切换 | `darkmode`、`pjax` | 浏览器自定义交互需同时检查 PJAX 导航后的初始化 |

本仓库已声明 `hexo-generator-searchdb`。如果启用本地搜索，先检查现有生成器配置及生成索引，不重复安装插件。主题覆盖示例：

```yaml
search:
  use: local_search
  local_search:
    preload: false
```

这只是待合入现有 YAML 的示例，不代表当前已经启用本地搜索。

## 自定义 CSS 与浏览器脚本

简单样式或已有脚本加载优先使用主题 `inject`，资源存放在 `source/css/`、`source/js/` 等仓库源目录中。仅在任务需要时创建资源，并将对应引用合入已有列表：

```yaml
inject:
  head:
    - '<link rel="stylesheet" href="/css/custom.css">'
  bottom:
    - '<script src="/js/custom.js" defer></script>'
```

例中应同时存在 `source/css/custom.css` 与 `source/js/custom.js`。不需要的引用不要添加。静态 `.js` 和 `.css` 从源目录生成到站点；不能只在 `public/` 新建文件。单纯配置与资源注入无需复制或修改整套主题模板。

## 排错顺序

1. 比较锁文件与本地主题版本，读取对应版本默认配置；核对键名、缩进、重复父键和 YAML 值的类型。
2. 检查配置属于站点、主题还是单页，确认是否有更高优先级的覆盖。不要把文章中的 `false` 当成字符串 `'false'`。
3. 资源不显示时检查源文件是否存在、大小写、生成路径与页面实际请求地址。导航链接保持现有大小写，例如 `/Gallery/`。
4. 执行 `npm run clean`、`npm run build`；重启预览后检查受影响页面，排除旧进程与缓存。
5. 如果问题只在线上发生，比较 CI 安装过程、构建结果和本地依赖；不要用增加随机配置字段代替定位。

## 资料

- [Butterfly 官方主题配置](https://butterfly.js.org/posts/4aa8abbe/)
- [Hexo 主题配置](https://hexo.io/docs/configuration#Using-an-alternate-config)

实现依据是本地 `scripts/events/merge_config.js`、`layout/includes/head.pug` 和 `layout/includes/additional-js.pug`；路径相对于主题包目录。
