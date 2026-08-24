---
title: Prism.DryIoc使用Microsoft.Extensions.Hosting
tags:
  - tag1
  - tag2
categories: uncategorized
keywords:
  - Prism
  - DryIoc
  - Microsoft
  - DependencyInjection
  - Hosting
toc: true
aside: true
highlight_shrink: true
abbrlink: cd3ab45e
date: 2026-05-31 16:53:03
updated: 2026-05-31 16:53:03
description:
---

<!-- 这里是你的文章内容 -->

## 引言
Prism.DryIoc中试用微软的DI全家桶服务怎么做？  

<!-- more -->  <!-- 摘要分隔符 -->
所使用的NUGET包
```csproj
  <ItemGroup>
    <PackageReference Include="DryIoc.Microsoft.DependencyInjection" Version="6.2.0" />
    <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="10.0.8" />
    <PackageReference Include="Prism.DryIoc" Version="9.0.537" />
  </ItemGroup>
```

**为什么要这么做？**  
DroIoc速度快，可以动态注册；Hosting注册后，就不能够再动态注册了。  
就目前微软的生态来说，使用Hosting的配置比较多。  
所以可以在Prism这样桥接一下。

## 正文内容

```csharp
public abstract class DevkitPrismApplication : PrismApplication
{
    protected override IContainerExtension CreateContainerExtension()
    {
        // 适配Microsoft.Extensions.DependencyInjection，将微软容器中的东西加入到DryIoc中
        var microsoftServers = GetPrismServiceCollection();

        Rules ruls = this.CreateContainerRules();
        var container = new DryIoc.Container(this.CreateContainerRules());
        container.WithDependencyInjectionAdapter(microsoftServers);
    
        return new DryIocContainerExtension(container);
    }

    private IServiceCollection _serviceCollection = new ServiceCollection();
    protected virtual IServiceCollection GetPrismServiceCollection() => _serviceCollection;
}

// 其他地方的代码
private void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IFileService, FileService>();
}
```

继承 PrismApplication ，重写 CreateContainerExtension ，创建新的 IServiceCollection ，加入其他微软套件，调用 ‘WithDependencyInjectionAdapter’ 即可将 IServiceCollection 中的全部服务注册到 DryIoc 容器中。  
