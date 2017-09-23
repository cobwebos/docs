---
title: "缓存 ASP.NET 输出缓存提供程序"
description: "了解如何使用 Azure Redis 缓存来缓存 ASP.NET 页面输出"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/14/2017
ms.author: sdanie
ms.translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 845f25637a0e48460fc76c1ee36060274b3cec38
ms.contentlocale: zh-cn
ms.lasthandoff: 03/22/2017

---
# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>Azure Redis 缓存的 ASP.NET 输出缓存提供程序
Redis 输出缓存提供程序是用于输出缓存数据的进程外存储机制。 此数据专门用于完整 HTTP 响应（页面输出缓存）。 此提供程序会插入 ASP.NET 4 中引入的新输出缓存提供程序扩展点。

要使用 Redis 输出缓存提供程序，首先配置你的缓存，然后使用  Redis 输出缓存提供程序 NuGet 包配置 ASP.NET 应用程序。 本主题提供有关配置应用程序以使用 Redis 输出缓存提供程序的指南。 有关创建和配置 Azure Redis 缓存实例的详细信息，请参阅[创建缓存](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)。

## <a name="store-aspnet-page-output-in-the-cache"></a>在缓存中存储 ASP.NET 页面输出
若要使用 Redis 缓存会话状态 NuGet 包在 Visual Studio 中配置客户端应用程序，请在“工具”菜单中依次单击“NuGet 包管理器”和“包管理器控制台”。

从 `Package Manager Console` 窗口运行以下命令。
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Redis 输出缓存提供程序 NuGet 包依赖于 StackExchange.Redis.StrongName 包。 如果项目中没有 StackExchange.Redis.StrongName 包，则会安装它。 有关 Redis 输出缓存提供程序 NuGet 包的详细信息，请参阅 [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet 页。

>[!NOTE]
>除了强命名的 StackExchange.Redis.StrongName 包外，还有 StackExchange.Redis 非强命名版本。 如果项目使用的是非强命名 StackExchange.Redis 版本，则必须卸载，否则项目中将出现命名冲突。 有关这些包的详细信息，请参阅[配置 .NET 缓存客户端](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)。
>
>

下载 NuGet 包、添加所需的程序集引用并将下面的部分添加到 web.config 文件。 此部分包含 ASP.NET 应用程序使用 Redis 输出缓存提供程序所需的配置。

```xml
<caching>
  <outputCachedefault Provider="MyRedisOutputCache">
    <providers>
      <!--
      <add name="MyRedisOutputCache"
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "5000" [number]
      />
      -->
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
  </outputCache>
</caching>
```

注释部分提供了属性及每个属性的示例设置的一个示例。

在 Microsoft Azure 门户中使用缓存边栏选项卡中的值配置属性，并根据需要配置其他值。 有关访问缓存属性的说明，请参阅[配置 Redis 缓存设置](cache-configure.md#configure-redis-cache-settings)。

* **host** - 指定缓存终结点。
* **port** - 使用非 SSL 端口或 SSL 端口，具体取决于 SSL 设置。
* **accessKey** - 使用缓存的主密钥或辅助密钥。
* **ssl** - 如果要使用 SSL 保护缓存/客户端通信，则为 true；否则为 false。 请务必指定正确的端口。
  * 默认情况下，将为新缓存禁用非 SSL 端口。 为此设置指定 true 可使用 SSL 端口。 有关启用非 SSL 端口的详细信息，请参阅[配置缓存](cache-configure.md)主题中的[访问端口](cache-configure.md#access-ports)部分。
* **databaseId** - 指定要用于缓存输出数据的数据库。 如果未指定，则使用默认值 0。
* **applicationName** - 密钥存储在 redis 中作为 `<AppName>_<SessionId>_Data`。 此类命名架构使多个应用程序可以共享同一密钥。 此参数是可选的，如果未提供它，则使用默认值。
* **connectionTimeoutInMilliseconds** - 此设置可替代 StackExchange.Redis 客户端中的 connectTimeout 设置。 如果未指定，则使用默认 connectTimeout 设置 5000。 有关详细信息，请参阅 [StackExchange.Redis 配置模型](http://go.microsoft.com/fwlink/?LinkId=398705)。
* **operationTimeoutInMilliseconds** - 此设置可替代 StackExchange.Redis 客户端中的 syncTimeout 设置。 如果未指定，则使用默认 syncTimeout 设置 1000。 有关详细信息，请参阅 [StackExchange.Redis 配置模型](http://go.microsoft.com/fwlink/?LinkId=398705)。

将 OutputCache 指令添加到希望为其缓存输出的每个页面中。

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

在上例中，缓存的页面数据可在缓存中保留 60 秒，并且将为每个参数组合缓存不同版本的页面。 有关 OutputCache 指令的详细信息，请参阅 [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837)。

执行这些步骤后，你的应用程序已配置为使用 Redis 输出缓存提供程序。

## <a name="next-steps"></a>后续步骤
了解 [Azure Redis 缓存的 ASP.NET 会话状态提供程序](cache-aspnet-session-state-provider.md)。


