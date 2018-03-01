---
title: "使用 API 管理服务生成 HTTP 请求"
description: "了解如何使用 API 管理中的请求和响应策略从 API 调用外部服务"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 7f3cc81327d1d247fb8e19e256eafb009a5bf162
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>通过 Azure API 管理服务使用外部服务
Azure API 管理服务中的策略可以单纯根据传入的请求、传出的响应以及基本配置信息执行多种不同的有用工作。 但是，如果能够与 API 管理策略中的外部服务进行交互，则可以使更多的想法成为可能。

你以前见过如何与[用于日志记录、监视及分析的 Azure 事件中心服务](api-management-log-to-eventhub-sample.md)交互。 本文演示可用来与基于 HTTP 的任何外部服务进行交互的策略。 这些策略可用于触发远程事件，或检索可用于以某种方式处理原始请求和响应的信息。

## <a name="send-one-way-request"></a>Send-One-Way-Request
最简单的外部交互方式也许是即发即弃，使外部服务能够获得某种重要事件的通知。 可以使用控制流策略 `choose` 来检测自己感兴趣的任何类型的条件。  如果条件满足，可以使用 [send-one-way-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) 策略发起外部 HTTP 请求。 这可以是对消息传送系统（例如 Hipchat 或 Slack）的请求，也可能是对邮件 API（例如 SendGrid 或 MailChimp）的请求，或者是针对某些例如 PagerDuty 的重大支持事件的请求。 所有这些消息传送系统都提供可供调用的简单 HTTP API。

### <a name="alerting-with-slack"></a>使用 Slack 发出警报
以下示例演示当 HTTP 响应状态代码大于或等于 500 时如何将消息发送到 Slack 聊天室。 500 范围错误表示后端 API 发生问题，而 API 的客户端无法解决此类问题。 通常需要在 API 管理部件上进行某种形式的介入。  

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>
```

Slack 具有入站 Web Hook 的概念。 配置入站 Web Hook 时，Slack 将生成特殊的 URL，让用户执行简单的 POST 请求，并将消息传递到 Slack 通道。 创建的 JSON 主体基于 Slack 定义的格式。

![Slack Web Hook](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>即发即弃是否足够好？
使用请求的即发即弃形式有一些特定的权衡取舍。 如果出于某些原因导致请求失败，系统不会报告失败。 在这种特定情况下，无法保证具有辅助失败报告系统的复杂性，以及等待响应所需的其他性能成本。 如果检查响应很重要，则 [send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) 策略是较好的选项。

## <a name="send-request"></a>Send-Request
`send-request` 策略能够使用外部服务来执行复杂的处理函数，并将数据返回到 API 管理服务，此服务可用于进一步处理策略。

### <a name="authorizing-reference-tokens"></a>授权引用令牌
API 管理的主要功能是保护后端资源。 如果 API 使用的授权服务器可以像 [Azure Active Directory](../active-directory/active-directory-aadconnect.md) 一样创建 [JWT 令牌](http://jwt.io/)作为其 OAuth2 流程的一部分，则可以使用 `validate-jwt` 策略来验证令牌的有效性。 某些授权服务器创建所谓的[引用令牌](http://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/)，这些令牌无法在不对授权服务器进行回调的情况下进行验证。

### <a name="standardized-introspection"></a>标准化自检
对于使用授权服务器来验证引用令牌，过去一直没有标准化的方式。 但是，IETF 最近发布的提议标准 [RFC 7662](https://tools.ietf.org/html/rfc7662) 定义了资源服务器如何验证令牌的有效性。

### <a name="extracting-the-token"></a>提取令牌
第一个步骤是从授权标头撷取令牌。 标头值应该使用 `Bearer` 授权方案、单个空格和授权令牌根据 [RFC 6750](http://tools.ietf.org/html/rfc6750#section-2.1) 进行格式化。 但是，有一些情况需要省略授权分配。 考虑到这一点，API 管理在分析时会使用空格来拆分标头值，并从返回的字符串数组中选择最后一个字符串。 这样可为格式错误的授权标头提供应对措施。

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>发出验证请求
获取授权令牌后，API 管理就可以发出验证令牌的请求。 RFC 7662 调用此程序进行自检，并请求将 HTML 窗体 `POST` 到自检资源。 HTML 窗体必须至少包含具有键 `token` 的键/值对。 对授权服务器的请求也必须经过身份验证，确保恶意客户端无法获取有效令牌。

```xml
<send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
  <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
  <set-method>POST</set-method>
  <set-header name="Authorization" exists-action="override">
    <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
  </set-header>
  <set-header name="Content-Type" exists-action="override">
    <value>application/x-www-form-urlencoded</value>
  </set-header>
  <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
</send-request>
```

### <a name="checking-the-response"></a>检查响应
`response-variable-name` 属性可用于提供所返回响应的访问权限。 此属性中定义的名称可以用于作为 `context.Variables` 字典的键来访问 `IResponse` 对象。

从响应对象中可以检索主体，RFC 7622 会告知 API 管理，响应必须是 JSON 对象，并且必须至少包含一个名为 `active` 的属性（布尔值）。 当 `active` 为 true，则令牌被视为有效。

### <a name="reporting-failure"></a>报告失败
可以使用 `<choose>` 策略来检测令牌是否无效，如果无效，则返回 401 响应。

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>
```

根据 [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) 中说明的 `bearer` 令牌的使用方式，API 管理还返回了 `WWW-Authenticate` 标头以及 401 响应。 WWW-Authenticate 的目的是指示客户端如何构造适当授权的请求。 由于有各式各样可能具有 OAuth2 架构的处理方法，因此很难传达所有必要的信息。 幸好我们仍持续努力来帮助[客户端发现如何适当地将请求授权给资源服务器](http://tools.ietf.org/html/draft-jones-oauth-discovery-00)。

### <a name="final-solution"></a>最终解决方案
在结束时，你获得以下策略：

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
          <!-- Check active property in response -->
          <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
              <!-- Return 401 Unauthorized with http-problem payload -->
              <return-response response-variable-name="existing response variable">
                  <set-status code="401" reason="Unauthorized" />
                  <set-header name="WWW-Authenticate" exists-action="override">
                      <value>Bearer error="invalid_token"</value>
                  </set-header>
              </return-response>
          </when>
      </choose>
  <base />
</inbound>
```

这是众多示例中唯一一个说明如何使用 `send-request` 策略，通过 API 管理服务将有用的外部服务集成到请求和响应的过程。

## <a name="response-composition"></a>响应组合
`send-request` 策略可用于增强对后端系统的主要请求（如同你在上述示例中所见），或者用于完全取代后端调用。 使用此技术可以轻松创建聚合自多个不同系统的复合资源。

### <a name="building-a-dashboard"></a>构建仪表板
有时我们希望能够公开多个后端系统中的信息，例如，驱动仪表板。 KPI 来自所有不同的后端，但是习惯不提供它们的直接访问权限，并且如果所有信息都是检索自单个请求，这就非常有用。 也许有些后端信息需要进行某种切片和细分，需要先稍微处理一下！ 如果知道用户习惯按 F5 键来查看其性能不佳的指标是否可能更改时，为了降低后端负载，能够缓存该复合资源就非常有用。    

### <a name="faking-the-resource"></a>伪装资源
构建仪表板资源的第一步是在 Azure 门户中配置新的操作。 这是占位符操作，用于配置编写策略以构建动态资源。

![仪表板操作](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>发出请求
创建该操作后，可以专门针对该操作配置策略。 

![仪表板操作](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

第一个步骤是提取来自传入请求的任何查询参数，以便将其转发到后端。 在本示例中，仪表板每隔一段时间显示信息，因此具有 `fromDate` 和 `toDate` 参数。 可以使用 `set-variable` 策略来提取请求 URL 中的信息。

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

获取此信息后，可以对所有后端系统发出请求。 每个请求使用参数信息构造新 URL，调用相应的服务器，并将响应存储在上下文变量中。

```xml
<send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
  <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
  <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

这些请求按顺序执行，但这不是理想的做法。 

### <a name="responding"></a>响应
若要构造复合响应，可以使用 [return-response](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) 策略。 `set-body` 元素可以使用表达式构造新的 `JObject` 以及嵌入为属性的所有组件表示形式。

```xml
<return-response response-variable-name="existing response variable">
  <set-status code="200" reason="OK" />
  <set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
  </set-header>
  <set-body>
    @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                  new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                  new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                  new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                  ).ToString())
  </set-body>
</return-response>
```

完整的策略如下所示：

```xml
<policies>
    <inbound>

  <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
  <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
</policies>
```

在配置占位符操作时，可以将仪表板资源配置为至少缓存一小时。 

## <a name="summary"></a>摘要
Azure API 管理服务提供可根据需要应用到 HTTP 流量的灵活策略，并支持后端服务的组合。 不管是要使用警报、校验、验证功能还是基于多个后端服务创建新的复合资源来增强 API 网关，`send-request` 和相关策略都能使这种想法成为可能。

## <a name="watch-a-video-overview-of-these-policies"></a>观看这些策略的视频概述
有关本文所述的 [send-one-way-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest)、[send-request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) 和 [return-response](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) 策略的详细信息，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Send-Request-and-Return-Response-Policies/player]
> 
> 

