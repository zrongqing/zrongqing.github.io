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
updated: 2025-07-01 23:13:15
description:
---

<!-- 这里是你的文章内容 -->

## 引言
记录一些使用hexo的问题以及解决方案

<!-- more -->  <!-- 摘要分隔符 -->

## HEXO使用

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