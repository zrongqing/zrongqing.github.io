---
title: .NET Confuse
tags:
  - .NET
  - Confuse
categories:
  - .NET
keywords:
  - 混淆
toc: true
aside: true
highlight_shrink: true
abbrlink: 7f4ee469
date: 2025-10-13 11:07:30
updated: 2025-10-13 11:07:30
description:
---

## 引言

.Net 混淆相关知识点。
如今有了AOT后，混淆在现在的场景用处不是特别大，但是还是在这里做一个记录。

## obfuscar

github地址：[obfuscar](https://github.com/obfuscar/obfuscar)  
例子：[example](https://github.com/obfuscar/example)

obfuscar混淆的工程文件是.xml文件，具体含义可 [参考](https://docs.lextudio.com/obfuscar/getting-started/configuration)
```xml
<?xml version="1.0" encoding="utf-8"?>
<Obfuscator>
  <Var name="InPath" value="bin\Release\net8.0\" />
  <Var name="OutPath" value="bin\Obfuscated\" />
  
  <Module file="$(InPath)\ClassLibrary1.dll" />
</Obfuscator>
```

相较于非SDK风格的混淆方式，在官方文档较为详细，在此不作额外说明，详细讲解一下dot中的部署。

---

```powshell
// 安装混淆工具
dotnet tool install --global Obfuscar.GlobalTool
```

%USERPROFILE%\.dotnet\tools 将这个配置到电脑的环境变量中，并且存在 obfuscar.console.exe

```powshell
// 进行混淆
obfuscar.console obfuscar.xml
```

将会按照 obfuscar.xml 配置执行相关流程

### obfuscar issue

#### 提示格式不正确

[原因之一](https://www.google.com.hk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=&ved=2ahUKEwjYxOTWqKOQAxVMyDgGHarQJHcQFnoECBsQAQ&url=https%3A%2F%2Fgithub.com%2Fobfuscar%2Fobfuscar%2Fissues%2F287&usg=AOvVaw0icCnBFrCk9AVRYJ_HtRoo&opi=89978449)

多出现与直接混淆 exe 项目，因为在net以后，.exe 并非是常规的程序集，obfuscar不支持混淆；

## 正文内容
这里是文章的详细内容...

## 结论
总结你的文章...