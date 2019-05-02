---
title: Azure Redis 缓存的 ASP.NET 输出缓存提供程序
description: 了解如何使用 Azure Redis 缓存来缓存 ASP.NET 页面输出
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 04/22/2018
ms.author: yegu
ms.openlocfilehash: a93d21b07dc486f743694ee99f60018ed4ef517c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943875"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Azure Redis 缓存的 ASP.NET 输出缓存提供程序

Redis 输出缓存提供程序是用于输出缓存数据的进程外存储机制。 此数据专门用于完整 HTTP 响应（页面输出缓存）。 此提供程序会插入 ASP.NET 4 中引入的新输出缓存提供程序扩展点。

要使用 Redis 输出缓存提供程序，首先配置缓存，然后使用  Redis 输出缓存提供程序 NuGet 包配置 ASP.NET 应用程序。 本主题提供有关配置应用程序以使用 Redis 输出缓存提供程序的指南。 有关创建和配置 Azure Redis 缓存实例的详细信息，请参阅[创建缓存](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)。

## <a name="store-aspnet-page-output-in-the-cache"></a>在缓存中存储 ASP.NET 页面输出

要使用 Azure Redis 缓存会话状态 NuGet 包在 Visual Studio 中配置客户端应用程序，请在“工具”菜单中依次单击“NuGet 包管理器”和“包管理器控制台”。

从 `Package Manager Console` 窗口运行以下命令。

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Redis 输出缓存提供程序 NuGet 包依赖于 StackExchange.Redis.StrongName 包。 如果项目中没有 StackExchange.Redis.StrongName 包，则会安装它。 有关 Redis 输出缓存提供程序 NuGet 包的详细信息，请参阅 [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet 页。

>[!NOTE]
>除了强命名的 StackExchange.Redis.StrongName 包外，还有 StackExchange.Redis 非强命名版本。 如果您的项目使用的是非强命名 StackExchange.Redis 版本，则必须卸载它;否则，你将在项目中遇到命名冲突。 有关这些包的详细信息，请参阅[配置 .NET 缓存客户端](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)。

下载 NuGet 包、添加所需的程序集引用并将下面的部分添加到 web.config 文件。 此部分包含 ASP.NET 应用程序使用 Redis 输出缓存提供程序所需的配置。

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

在 Microsoft Azure 门户中使用缓存边栏选项卡中的值配置属性，并根据需要配置其他值。 有关访问缓存属性的说明，请参阅[配置 Azure Redis 缓存设置](cache-configure.md#configure-azure-cache-for-redis-settings)。

| 属性 | Type | 默认 | 描述 |
| --------- | ---- | ------- | ----------- |
| *主机* | string | "localhost" | Redis 服务器 IP 地址或主机名 |
| *port* | 正整数 | 6379 (non-SSL)<br/>6380 (SSL) | Redis 服务器端口 |
| *accessKey* | string | "" | 当启用了 Redis 授权时，redis 服务器密码。 值为空字符串，默认情况下，这意味着会话状态提供程序不会连接到 Redis 服务器时使用的任何密码。 **如果 Redis 服务器的 Azure Redis 缓存等可公开访问网络中时，请务必启用 Redis 授权以提高安全性，并提供一个安全的密码。** |
| *ssl* | boolean | **false** | 是否要连接到通过 SSL 的 Redis 服务器。 此值是**false**默认情况下由于 Redis 不现成支持 SSL。 **如果使用 Azure Redis 缓存支持 SSL 默认情况下，请务必将其设置为 true 以提高安全性。**<br/><br/>默认情况下，将为新缓存禁用非 SSL 端口。 指定 **，则返回 true**为此设置为使用 SSL 端口。 有关启用非 SSL 端口的详细信息，请参阅[配置缓存](cache-configure.md)主题中的[访问端口](cache-configure.md#access-ports)部分。 |
| *databaseIdNumber* | 正整数 | 0 | *可以将此属性指定只能通过 web.config 或 AppSettings。*<br/><br/>指定要使用的 Redis 数据库。 |
| *connectionTimeoutInMilliseconds* | 正整数 | 提供的 StackExchange.Redis | 用于设置*ConnectTimeout*创建 StackExchange.Redis.ConnectionMultiplexer 时。 |
| *operationTimeoutInMilliseconds* | 正整数 | 提供的 StackExchange.Redis | 用于设置*SyncTimeout*创建 StackExchange.Redis.ConnectionMultiplexer 时。 |
| *connectionString* （有效 StackExchange.Redis 连接字符串） | string | *n/a* | 任一参数引用到 AppSettings 或 web.config 文件中，否则有效 StackExchange.Redis 连接字符串。 此属性可以提供相应的值*主机*，*端口*， *accessKey*， *ssl*，和其他 StackExchange.Redis 属性。 仔细看一下*connectionString*，请参阅[设置 connectionString](#setting-connectionstring)中[属性说明](#attribute-notes)部分。 |
| *settingsClassName*<br/>*settingsMethodName* | string<br/>string | *n/a* | *这些属性可以指定只能通过 web.config 或 AppSettings。*<br/><br/>这些属性用于提供连接字符串。 *settingsClassName*应为包含指定的方法的程序集限定的类名*settingsMethodName*。<br/><br/>指定的方法*settingsMethodName*应为公共、 静态的和 void （不需要任何参数），返回类型为**字符串**。 此方法返回实际的连接字符串。 |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *n/a* | *这些属性可以指定只能通过 web.config 或 AppSettings。*<br/><br/>使用这些属性可以通过从 StackExchange.Redis 提供从会话状态/输出缓存日志以及日志调试应用程序。 *loggingClassName*应为包含指定的方法的程序集限定的类名*loggingMethodName*。<br/><br/>指定的方法*loggingMethodName*应为公共、 静态的和 void （不需要任何参数），返回类型为**System.IO.TextWriter**。 |
| *applicationName* | string | 当前进程的模块名称或"/" | *仅 SessionStateProvider*<br/>*可以将此属性指定只能通过 web.config 或 AppSettings。*<br/><br/>要使用 Redis 缓存中的应用名称前缀。 客户可以使用相同的 Redis 缓存用于不同目的。 若要确保副本会话密钥不会冲突，它可以作为前缀的应用程序名称。 |
| *throwOnError* | boolean | true | *仅 SessionStateProvider*<br/>*可以将此属性指定只能通过 web.config 或 AppSettings。*<br/><br/>是否发生错误时引发异常。<br/><br/>有关详细信息*throwOnError*，请参阅[上说明*throwOnError* ](#notes-on-throwonerror)中[特性说明](#attribute-notes)部分。 |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | 正整数 | 5000 | *仅 SessionStateProvider*<br/>*可以将此属性指定只能通过 web.config 或 AppSettings。*<br/><br/>重试操作失败时的时间。 如果此值是小于*operationTimeoutInMilliseconds*，提供程序不会重试。<br/><br/>有关详细信息*retryTimeoutInMilliseconds*，请参阅[上说明*retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds)中[特性说明](#attribute-notes)部分。 |
| *redisSerializerType* | string | *n/a* | 指定实现 Microsoft.Web.Redis 的类的程序集限定的类型名称。 ISerializer 并包含自定义逻辑来序列化和反序列化值。 有关详细信息，请参阅[有关*redisSerializerType* ](#about-redisserializertype)中[属性说明](#attribute-notes)部分。 |
|

## <a name="attribute-notes"></a>属性说明

### <a name="setting-connectionstring"></a>设置*connectionString*

值*connectionString*用作键以提取实际的连接字符串从 AppSettings，如果在 AppSettings 中存在这样的字符串。 如果未找到 AppSettings 的值内*connectionString*将用作键以从 web.config 中提取实际的连接字符串**ConnectionString**部分中，如果存在该部分。 如果连接字符串不 AppSettings 或 web.config 中存在**ConnectionString**部分的文字值*connectionString*创建时将用作连接字符串StackExchange.Redis.ConnectionMultiplexer。

以下示例说明了如何*connectionString*使用。

#### <a name="example-1"></a>示例 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

在`web.config`，以上键用作参数值而不是实际值。

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>示例 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

在`web.config`，以上键用作参数值而不是实际值。

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>示例 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>有关说明*throwOnError*

目前，如果在会话操作期间发生错误，会话状态提供程序将引发异常。 这将关闭该应用程序。

已在支持现有的 ASP.NET 会话状态提供程序用户的期望，同时还提供相关功能来处理异常，如果所需的一种方法中修改此行为。 默认行为仍会引发异常时出现错误，与其他 ASP.NET 会话状态提供程序; 一致现有代码应该工作与之前相同。

如果您设置*throwOnError*到**false**，而不会发生错误时引发异常，它将会失败以无提示方式。 若要查看是否有错误，以及如果是这样，发现该异常是，检查静态属性*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*。

### <a name="notes-on-retrytimeoutinmilliseconds"></a>有关说明*retryTimeoutInMilliseconds*

这提供了一些重试逻辑来简化这种情况，其中一些会话操作应失败时重试由于诸如网络故障，同时还允许您来控制重试超时时间，或完全选择退出重试。

如果您设置*retryTimeoutInMilliseconds*为数字，例如 2000 中，然后当会话操作失败时，它将重试为 2000年毫秒之前将其视为错误。 因此若要将此重试逻辑应用的会话状态提供程序，只需配置超时。 网络故障发生时即足够在大多数情况下在 20 毫秒后会发生首次重试。 然后，它将重试每隔一秒超时为止。超时时间之后, 它将重试一次，以确保，它不会截断超时的一秒 （至多）。

如果您认为不需要重试 （例如，当你与你的应用程序相同的计算机上运行 Redis 服务器），或如果想要自己，处理重试逻辑设置*retryTimeoutInMilliseconds*为 0。

### <a name="about-redisserializertype"></a>About *redisSerializerType*

默认情况下，若要在 Redis 中存储的值的序列化在提供的二进制格式中完成**BinaryFormatter**类。 使用*redisSerializerType*以指定实现的类的程序集限定的类型名称**Microsoft.Web.Redis.ISerializer**和包含自定义逻辑来序列化和反序列化值。 例如，下面是使用 JSON.NET 的 Json 序列化程序类：

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)6t6
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

假设此类定义中使用名称为程序集**MyCompanyDll**，可将参数设置*redisSerializerType*来使用它：

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>输出缓存指令

将 OutputCache 指令添加到希望为其缓存输出的每个页面中。

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

在上例中，缓存的页面数据可在缓存中保留 60 秒，并且将为每个参数组合缓存不同版本的页面。 有关 OutputCache 指令的详细信息，请参阅 [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837)。

执行这些步骤后，应用程序已配置为使用 Redis 输出缓存提供程序。

## <a name="next-steps"></a>后续步骤

了解 [Azure Redis 缓存的 ASP.NET 会话状态提供程序](cache-aspnet-session-state-provider.md)。
