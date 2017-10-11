---
title: "缓存 ASP.NET 输出缓存提供程序"
description: "了解如何使用 Azure Redis 缓存的 ASP.NET 页面输出缓存"
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
ms.openlocfilehash: 845f25637a0e48460fc76c1ee36060274b3cec38
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="aspnet-output-cache-provider-for-azure-redis-cache"></a>ASP.NET 输出缓存提供程序 Azure Redis 缓存
Redis 输出缓存提供程序是一种用于输出缓存数据的进程外存储机制。 此数据是专门用于完整 HTTP 响应 （页面输出缓存）。 此提供程序插入的新输出缓存提供程序扩展点 ASP.NET 4 中引入的。

若要使用 Redis 输出缓存提供程序，首先配置缓存，，然后配置 ASP.NET 应用程序使用 Redis 输出缓存提供程序 NuGet 包。 本主题提供有关配置应用程序以使用 Redis 输出缓存提供程序的指南。 有关创建和配置 Azure Redis 缓存实例的详细信息，请参阅[创建缓存](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)。

## <a name="store-aspnet-page-output-in-the-cache"></a>在缓存中存储 ASP.NET 页面输出
若要使用 Redis 缓存会话状态 NuGet 包的 Visual Studio 中配置客户端应用程序，请单击**NuGet 包管理器**，**程序包管理器控制台**从**工具**菜单。

运行以下命令从`Package Manager Console`窗口。
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Redis 输出缓存提供程序 NuGet 程序包对 StackExchange.Redis.StrongName 程序包有依赖。 如果 StackExchange.Redis.StrongName 程序包不存在于你的项目中，它被安装。 有关 Redis 输出缓存提供程序 NuGet 包的详细信息，请参阅[RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet 页。

>[!NOTE]
>除了具有强名称 StackExchange.Redis.StrongName 程序包，还有 StackExchange.Redis 非强命名版本。 如果你的项目使用的是非强命名 StackExchange.Redis 版本则必须卸载它，否则出现命名冲突项目中。 有关这些程序包的详细信息，请参阅[配置.NET 缓存客户端](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)。
>
>

NuGet 程序包下载并添加所需的程序集引用并添加以下会话到 web.config 文件。 本部分包含 ASP.NET 应用程序以使用 Redis 输出缓存提供程序所需的配置。

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

注释的部分提供特性和每个属性的示例设置的示例。

在 Microsoft Azure 门户中，你缓存边栏选项卡中的值配置属性，并根据需要配置其他值。 有关访问缓存属性的说明，请参阅[配置 Redis 缓存设置](cache-configure.md#configure-redis-cache-settings)。

* **主机**– 指定缓存终结点。
* **端口**– 使用非 SSL 端口或 SSL 端口，具体取决于 ssl 设置。
* **accessKey** – 使用缓存的主密钥或辅助密钥。
* **ssl** – 如果你想要保护缓存/客户端通信使用 ssl，则返回 true; 否则为 false。 请务必指定正确的端口。
  * 默认情况下，为新缓存禁用非 SSL 端口。 指定为 true，此设置以使用 SSL 端口。 有关启用非 SSL 端口的详细信息，请参阅[访问端口](cache-configure.md#access-ports)主题中[配置缓存](cache-configure.md)主题。
* **databaseId** – 指定的数据库以使用用于缓存输出数据。 如果未指定，使用默认值为 0。
* **applicationName** – 密钥存储在 redis 中作为`<AppName>_<SessionId>_Data`。 此命名方案使多个应用程序可以共享相同的密钥。 此参数是可选的如果未提供使用默认值。
* **connectionTimeoutInMilliseconds** – 此设置允许你覆盖 StackExchange.Redis 客户端中的 connectTimeout 设置。 如果未指定，使用默认 connectTimeout 设置 5000。 有关详细信息，请参阅[StackExchange.Redis 配置模型](http://go.microsoft.com/fwlink/?LinkId=398705)。
* **operationTimeoutInMilliseconds** – 此设置允许你覆盖 StackExchange.Redis 客户端中的 syncTimeout 设置。 如果未指定，使用默认 syncTimeout 设置 1000年。 有关详细信息，请参阅[StackExchange.Redis 配置模型](http://go.microsoft.com/fwlink/?LinkId=398705)。

将 OutputCache 指令添加到你希望为其缓存输出每个页面。

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

在前面的示例中，缓存的页面数据保留在缓存中 60 秒，并且对于各个参数组合缓存不同版本的页。 有关 OutputCache 指令的详细信息，请参阅[ @OutputCache ](http://go.microsoft.com/fwlink/?linkid=320837)。

一旦执行这些步骤，你的应用程序配置为使用 Redis 输出缓存提供程序。

## <a name="next-steps"></a>后续步骤
签出[Azure Redis 缓存的 ASP.NET 会话状态提供程序](cache-aspnet-session-state-provider.md)。

