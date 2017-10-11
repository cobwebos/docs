---
title: "缓存 ASP.NET 会话状态提供程序 |Microsoft 文档"
description: "了解如何将使用 Azure Redis 缓存的 ASP.NET 会话状态存储"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 192f384c-836a-479a-bb65-8c3e6d6522bb
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 05/01/2017
ms.author: sdanie
ms.openlocfilehash: 0f3683939ac9646565a0669e19b4c82811d621fc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="aspnet-session-state-provider-for-azure-redis-cache"></a>ASP.NET 会话状态提供程序 Azure Redis 缓存
Azure Redis 缓存提供的会话状态提供程序可用于在缓存中而非内存或 SQL Server 数据库中存储会话状态。 若要使用 caching 会话状态提供程序，首先配置缓存，，然后配置你的 ASP.NET 应用程序的缓存使用 Redis 缓存会话状态 NuGet 包。

它通常是不现实在真实世界云应用程序中以避免存储某种形式的状态，以便用户会话，但某些方法影响性能和可伸缩性比其他更多。 如果你需要存储状态，最佳解决方案是使状态量保持较小，并将其存储在 cookie 中。 如果这不可行下, 一个最佳解决方案是使用与提供程序实现分布式内存中缓存的 ASP.NET 会话状态。 从性能和可伸缩性的角度来看最差的解决方案是使用数据库支持的会话状态提供程序。 本主题提供有关使用用于 Azure Redis Cache 的 ASP.NET 会话状态提供程序的指南。 有关其他会话状态选项的信息，请参阅[ASP.NET 会话状态选项](#aspnet-session-state-options)。

## <a name="store-aspnet-session-state-in-the-cache"></a>在缓存中存储 ASP.NET 会话状态
若要使用 Redis 缓存会话状态 NuGet 包的 Visual Studio 中配置客户端应用程序，请单击**NuGet 包管理器**，**程序包管理器控制台**从**工具**菜单。

运行以下命令从`Package Manager Console`窗口。
    
```
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> 如果你使用的高级层中的聚类分析功能，则必须使用[RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 或更高版本，否则异常会引发。 将移到 2.0.1 或更高版本是一项重大更改;有关详细信息，请参阅[2.0.0 版重大更改详细信息](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details)。 此文章更新时，此包的当前版本是 2.2.3。
> 
> 

Redis Session State Provider NuGet 程序包对 StackExchange.Redis.StrongName 程序包有依赖。 如果 StackExchange.Redis.StrongName 程序包不存在于你的项目中，它被安装。

>[!NOTE]
>除了具有强名称 StackExchange.Redis.StrongName 程序包，还有 StackExchange.Redis 非强命名版本。 如果你的项目使用的是非强命名 StackExchange.Redis 版本则必须卸载它，否则出现命名冲突项目中。 有关这些程序包的详细信息，请参阅[配置.NET 缓存客户端](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)。
>
>

NuGet 程序包下载并添加所需的程序集引用并添加以下会话到 web.config 文件。 本部分包含所需的配置的 ASP.NET 应用程序，以使用 Redis 缓存会话状态提供程序。

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
    <!--
    <add name="MySessionStateStore"
           host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "0" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "5000" [number]
    />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
</sessionState>
```

注释的部分提供特性和每个属性的示例设置的示例。

在 Microsoft Azure 门户中，你缓存边栏选项卡中的值配置属性，并根据需要配置其他值。 有关访问缓存属性的说明，请参阅[配置 Redis 缓存设置](cache-configure.md#configure-redis-cache-settings)。

* **主机**– 指定缓存终结点。
* **端口**– 使用非 SSL 端口或 SSL 端口，具体取决于 ssl 设置。
* **accessKey** – 使用缓存的主密钥或辅助密钥。
* **ssl** – 如果你想要保护缓存/客户端通信使用 ssl，则返回 true; 否则为 false。 请务必指定正确的端口。
  * 默认情况下，为新缓存禁用非 SSL 端口。 指定为 true，此设置以使用 SSL 端口。 有关启用非 SSL 端口的详细信息，请参阅[访问端口](cache-configure.md#access-ports)主题中[配置缓存](cache-configure.md)主题。
* **throwOnError** – 如果你想如果没有失败，则为 false 如果你想要以无提示方式失败的操作引发异常，则返回 true。 可以通过检查静态 Microsoft.Web.Redis.RedisSessionStateProvider.LastException 属性来检查失败。 默认值为 true。
* **retryTimeoutInMilliseconds** – 以毫秒为单位指定此时间间隔内重试失败的操作。 首次重试发生 20 毫秒后，然后重试出现每秒，直到 retryTimeoutInMilliseconds 间隔到期。 此间隔后立即重试操作时最后一次。 如果操作仍失败，则会向调用方，具体取决于 throwOnError 设置重新引发异常。 默认值为 0，这意味着不重试。
* **databaseId** – 指定要用于缓存输出数据的数据库。 如果未指定，使用默认值为 0。
* **applicationName** – 密钥存储在 redis 中作为`{<Application Name>_<Session ID>}_Data`。 此命名方案使多个应用程序可以共享相同的 Redis 实例。 此参数是可选的如果未提供使用默认值。
* **connectionTimeoutInMilliseconds** – 此设置允许你覆盖 StackExchange.Redis 客户端中的 connectTimeout 设置。 如果未指定，使用默认 connectTimeout 设置 5000。 有关详细信息，请参阅[StackExchange.Redis 配置模型](http://go.microsoft.com/fwlink/?LinkId=398705)。
* **operationTimeoutInMilliseconds** – 此设置允许你覆盖 StackExchange.Redis 客户端中的 syncTimeout 设置。 如果未指定，使用默认 syncTimeout 设置 1000年。 有关详细信息，请参阅[StackExchange.Redis 配置模型](http://go.microsoft.com/fwlink/?LinkId=398705)。

有关这些属性的详细信息，请参阅在原始的博客文章公告[宣布推出适用于 Redis ASP.NET 会话状态提供](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)。

不要忘记将注释掉标准 InProc 会话状态提供程序中的一节 web.config。

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

一旦执行这些步骤，你的应用程序配置为使用 Redis 缓存会话状态提供程序。 当应用程序中使用会话状态时，则将它存储在 Azure Redis 缓存实例。

> [!IMPORTANT]
> 在缓存中存储的数据必须是可序列化，与可以存储在默认的数据不同内存中 ASP.NET 会话状态提供程序。 当使用 Redis 会话状态提供程序时，应确保在会话状态中存储的数据类型都可序列化。
> 
> 

## <a name="aspnet-session-state-options"></a>ASP.NET 会话状态选项
* 在内存会话状态提供程序-此提供程序存储在内存中会话状态。 使用此提供程序的好处是可以很简单和快速。 但是，如果使用内存中提供程序，由于它不是分布式的，因此不能缩放 Web 应用。
* Sql Server 会话状态提供程序-此提供程序将会话状态存储在 Sql Server。 如果你想要在持久性存储区中存储会话状态，请使用此提供程序。 可以缩放你的 Web 应用，但将 Sql Server 用于会话上你的 Web 应用会影响性能。
* 分布式内存中会话状态提供程序如 Redis 缓存会话状态提供程序-此提供程序提供两全其美的最佳。 你的 Web 应用可以有一个简单、 快速，且可缩放会话状态提供程序。 由于此提供程序将会话状态存储在缓存中，你的应用程序必须仔细考虑到分布式中内存缓存，如暂时性网络故障通信时关联的所有特征。 有关使用缓存的最佳实践，请参阅[缓存指导原则](../best-practices-caching.md)从 Microsoft 模式和实践[Azure 云应用程序设计和实施指导原则](https://github.com/mspnp/azure-guidance)。

有关会话状态和其他最佳实践的详细信息，请参阅[Web 开发最佳做法 （构建真实世界云应用程序与 Azure）](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices)。

## <a name="next-steps"></a>后续步骤
签出[Azure Redis 缓存的 ASP.NET 输出缓存提供程序](cache-aspnet-output-cache-provider.md)。

