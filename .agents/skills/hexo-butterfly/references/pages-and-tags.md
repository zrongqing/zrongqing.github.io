# 特殊页面与标签外挂

## 特殊页面

先检查现有文件和菜单，避免为同一个功能新建第二套页面。本仓库已有：

| 功能 | 源文件 | Front Matter 的 `type` |
| --- | --- | --- |
| 标签索引 | `source/tags/index.md` | `'tags'` |
| 分类索引 | `source/categories/index.md` | `'categories'` |
| 友情链接 | `source/link/index.md` | `'link'` |

`type` 是精确字符串。YAML 字符串使用普通单双引号，中文弯引号会成为值的一部分。遇到特殊页面退回普通文章布局时，要检查实际解析值，而不只检查 YAML 能否通过语法验证。

仅在目标页面不存在且任务需要时，从仓库根目录执行 `npx hexo new page "页面目录"`，随后设置对应 `type`，并核对导航 URL。修改既有页面时沿用维护技能的日期与链接规则。

友链数据写入 `source/_data/link.yml`，保持其分类列表与 `link_list` 层级；每项使用 `name`、`link`、`avatar`、`descr` 等已有字段。不要用个人示例友链替换用户数据。

## 标签外挂

以下是 Butterfly 渲染语法，普通 Markdown 编辑器不一定能预览。使用前检查本地主题 `scripts/tag/` 下对应注册实现；需完整闭合，代码示例中的标签不要被误执行。

### 提示框

```markdown
{% note info %}
补充说明或注意事项。
{% endnote %}
```

### 选项卡

```markdown
{% tabs 环境选择 %}
<!-- tab Windows -->
Windows 操作说明。
<!-- endtab -->
<!-- tab Linux -->
Linux 操作说明。
<!-- endtab -->
{% endtabs %}
```

同一页面中使用不同的 Tabs 名称，检查生成后的切换按钮与内容是否一一对应。嵌套方式按当前主题支持的 `subtabs` 等实现核对，不假定任意嵌套均可用。

### 折叠内容

```markdown
{% hideToggle 查看详细步骤 %}
需要时展开阅读的内容。
{% endhideToggle %}
```

折叠只是界面行为，内容仍会出现在公开页面中。敏感材料不能靠折叠隐藏。

## 验证目标

- 特殊页面实际使用对应布局，标签、分类或友链数据能显示。
- Note、Tabs、折叠块在生成后的页面正常展示，代码块与摘要边界没有破坏标签闭合。
- 新图片和资源在本地与 Linux CI 的大小写规则下都能定位。
- 仅展示标签源码的文章若发生 Nunjucks 误解析，按维护指南检查 `disableNunjucks` 或局部转义；不要全站关闭模板处理来修复一篇文章。

## 资料

- [Butterfly 官方页面配置](https://butterfly.js.org/posts/dc584b87/)
- [Butterfly 官方标签外挂](https://butterfly.js.org/posts/ceeb73f/)

示例已对照本地 Butterfly 5.4.2 的 `note.js`、`tabs.js` 和 `hide.js` 注册实现。
