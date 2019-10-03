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
ms.openlocfilehash: d3babb213f633586786c0015c27fae50e44369df
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815658"
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
>除了强命名的 StackExchange.Redis.StrongName 包外，还有 StackExchange.Redis 非强命名版本。 如果项目使用非强命名的 StackExchange.Redis 版本，则必须将其卸载，否则会在项目中遇到命名冲突。 有关这些包的详细信息，请参阅[配置 .NET 缓存客户端](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)。

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

| 特性 | 类型 | 默认 | 描述 |
| --------- | ---- | ------- | ----------- |
| *主机* | string | "localhost" | Redis 服务器 IP 地址或主机名 |
| *port* | 正整数 | 6379 (non-SSL)<br/>6380 (SSL) | Redis 服务器端口 |
| *accessKey* | string | "" | 启用 Redis 授权时的 Redis 服务器密码。 该值默认为空字符串，即，会话状态提供程序在连接到 Redis 服务器时不使用任何密码。 **如果 Redis 服务器位于 Azure Redis 缓存等可公开访问的网络中，请务必启用 Redis 授权以提高安全性，并提供安全密码。** |
| *ssl* | boolean | **false** | 是否通过 SSL 连接到 Redis 服务器。 此值默认为 **false**，因为 Redis 不能现成地支持 SSL。 **如果使用现成支持 SSL 的 Azure Redis 缓存，请务必将此属性设置为 true，以提高安全性。**<br/><br/>默认情况下，将为新缓存禁用非 SSL 端口。 为此设置指定 **true** 可使用 SSL 端口。 有关启用非 SSL 端口的详细信息，请参阅[配置缓存](cache-configure.md)主题中的[访问端口](cache-configure.md#access-ports)部分。 |
| *databaseIdNumber* | 正整数 | 0 | 只能通过 web.config 或 AppSettings 指定此属性。<br/><br/>指定要使用的 Redis 数据库。 |
| *connectionTimeoutInMilliseconds* | 正整数 | 由 StackExchange.Redis 提供 | 用于在创建 StackExchange.Redis.ConnectionMultiplexer 时设置 *ConnectTimeout*。 |
| *operationTimeoutInMilliseconds* | 正整数 | 由 StackExchange.Redis 提供 | 用于在创建 StackExchange.Redis.ConnectionMultiplexer 时设置 *SyncTimeout*。 |
| *connectionString*（有效的 StackExchange.Redis 连接字符串） | string | *n/a* | 对 AppSettings 或 web.config 的参数引用，或有效的 StackExchange.Redis 连接字符串。 此属性可提供 *host*、*port*、*accessKey*、*ssl* 和其他 StackExchange.Redis 属性的值。 若要更详细地了解 *connectionString*，请参阅[属性说明](#attribute-notes)部分中的[设置 connectionString](#setting-connectionstring)。 |
| *settingsClassName*<br/>*settingsMethodName* | string<br/>string | *n/a* | 只能通过 web.config 或 AppSettings 指定这些属性。<br/><br/>使用这些属性提供连接字符串。 *settingsClassName* 应是程序集限定的类名，其中包含 *settingsMethodName* 指定的方法。<br/><br/>*settingsMethodName* 指定的方法应是 public、static 和 void（不采用任何参数），其返回类型为 **string**。 此方法返回实际的连接字符串。 |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *n/a* | 只能通过 web.config 或 AppSettings 指定这些属性。<br/><br/>使用这些属性可以通过提供会话状态/输出缓存的日志以及 StackExchange.Redis 的日志，来调试应用程序。 *loggingClassName* 应是程序集限定的类名，其中包含 *loggingMethodName* 指定的方法。<br/><br/>*loggingMethodName* 指定的方法应是 public、static 和 void（不采用任何参数），其返回类型为 **System.IO.TextWriter**。 |
| *applicationName* | string | 当前进程的模块名称，或“/” | 仅限 SessionStateProvider<br/>只能通过 web.config 或 AppSettings 指定此属性。<br/><br/>要在 Redis 缓存中使用的应用名称前缀。 客户可以使用相同的 Redis 缓存来实现不同的目的。 为了确保会话密钥不冲突，可以使用应用程序名称作为 Redis 缓存的前缀。 |
| *throwOnError* | boolean | true | 仅限 SessionStateProvider<br/>只能通过 web.config 或 AppSettings 指定此属性。<br/><br/>出错时是否引发异常。<br/><br/>有关 *throwOnError* 的详细信息，请参阅[属性说明](#attribute-notes)部分中的[有关 *throwOnError* 的说明](#notes-on-throwonerror)。 |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*。 |
| *retryTimeoutInMilliseconds* | 正整数 | 5000 | 仅限 SessionStateProvider<br/>只能通过 web.config 或 AppSettings 指定此属性。<br/><br/>操作失败时要重试多长时间。 如果此值小于 *operationTimeoutInMilliseconds*，则提供程序不会重试。<br/><br/>有关 *retryTimeoutInMilliseconds* 的详细信息，请参阅[属性说明](#attribute-notes)部分中的[有关 *retryTimeoutInMilliseconds* 的说明](#notes-on-retrytimeoutinmilliseconds)。 |
| *redisSerializerType* | string | *n/a* | 指定某个类的程序集限定类型名称，该类实现 Microsoft.Web.Redis. ISerializer，且包含用于序列化和反序列化值的自定义逻辑。 有关详细信息，请参阅[属性说明](#attribute-notes)部分中的[关于 *redisSerializerType*](#about-redisserializertype)。 |
|

## <a name="attribute-notes"></a>属性说明

### <a name="setting-connectionstring"></a>设置 *connectionString*

*connectionString* 的值用作密钥，从 AppSettings 中提取实际的连接字符串（如果 AppSettings 中存在此类字符串）。 如果在 AppSettings 中未找到此字符串，*connectionString* 的值将用作密钥，从 web.config 的 **ConnectionString** 节（如果存在该节）中提取实际的连接字符串。 如果 AppSettings 或 web.config 的 **ConnectionString** 节中不存在该连接字符串，则创建 StackExchange.Redis.ConnectionMultiplexer 时，会将 *connectionString* 的文本值用作连接字符串。

以下示例演示如何使用 *connectionString*。

#### <a name="example-1"></a>示例 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

在 `web.config` 中，使用上述密钥作为参数值而不是实际值。

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

在 `web.config` 中，使用上述密钥作为参数值而不是实际值。

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

### <a name="notes-on-throwonerror"></a>有关 *throwOnError* 的说明

目前，如果在会话操作期间出错，则会话状态提供程序将引发异常。 这会关闭应用程序。

此行为现已经过修改，符合现有 ASP.NET 会话状态提供程序用户的预期，同时还提供按需处理异常的功能。 默认行为仍会在出错时引发异常，这与其他 ASP.NET 会话状态提供程序相一致；现有代码应可如过去一样正常运行。

如果将 *throwOnError* 设置为 **false**，则出错时不会引发异常，而是以无提示方式失败。 若要查看是否已出错，从而发现具体的异常是什么，请检查静态属性 *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*。

### <a name="notes-on-retrytimeoutinmilliseconds"></a>有关 *retryTimeoutInMilliseconds* 的说明

此属性提供某种重试逻辑来简化以下方案：某项会话操作因网络小故障等问题失败时应该重试，同时允许控制重试超时，或完全禁用重试。

如果将 *retryTimeoutInMilliseconds* 设置为某个数字（例如 2000），则当会话操作失败时，它会重试 2000 毫秒，如果仍然失败，系统将认为出错。 因此，若要让会话状态提供程序应用此重试逻辑，只需配置超时即可。 首次重试发生在 20 毫秒之后，在大多数情况下，发生网络小故障时，此时间已足够。 此后，操作会每隔一秒重试一次，直到超时。超时过后，操作会紧接着再试一次，以确保不会将超时终止（最多）一秒。

如果你认为不需要重试（例如，在应用程序所在的同一台计算机上运行 Redis 服务器时），或者要自行处理重试逻辑，请将 *retryTimeoutInMilliseconds* 设置为 0。

### <a name="about-redisserializertype"></a>About *redisSerializerType*

默认情况下，用于在 Redis 上存储值的序列化是以 **BinaryFormatter** 类提供的二进制格式完成的。 使用 *redisSerializerType* 可以指定某个类的程序集限定类型名称，该类实现 **Microsoft.Web.Redis.ISerializer**，且包含用于序列化和反序列化值的自定义逻辑。 例如，下面是一个使用 JSON.NET 的 JSON 序列化程序类：

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
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

假设此类在名为 **MyCompanyDll** 的程序集中定义，你可以设置参数 *redisSerializerType* 来使用该类：

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
