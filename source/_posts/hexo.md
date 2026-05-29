---
title: hexo
tags:
  - hexo
categories:
  - hexo
keywords:
  - hexo
toc: true
aside: true
highlight_shrink: true
abbrlink: hexo
date: 2025-07-01 23:13:15
description:
---

<!-- 这里是你的文章内容 -->

## 引言
记录一些使用hexo的问题以及解决方案

<!-- more -->  <!-- 摘要分隔符 -->

## HEXO使用

### 本地运行博客

1. 清理缓存（可选，但推荐）：确保生成的是最新内容。
```bash
hexo clean  #
```
这个命令会删除之前生成的缓存文件 (db.json) 和静态文件 (public 文件夹)。

2. 生成静态页面：将你写的 Markdown 文章转换成网站文件。
```bash
hexo generate
```

3. 启动本地服务器预览：在本地启动一个服务器，让你能在浏览器中实时看到博客的样子。
```bash
hexo server
```

4. 效果
执行 hexo server 命令后，命令行终端会显示访问地址。打开浏览器，访问 http://localhost:4000，你的博客就在本地运行起来了。

在预览模式下，你可以随时修改文章或主题配置。保存修改后，需要重新运行 hexo g 和 hexo s（或 Ctrl+C 停止服务后，重新执行 hexo s）才能看到更新。

当你对本地预览的效果感到满意后，就可以使用 hexo deploy (hexo d) 命令将其部署到 GitHub Pages 等平台了。

希望这份指南能帮你顺利地在本地跑起 Hexo 博客。如果遇到任何具体问题，可以随时再问我～

### 创建博客

```powershell
hexo new "我的第一篇文章"
```

创建博客会使用 scaffolds/post 中的模板去自动创建新建的博客，可以根据所选择的主题样式，去设置一些默认参数。

### 自动生成永久链接（abbrlink）

_config.yml 文件添加一下配置：

```yml
permalink: posts/:abbrlink/

# abbrlink 配置
abbrlink:
  alg: crc32  # 算法：crc16(default), crc32
  rep: hex    # 进制：dec(default), hex
  auto_add: true  # 自动为所有文章添加 abbrlink（即使没有设置）
```

```powershell
hexo s
```

在调用命令 "hexo s" 后自动在文章补充自动生成的"abbrlink"字段

## ISSUS


## 结论
总结你的文章...