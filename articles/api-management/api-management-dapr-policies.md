---
title: Azure API 管理 Dapr 集成策略 |Microsoft Docs
description: 了解用于与 Dapr 微服务扩展进行交互的 Azure API 管理策略。
author: vladvino
ms.author: vlvinogr
ms.date: 9/13/2020
ms.topic: article
ms.service: api-management
ms.openlocfilehash: d537040be4ed4cbf961a4621980d3d290e306359
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91341304"
---
# <a name="api-management-dapr-integration-policies"></a>API 管理 Dapr 集成策略

本主题提供 Dapr 集成 API 管理策略的参考。 Dapr 是一种可移植运行时，可使用任何语言或框架构建无状态和有状态的基于微服务的应用程序。 它整理常见的微服务模式，如使用内置重试逻辑进行服务发现和调用、使用至少一次传递语义发布和订阅，或可插入绑定资源，以使用外部服务简化组合。 转到 [dapr.io](https://dapr.io) ，了解有关如何开始 dapr 的详细信息和说明。 有关添加和配置策略的信息，请参阅 [API 管理中的策略](api-management-howto-policies.md)。

> [!CAUTION]
> 本主题中所述的策略是公共预览版，并受 [Microsoft Azure 预览版补充使用条款的约束](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

> [!IMPORTANT]
> 本主题中引用的策略仅适用于启用了 Dapr 支持的 [自承载版本的 API 管理网关](self-hosted-gateway-overview.md) 。

## <a name="enable-dapr-support-in-the-self-hosted-gateway"></a>在自承载网关上启用 Dapr 支持

若要在自承载网关上启用 Dapr 支持，请将下面的 [Dapr 批注](https://github.com/dapr/docs/blob/master/howto/configure-k8s/README.md) 添加到 [Kubernetes 部署模板](how-to-deploy-self-hosted-gateway-kubernetes.md) ，将 "app name" 替换为所需的名称。 [此处](https://aka.ms/apim/dapr/walkthru)提供了使用 Dapr 设置和使用 API 管理的完整演练。
```yml
template:
    metadata:
      labels:
        app: app-name
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "app-name"
```


## <a name="distributed-application-runtime-dapr-integration-policies"></a>分布式应用程序运行时 (Dapr) 集成策略

-  向[服务发送请求](api-management-dapr-policies.md#invoke)：使用 Dapr 运行时找到 Dapr 微服务并可靠地与之通信。 若要详细了解 Dapr 中的服务调用，请参阅此 [自述](https://github.com/dapr/docs/blob/master/concepts/service-invocation/README.md#service-invocation) 文件中的说明。
-  [将消息发送到发布/订阅主题](api-management-dapr-policies.md#pubsub)：使用 Dapr 运行时将消息发布到发布/订阅主题。 若要详细了解 Dapr 中的发布/订阅消息，请参阅此 [自述](https://github.com/dapr/docs/blob/master/concepts/publish-subscribe-messaging/README.md) 文件中的说明。
-  [Trigger output binding](api-management-dapr-policies.md#bind)：通过输出绑定使用 Dapr 运行时调用外部系统。 若要详细了解 Dapr 中的绑定，请参阅此 [自述](https://github.com/dapr/docs/blob/master/concepts/bindings/README.md) 文件中的说明。

## <a name="send-request-to-a-service"></a><a name="invoke"></a> 向服务发送请求

此策略设置当前请求的目标 URL，以将 `http://localhost:3500/v1.0/invoke/{app-id}/method/{method-name}` 模板参数替换为 policy 语句中指定的值。

策略假定 Dapr 在网关所在的同一 pod 中的挎斗容器中运行。 收到请求后，Dapr 运行时将执行服务发现和实际调用，包括 HTTP 和 gRPC、重试、分布式跟踪和错误处理之间的可能的协议转换。

### <a name="policy-statement"></a>策略语句

```xml
<set-backend-service backend-id="dapr" dapr-app-id="app-id" dapr-method="method-name" />
```

### <a name="examples"></a>示例

#### <a name="example"></a>示例

下面的示例演示如何在名为 "echo" 的微服务上调用名为 "back" 的方法。 `set-backend-service`策略设置目标 URL。 `forward-request`策略会将请求调度到 Dapr 运行时，这会将其传递到微服务。

`forward-request`为清楚起见，此处显示了策略。 策略通常通过关键字从全局作用域 "继承" `base` 。

```xml
<policies>
    <inbound>
        <base />
        <set-backend-service backend-id="dapr" dapr-app-id="echo" dapr-method="back" />
    </inbound>
    <backend>
        <forward-request />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### <a name="elements"></a>元素

| 元素             | 说明  | 必须 |
|---------------------|--------------|----------|
| set-backend-service | Root 元素 | 是      |

### <a name="attributes"></a>属性

| 属性        | 说明                     | 必须 | 默认 |
|------------------|---------------------------------|----------|---------|
| backend-id       | 必须设置为 "dapr"           | 是      | 空值     |
| dapr-id      | 目标微服务的名称。 映射到 Dapr 中的 [appId](https://github.com/dapr/docs/blob/master/reference/api/service_invocation_api.md) 参数。| 是 | 空值 |
| dapr-方法      | 要在目标微服务上调用的方法的名称或 URL。 映射到 Dapr 中的 [方法名称](https://github.com/dapr/docs/blob/master/reference/api/service_invocation_api.md) 参数。| 是 | 空值 |

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

- **策略节：** 入站
- **策略范围：** 所有范围

## <a name="send-message-to-pubsub-topic"></a><a name="pubsub"></a> 将消息发送到发布/订阅主题

此策略指示 API 管理网关将消息发送到 Dapr 发布/订阅主题。 此策略通过发出 HTTP POST 请求来 `http://localhost:3500/v1.0/publish/{{pub-name}}/{{topic}}` 替换模板参数并添加在 policy 语句中指定的内容来完成此过程。

此策略假定 Dapr 运行时在与网关相同的 pod 中的挎斗容器中运行。 Dapr 运行时实现 Pub/Sub 语义。

### <a name="policy-statement"></a>策略语句

```xml
<publish-to-dapr topic=”topic-name” ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template=”Liquid” content-type="application/json">
    <!-- message content -->
</publish-to-dapr>
```

### <a name="examples"></a>示例

#### <a name="example"></a>示例

下面的示例演示如何将当前请求的主体发送到 "订单" 发布/订阅[组件](https://github.com/dapr/docs/blob/master/reference/api/pubsub_api.md#url-parameters)的 "新"[主题](https://github.com/dapr/docs/blob/master/reference/api/pubsub_api.md#url-parameters)。 从 Dapr 运行时收到的响应存储在 [上下文](api-management-policy-expressions.md#ContextVariables) 对象中 Variables 集合的 "Dapr" 项中。

例如，如果 Dapr 运行时找不到目标主题，并响应错误，则会触发 "错误" 部分。 从 Dapr 运行时收到的响应将按原义返回到调用方。 否则， `200 OK` 将返回默认响应。

"后端" 部分为空，请求不会转发到后端。

```xml
<policies>
     <inbound>
        <base />
        <publish-to-dapr
               topic="@("orders/new")"
               response-variable-name="dapr-response">
            @(context.Request.Body.As<string>())
        </publish-to-dapr>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="pubsub-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>元素

| 元素             | 说明  | 必需 |
|---------------------|--------------|----------|
| 发布到 dapr     | Root 元素 | 是      |

### <a name="attributes"></a>属性

| 属性        | 说明                     | 必须 | 默认 |
|------------------|---------------------------------|----------|---------|
| 主题            | 目标主题名称               | 是      | 空值     |
| ignore-error     | 如果设置为， `true` 则在从 Dapr 运行时收到错误时指示策略不触发 ["错误时"](api-management-error-handling-policies.md) 部分 | 否 | `false` |
| response-variable-name | 用于存储来自 Dapr 运行时的响应的 [Variables](api-management-policy-expressions.md#ContextVariables) 集合项的名称 | 否 | 无 |
| timeout | ) 等待 Dapr 运行时响应的时间 (秒。 范围介于1到240秒之间。 | 否 | 5 |
| template | 用于转换消息内容的模板化引擎。 "液体" 是唯一受支持的值。 | 否 | 无 |
| content-type | 消息内容的类型。 "application/json" 是唯一受支持的值。 | 否 | 无 |

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

- **策略节：** 入站、出站、错误时
- **策略范围：** 所有范围

## <a name="trigger-output-binding"></a><a name="bind"></a> 触发器输出绑定

此策略指示 API 管理网关触发出站 Dapr [绑定](https://github.com/dapr/docs/blob/master/concepts/bindings/README.md)。 此策略通过发出 HTTP POST 请求来 `http://localhost:3500/v1.0/bindings/{{bind-name}}` 替换模板参数并添加在 policy 语句中指定的内容来完成此过程。

此策略假定 Dapr 运行时在与网关相同的 pod 中的挎斗容器中运行。 Dapr 运行时负责调用绑定所表示的外部资源。

### <a name="policy-statement"></a>策略语句

```xml
<invoke-dapr-binding name=”bind-name" operation="op-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template=”Liquid content-type="application/json">
    <metadata>
        <item key=”item-name”><!-- item-value --></item>
    </metadata>
    <data>
        <!-- message content -->
    </data>
</invoke-dapr-binding>
```

### <a name="examples"></a>示例

#### <a name="example"></a>示例

下面的示例演示了名为 "external systems"、操作名称为 "create"、由名为 "source" 和 "client-ip" 的两个键/值项组成的出站绑定，以及来自原始请求的正文。 从 Dapr 运行时收到的响应在 [上下文](api-management-policy-expressions.md#ContextVariables) 对象中的 Variables 集合的 "绑定响应" 条目中捕获。

如果 Dapr 运行时由于某种原因而失败，并且响应错误，则会触发 "错误" 部分，并且从 Dapr 运行时收到的响应将按原义返回到调用方。 否则， `200 OK` 将返回默认响应。

"后端" 部分为空，请求不会转发到后端。

```xml
<policies>
     <inbound>
        <base />
        <invoke-dapr-binding
                      name="external-system"
                      operation="create"
                      response-variable-name="bind-response">
            <metadata>
                <item key="source">api-management</item>
                <item key="client-ip">@( context.Request.IpAddress )</item>
            </metadata>
            <data>
                @( context.Request.Body.As<string>() )
            </data>
        </invoke-dapr-binding>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="bind-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>元素

| 元素             | 说明  | 必需 |
|---------------------|--------------|----------|
| dapr-binding | Root 元素 | 是      |
| metadata            | 以键/值对的形式绑定特定元数据。 映射到 Dapr 中的 [元数据](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#invoking-output-bindings) 属性。 | 否 |
| 数据            | 消息的内容。 映射到 Dapr 中的 [数据](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#invoking-output-bindings) 属性。 | 否 |


### <a name="attributes"></a>属性

| 属性        | 说明                     | 必须 | 默认 |
|------------------|---------------------------------|----------|---------|
| name            | 目标绑定名称。 必须与 Dapr 中 [定义](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#bindings-structure) 的绑定的名称相匹配。           | 是      | 空值     |
| operation       | 目标操作名称 (绑定特定) 。 映射到 Dapr 中的 [操作](https://github.com/dapr/docs/blob/master/reference/api/bindings_api.md#invoking-output-bindings) 属性。 | 否 | 无 |
| ignore-error     | 如果设置为， `true` 则在从 Dapr 运行时收到错误时指示策略不触发 ["错误时"](api-management-error-handling-policies.md) 部分 | 否 | `false` |
| response-variable-name | 用于存储来自 Dapr 运行时的响应的 [Variables](api-management-policy-expressions.md#ContextVariables) 集合项的名称 | 否 | 无 |
| timeout | ) 等待 Dapr 运行时响应的时间 (秒。 范围介于1到240秒之间。 | 否 | 5 |
| template | 用于转换消息内容的模板化引擎。 "液体" 是唯一受支持的值。 | 否 | 无 |
| content-type | 消息内容的类型。 "application/json" 是唯一受支持的值。 | 否 | 无 |

### <a name="usage"></a>使用情况

此策略可在以下策略[节](./api-management-howto-policies.md#sections)和[范围](./api-management-howto-policies.md#scopes)中使用。

- **策略节：** 入站、出站、错误时
- **策略范围：** 所有范围
