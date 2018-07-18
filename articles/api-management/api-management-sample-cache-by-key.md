---
title: Azure API 管理中的自定义缓存
description: 了解如何在 Azure API 管理中根据键缓存项
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 838850d38c9df51fabcf620831371bed401e9492
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
ms.locfileid: "29376025"
---
# <a name="custom-caching-in-azure-api-management"></a>Azure API 管理中的自定义缓存
Azure API 管理服务使用资源 URL 作为键，提供对 [HTTP 响应缓存](api-management-howto-cache.md)的内置支持。 可以使用 `vary-by` 属性根据请求标头修改键。 这种做法适合用于缓存整个 HTTP 响应（也称为“表示形式”），但有时也适合用于只缓存一部分表示形式。 使用新的 [cache-lookup-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) 和 [cache-store-value](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) 策略可以存储和检索策略定义中的任意数据。 此功能使得以前推出的 [send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) 策略更有作用，因为现在可以缓存来自外部服务的响应。

## <a name="architecture"></a>体系结构
API 管理服务使用基于租户的共享数据缓存，因此，在增加到多个单位后，仍可以访问相同的缓存数据。 但是，使用多区域部署时，每个区域内有独立的缓存。 不得将缓存视为数据存储，因为数据存储是某些信息片段的唯一源。 如果这样做，后来又决定利用多区域部署，则具有移动工作人员的客户可能会失去该缓存数据的访问权限。

## <a name="fragment-caching"></a>分段缓存
在某些情况下，返回的响应中包含的某些数据部分不但非常重要，而且还保留一段合理的时间。 以航空公司构建的、提供航班预订、航班状态等信息的服务为例。如果用户是航空公司积分计划的会员，则他们也会获得其当前状态和累积里程的相关信息。 这些用户的相关信息可能存储在不同的系统中，但也可能需要包含在航班状态和预订相关的返回响应中。 可以使用名为“分段缓存”的过程实现此目的。 可以从源服务器返回主要表示形式，使用某种令牌来指明要将用户相关的信息插入到何处。 

假设后端 API 返回了以下 JSON 响应。

```json
{
  "airline" : "Air Canada",
  "flightno" : "871",
  "status" : "ontime",
  "gate" : "B40",
  "terminal" : "2A",
  "userprofile" : "$userprofile$"
}  
```

`/userprofile/{userid}` 中的辅助资源如下所示：

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

为了确定要包含哪些适当的用户信息，API 管理需要识别最终用户是谁。 此机制与实现相关。 例如，此处使用了 `JWT` 令牌的 `Subject` 声明。 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

API 管理将 `enduserid` 值存储在上下文变量中供稍后使用。 下一步是确定前面的请求是否已检索用户信息并将其存储在缓存中。 为此，API 管理使用 `cache-lookup-value` 策略。

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

如果缓存中没有任何条目对应于键值，则不会创建 `userprofile` 上下文变量。 API 管理使用 `choose` 控制流策略来检查查询是否成功。

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

如果 `userprofile` 上下文变量不存在，则 API 管理需发出 HTTP 请求来检索信息。

```xml
<send-request
  mode="new"
  response-variable-name="userprofileresponse"
  timeout="10"
  ignore-error="true">

  <!-- Build a URL that points to the profile for the current end-user -->
  <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
      (string)context.Variables["enduserid"]).AbsoluteUri)
  </set-url>
  <set-method>GET</set-method>
</send-request>
```

API 管理使用 `enduserid` 来构造用户配置文件资源的 URL。 获得响应后，API 管理可以从响应中提取正文文本，并将它存回到上下文变量。

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

为了避免当同一用户发出另一个请求时 API 管理再次发出此 HTTP 请求，可以指定将用户配置文件存储在缓存中。

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

API 管理使用最初尝试检索值时使用的同一个键，将值存储在缓存中。 API 管理选择将值存储多长时间应该取决于信息更改的频率，以及用户对过期信息的容许度。 

请务必注意，从缓存中检索信息仍是过程外的网络请求，可能使请求的时间增加数十毫秒。 如果确定用户配置文件信息所需的时间超过需要执行数据库查询或者从多个后端聚合信息的时间，这种方案的优势就会显现出来。

此过程的最后一步是使用用户配置文件信息更新返回的响应。

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

可以选择在令牌中包含引号，目的是确保即使在替换没有发生的情况下，响应仍是有效的 JSON。  

将所有这些步骤组合到一起后，最终会创建如下所示的策略。

```xml
<policies>
    <inbound>
        <!-- How you determine user identity is application dependent -->
        <set-variable
          name="enduserid"
          value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

        <!--Look for userprofile for this user in the cache -->
        <cache-lookup-value
          key="@("userprofile-" + context.Variables["enduserid"])"
          variable-name="userprofile" />

        <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                <!-- Make HTTP request to get user profile -->
                <send-request
                  mode="new"
                  response-variable-name="userprofileresponse"
                  timeout="10"
                  ignore-error="true">

                   <!-- Build a URL that points to the profile for the current end-user -->
                    <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>

                <!-- Store response body in context variable -->
                <set-variable
                  name="userprofile"
                  value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                <!-- Store result in cache -->
                <cache-store-value
                  key="@("userprofile-" + context.Variables["enduserid"])"
                  value="@((string)context.Variables["userprofile"])"
                  duration="100000" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <!-- Update response body with user profile-->
        <find-and-replace
              from='"$userprofile$"'
              to="@((string)context.Variables["userprofile"])" />
        <base />
    </outbound>
</policies>
```

此缓存方法主要用于其 HTML 在服务器端撰写的网站中，能够以单页的形式呈现 HTML。 它也适用于其中的客户端无法执行客户端 HTTP 缓存的 API，或者不需要将此责任施加到客户端的情况。

这种分段缓存也可以在使用 Redis 缓存服务器的后端 Web 服务器上执行，但是，当缓存的段来自不同后端而不是主要响应时，使用 API 管理服务执行此工作便很有效。

## <a name="transparent-versioning"></a>透明版本控制
随时支持多个不同实现版本的 API 是常见做法。 例如，为了支持不同的环境（开发、测试、生产等），或者为了使旧版 API 能够让 API 使用者有时间迁移到新版本。 

其中一种方法是将使用者目前想要使用的 API 版本存储在使用者的配置文件数据中，并调用适当的后端 URL，客户端开发人员无需将 URL 从 `/v1/customers` 更改为 `/v2/customers`。 为了确定特定客户端要调用的正确后端 URL，必须查询某些配置数据。 通过缓存此配置数据，API 管理可以最大程度地减少执行此查找所造成的不利性能影响。

第一步是确定用于配置所需版本的标识符。 本示例选择将版本关联到产品订阅密钥。 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

然后，API 管理执行缓存查找，确定是否已检索到所需的客户端版本。

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

然后，API 管理会进行检查，看是否没有在缓存中找到它。

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

如果未找到它，API 管理会检索它。

```xml
<send-request
    mode="new"
    response-variable-name="clientconfiguresponse"
    timeout="10"
    ignore-error="true">
            <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
            <set-method>GET</set-method>
</send-request>
```

从响应中提取响应正文文本。

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

将它存回到缓存供日后使用。

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

最后，更新后端 URL 以选择客户端所需的服务版本。

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

完整的策略如下所示：

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">
            <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
            </send-request>
            <!-- Store response body in context variable -->
            <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
            <!-- Store result in cache -->
            <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
        </when>
    </choose>
    <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
</inbound>
```

使 API 使用者无需更新和重新部署客户端就能透明地控制客户端可以访问的后端版本是一种优雅的解决办法，能够解决 API 版本控制方面的许多问题。

## <a name="tenant-isolation"></a>租户隔离
在大型多租户部署中，有些公司在后端硬件的不同部署上创建独立的租户组。 这可以最大程度地减少后端硬件问题影响到的客户数目。 同时，可以将新软件版本分阶段推出。 在理想情况下，对 API 使用者而言，此后端体系结构应是透明的。 若要实现此目的，可以采用类似于透明版本控制的方式，因为这样可以根据相同的后端 URL 处理方法使用每个 API 密钥的配置状态。  

结果是返回将租户与分配的硬件组相关联的标识符，而不是每个订阅密钥的首选 API 版本。 该标识符可用于构造相应的后端 URL。

## <a name="summary"></a>摘要
由于可以自由使用 Azure API 管理缓存来存储任何类型的数据，因此可以有效访问可能影响入站请求处理方式的配置数据。 上述缓存也可以用于存储数据段（可以补充从后端 API 返回的响应）。
