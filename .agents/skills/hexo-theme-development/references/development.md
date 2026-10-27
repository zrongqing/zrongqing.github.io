# 模板与扩展实现指南

## 选择扩展点

| 需求 | 优先机制 | 持久源码位置 |
| --- | --- | --- |
| 修改已有外观、菜单或组件开关 | Butterfly 配置 | `_config.butterfly.yml` |
| 加载局部 CSS 或浏览器脚本 | 主题 `inject.head` / `inject.bottom` | 配置与 `source/css/`、`source/js/` |
| 在模板中计算或格式化数据 | Helper | 根目录 `scripts/` |
| 新增文章内的块语法 | Tag | 根目录 `scripts/` |
| 调整渲染前后数据 | Filter | 根目录 `scripts/` |
| 生成额外页面或数据路由 | Generator | 根目录 `scripts/` |
| 按页面类型注入 HTML | Hexo Injector | 根目录 `scripts/` |

根目录 `scripts/` 中的 JavaScript 在 Hexo 初始化时加载。先搜索主题和依赖中已注册的同名扩展，避免无意覆盖；例如 Butterfly 已注册 `note`、`tabs` 等标签。

## 模板环境

| 变量 | 含义 | 使用注意 |
| --- | --- | --- |
| `config` | 站点配置 | 不把主题覆盖项误写到站点字段中 |
| `theme` | 主题配置 | 与 `_config.butterfly.yml` 覆盖后的实际值对应 |
| `page` | 当前页面数据 | 先判断页面类型与字段是否存在 |
| `site` | 全站文章、页面、分类等集合 | Hexo 数据集合不等同于普通数组，使用对应集合 API |

Butterfly 使用 Pug；渲染器决定模板转义方式。文本默认转义，只有受信任的已渲染 HTML 才使用原样输出。内部链接优先通过 `url_for()` 等 Helper 生成。

Helper 需要模板上下文时使用普通函数，以便访问 `this.page`、`this.config` 等值。不要用箭头函数捕获错误的 `this`。模板片段缓存只适合跨页面不变的结果；文章内容、语言或用户状态相关内容应避免共用同一缓存键。

模板文字国际化先检查主题已有 `languages/` 键及 `__()`、`_p()` 的使用。站点的 `language`、菜单里的 `/en/` 链接和简繁转换开关并不自动生成多语言文章；需要翻译内容和路由时另行设计并验证。

## 扩展实现要点

- Filter 选用实际需要的阶段，返回预期数据；内容转换要考虑重复执行，避免重复插入片段。
- Generator 返回包含 `path` 与 `data` 的路由对象；需要主题渲染时再提供 `layout`。检查输出路径与已有文章、分页、标签和静态资源的冲突。
- Tag 先明确输入是纯文本、Markdown 还是 HTML；需要结束标签时设置 `ends: true`，并检查空参数与特殊字符。
- Hexo Injector 支持 `head_begin`、`head_end`、`body_begin`、`body_end`；不要把这些名字与 Butterfly 的 `inject.head`、`inject.bottom` 混用。同一资源只选择一种注入机制。
- 浏览器脚本将初始化写成可重复调用的函数，避免重复添加事件监听或 DOM。若站点启用 PJAX，除首次加载外还需处理实际使用的页面切换事件，并在必要时清理旧实例。
- 异步处理按所用 Hexo API 的约定返回 Promise 或结果，不依赖未等待的后台操作。访问外部服务不是构建的默认前提。

## 有针对性的验证

1. 先对新增 JS 做语法检查，再运行仓库要求的 clean/build；不以语法通过代替行为验证。
2. Helper 与 Filter 检查空值、特殊字符、多次处理；Generator 检查实际生成的路由和内容；Tag 检查正文渲染与闭合。
3. 涉及浏览器的更改检查生成的资源引用、控制台错误、首次访问和站内跳转。只生成 HTML 不能证明交互可用。
4. 检查首页、文章和受影响的特殊页面，避免一个共用模板的改动破坏其他页面类型。
5. 不把临时验证用文章、截图、日志或测试构建输出留在提交范围中。

## 官方资料

- [Hexo 主题目录与资源处理](https://hexo.io/docs/themes)
- [Hexo 模板](https://hexo.io/docs/templates)
- [Hexo 变量](https://hexo.io/docs/variables)
- [Helper API](https://hexo.io/api/helper)
- [Filter API](https://hexo.io/api/filter)
- [Generator API](https://hexo.io/api/generator)
- [Tag API](https://hexo.io/api/tag)
- [Injector API](https://hexo.io/api/injector)

优先查阅安装版本的实现与对应官方文档。上游社区示例用于提供思路，不自动授予安装依赖、修改部署或发布的权限。
