---
title: Azure Redis 缓存的 ASP.NET 输出缓存提供程序
description: 了解如何使用用于 Redis 的 Azure 缓存来缓存 ASP.NET 页输出。 Redis 输出缓存提供程序是用于输出缓存数据的进程外存储机制。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: 1a375f063d398c19ed86a0a401e2a41c696ef4e2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75412988"
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
>除了强命名的 StackExchange.Redis.StrongName 包外，还有 StackExchange.Redis 非强命名版本。 如果你的项目使用非强名称 Stackexchange.redis. Redis 版本，则必须将其卸载;否则，你将在项目中遇到命名冲突。 有关这些包的详细信息，请参阅[配置 .NET 缓存客户端](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)。

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

| Attribute | 类型 | 默认 | Description |
| --------- | ---- | ------- | ----------- |
| *主机* | 字符串 | 主机 | Redis 服务器的 IP 地址或主机名 |
| *port* | 正整数 | 6379 (non-SSL)<br/>6380 (SSL) | Redis 服务器端口 |
| *accessKey* | 字符串 | "" | 启用 Redis 授权时，Redis 服务器密码。 默认情况下，该值为空字符串，这意味着在连接到 Redis 服务器时，会话状态提供程序不会使用任何密码。 **如果你的 Redis 服务器位于可公开访问的网络（如 Azure Redis 缓存）中，请确保启用 Redis 授权以提高安全性，并提供安全密码。** |
| *ssl* | boolean | **false** | 是否通过 SSL 连接到 Redis 服务器。 默认情况下，此值为**false** ，因为 Redis 不支持现成的 SSL。 **如果使用的是支持 SSL 的 Azure Redis 缓存，请确保将此值设置为 true 以提高安全性。**<br/><br/>默认情况下，将为新缓存禁用非 SSL 端口。 将此设置指定为**true**可使用 SSL 端口。 有关启用非 SSL 端口的详细信息，请参阅[配置缓存](cache-configure.md)主题中的[访问端口](cache-configure.md#access-ports)部分。 |
| *databaseIdNumber* | 正整数 | 0 | *只能通过 web.config 或 AppSettings 指定此属性。*<br/><br/>指定要使用的 Redis 数据库。 |
| *connectionTimeoutInMilliseconds* | 正整数 | 由 Stackexchange.redis 提供。 Redis | 用于在创建 Stackexchange.redis 时设置*ConnectTimeout* 。 |
| *operationTimeoutInMilliseconds* | 正整数 | 由 Stackexchange.redis 提供。 Redis | 用于在创建 Stackexchange.redis 时设置*SyncTimeout* 。 |
| *connectionString* （有效的 stackexchange.redis Redis 连接字符串） | 字符串 | *不适用* | 对 AppSettings 或 web.config 的参数引用，或者为有效的 Stackexchange.redis 连接字符串。 此属性可以为*host*、 *port*、 *AccessKey*、 *ssl*和其他 Redis 属性提供值。 有关*connectionString*的详细介绍，请参阅[属性注释](#attribute-notes)部分中的[设置 connectionString](#setting-connectionstring) 。 |
| *settingsClassName*<br/>*settingsMethodName* | 字符串<br/>字符串 | *不适用* | *只能通过 web.config 或 AppSettings 指定这些属性。*<br/><br/>使用这些属性可以提供连接字符串。 *settingsClassName*应为程序集限定类名，其中包含由*settingsMethodName*指定的方法。<br/><br/>*SettingsMethodName*指定的方法应为 public、static 和 void （不采用任何参数），返回类型为**string**。 此方法返回实际的连接字符串。 |
| *loggingClassName*<br/>*loggingMethodName* | 字符串<br/>字符串 | *不适用* | *只能通过 web.config 或 AppSettings 指定这些属性。*<br/><br/>使用这些属性可以通过从会话状态/输出缓存提供日志以及 Stackexchange.redis. Redis 中的日志来调试应用程序。 *loggingClassName*应为程序集限定类名，其中包含由*loggingMethodName*指定的方法。<br/><br/>*LoggingMethodName*指定的方法应为 public、static 和 void （不采用任何参数），返回类型为**system.exception。** |
| *applicationName* | 字符串 | 当前进程的模块名称或 "/" | *仅 SessionStateProvider*<br/>*只能通过 web.config 或 AppSettings 指定此属性。*<br/><br/>要在 Redis 缓存中使用的应用名称前缀。 客户可出于不同目的使用同一 Redis 缓存。 若要确保会话密钥不会发生冲突，可将其作为应用程序名称的前缀。 |
| *throwOnError* | boolean | true | *仅 SessionStateProvider*<br/>*只能通过 web.config 或 AppSettings 指定此属性。*<br/><br/>发生错误时是否引发异常。<br/><br/>有关*throwOnError*的详细信息，请参阅[属性注释](#attribute-notes)部分中的[ *throwOnError*上的说明](#notes-on-throwonerror)。 |>*Redis. RedisSessionStateProvider. microsoft.web.redis.redissessionstateprovider.lastexception*。 |
| *retryTimeoutInMilliseconds* | 正整数 | 5000 | *仅 SessionStateProvider*<br/>*只能通过 web.config 或 AppSettings 指定此属性。*<br/><br/>操作失败时要重试的时间。 如果此值小于*operationTimeoutInMilliseconds*，则提供程序将不会重试。<br/><br/>有关*retryTimeoutInMilliseconds*的详细信息，请参阅[属性注释](#attribute-notes)部分中的[ *retryTimeoutInMilliseconds*上的说明](#notes-on-retrytimeoutinmilliseconds)。 |
| *redisSerializerType* | 字符串 | *不适用* | 指定实现 Redis 的类的程序集限定类型名称。 ISerializer 和，其中包含用于序列化和反序列化值的自定义逻辑。 有关详细信息，请参阅[属性注释](#attribute-notes)部分中的[About *redisSerializerType* ](#about-redisserializertype) 。 |
|

## <a name="attribute-notes"></a>属性说明

### <a name="setting-connectionstring"></a>设置*connectionString*

如果 AppSettings 中存在这样的字符串，则将*connectionString*的值用作密钥以便从 AppSettings 提取实际连接字符串。 如果在 AppSettings 中找不到，则*connectionString*的值将用作从 web.config **connectionString**部分获取实际连接字符串的密钥（如果该部分存在）。 如果 AppSettings 或 web.config **ConnectionString**部分中不存在连接字符串，则在创建 stackexchange.redis 时，将使用文本值*connectionstring*作为连接字符串。

下面的示例演示如何使用*connectionString* 。

#### <a name="example-1"></a>示例 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

在 `web.config`中，使用上面的键作为参数值而不是实际值。

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

在 `web.config`中，使用上面的键作为参数值而不是实际值。

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

### <a name="notes-on-throwonerror"></a>有关*throwOnError*的说明

目前，如果在会话操作期间发生错误，则会话状态提供程序将引发异常。 这会关闭应用程序。

此行为已采用支持现有 ASP.NET 会话状态提供程序用户的期望的方式进行了修改，同时还提供了根据需要处理异常的功能。 当发生错误时，默认行为仍会引发异常，与其他 ASP.NET 会话状态提供程序一致;现有代码应像以前一样工作。

如果将*throwOnError*设置为**false**，则不会在发生错误时引发异常，而是无提示地失败。 若要查看是否存在错误，如果出现错误，请检查静态属性*microsoft.web.redis.redissessionstateprovider.lastexception。 Redis. RedisSessionStateProvider.* 。

### <a name="notes-on-retrytimeoutinmilliseconds"></a>有关*retryTimeoutInMilliseconds*的说明

这提供了一些重试逻辑，用于简化某些会话操作应该在失败时重试的情况，这是因为网络有问题，同时也允许您控制重试超时或取消选择完全重试。

如果将*retryTimeoutInMilliseconds*设置为一个数字（例如2000），则当会话操作失败时，它将在2000毫秒后重试，然后将其视为错误。 因此，若要让会话状态提供程序应用此重试逻辑，只需配置超时。 第一次重试在20毫秒后进行，这在大多数情况下会发生网络故障。 之后，它会每秒重试一次，直到它超时。超时后，它将重试一次，以确保它不会被（最多）一秒钟地截断超时。

如果你认为需要重试（例如，当你在应用程序所在的同一台计算机上运行 Redis 服务器时），或者如果你想要自行处理重试逻辑，请将*retryTimeoutInMilliseconds*设置为0。

### <a name="about-redisserializertype"></a>About *redisSerializerType*

默认情况下，用于存储 Redis 上的值的序列化以**BinaryFormatter**类提供的二进制格式完成。 使用*redisSerializerType*指定实现**Redis**的类的程序集限定类型名称，并具有用于序列化和反序列化值的自定义逻辑。 例如，下面是一个使用 JSON.NET 的 Json 序列化程序类：

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

假设此类是在名为**MyCompanyDll**的程序集中定义的，则可以将参数*redisSerializerType*设置为使用它：

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
