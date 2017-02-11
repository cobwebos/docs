---
title: "作为可调用终结点的逻辑应用"
description: "如何创建并配置触发器终结点，以及在 Azure App Service 中的逻辑应用中使用它们"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 372114b1420139bdf525521a427f924aae38db6f


---
# <a name="logic-apps-as-callable-endpoints"></a>作为可调用终结点的逻辑应用
逻辑应用可以在本地将同步 HTTP 终结点作为触发器进行公开。  还可以使用可调用终结点的模式，通过逻辑应用中的“工作流”操作将逻辑应用作为嵌套工作流来调用。

有 3 种类型的触发器可以接收请求：

* 请求
* ApiConnectionWebhook
* HttpWebhook

为说明本文的其余部分，我们使用**请求**作为示例，但是所有原理都同样适用于其他 2 种类型的触发器。

## <a name="adding-a-trigger-to-your-definition"></a>将触发器添加到定义
第一步是将可以接收传入请求的触发器添加到逻辑应用定义。  可以在设计器中搜索“HTTP 请求”以添加到触发器卡。 可以定义请求正文 JSON 架构，设计器会生成标记以帮助分析数据并通过工作流从手动触发器传递数据。  建议使用 [jsonschema.net](http://jsonschema.net) 这类工具从示例正文有效负载生成 JSON 架构。

![请求触发器卡][2]

保存逻辑应用定义之后，会生成与以下类似的回调 URL：

``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
```

此 URL 在查询参数中包含用于进行身份验证的 SAS 密钥。

还可以在 Azure 门户中获取此终结点：

![][1]

或者，通过调用：

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

### <a name="security-for-the-trigger-url"></a>触发器 URL 的安全
使用共享访问签名安全地生成逻辑应用回调 URL。  签名将作为查询参数进行传递，且在逻辑应用触发之前必须经过验证。  它是通过每个逻辑应用的密钥、触发器名称和正在执行的操作的唯一组合而生成的。  除非具有对机密逻辑应用密钥的访问权限，否则将无法生成有效地签名。

## <a name="calling-the-logic-app-triggers-endpoint"></a>调用逻辑应用触发器的终结点
为触发器创建了终结点之后，可以通过针对完整 URL 的 `POST` 触发它。 可以在正文中包含其他标头以及任何内容。

如果内容类型是 `application/json`，则你能够从请求内部引用属性。 否则，它会被视为单个二进制单元，可以传递给其他 API，但不能在未转换内容的情况下在工作流中进行引用。  例如，如果传递 `application/xml` 内容，则可以使用 `@xpath()` 执行 xpath 提取，或使用 `@json()` 从 XML 转换为 JSON。  有关使用内容类型的详细信息[可以在此处找到](app-service-logic-content-type.md)

此外，可以在定义中指定 JSON 架构。 这会使设计器生成可以随后传入步骤的标记。  例如，以下内容会创建在设计器中可用的 `title` 和 `name` 标记：

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## <a name="referencing-the-content-of-the-incoming-request"></a>引用传入请求的内容
`@triggerOutputs()` 函数会输出传入请求的内容。 例如，它类似于下面这样：

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

可以使用 `@triggerBody()` 快捷方式专门访问 `body` 属性。 

## <a name="responding-to-the-request"></a>响应请求
对于某些启动逻辑应用的请求，你可能要使用一些内容来响应调用方。 有一种称为**响应**的新操作类型，可以用于为响应构造状态代码、正文和标头。 请注意，如果不存在**响应**形状，则逻辑应用终结点会立即使用“202 已接受”进行响应。

![HTTP 响应操作][3]

``` json
"Response": {
            "conditions": [],
            "inputs": {
                "body": {
                    "name": "@{triggerBody()['name']}",
                    "title": "@{triggerBody()['title']}"
                },
                "headers": {
                    "content-type": "application/json"
                },
                "statusCode": 200
            },
            "type": "Response"
        }
```

使用以下内容进行响应：

| 属性 | 说明 |
| --- | --- |
| statusCode |用于响应传入请求的 HTTP 状态代码。 它可以是以 2xx、4xx 或 5xx 开头的任何有效状态代码。 不允许使用 3xx 状态代码。 |
| body |正文对象，可以是字符串、JSON 对象甚至是从上一步引用的二进制内容。 |
| headers |可以定义任意数量的标头以包含在响应中 |

响应所需的所有逻辑应用步骤都必须在 60 秒内完成，以便原始请求可以接收响应，**除非以嵌套逻辑应用的形式调用工作流**。 如果在 60 秒中未完成任何响应操作，则传入请求会超时，并收到“408 客户端超时”HTTP 响应。  对于嵌套逻辑应用，父逻辑应用会继续等待响应直到完成（不考虑所花的总时间）。

## <a name="advanced-endpoint-configuration"></a>高级终结点配置
逻辑应用具有针对直接访问终结点的内置支持，始终使用 `POST` 方法启动逻辑应用的运行。 **HTTP 侦听程序** API 应用以前还支持更改 URL 段和 HTTP 方法。 甚至可以设置其他安全性或自定义域，具体方法是将它添加到 API 应用主机（承载 API 应用的 Web 应用）。 

此功能通过 **API 管理**提供：

* [更改请求的方法](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [更改请求的 URL 段](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* 在经典 Azure 门户中的“配置”选项卡上设置 API 管理域
* 设置策略以检查基本身份验证检查（**需要链接**）

## <a name="summary-of-migration-from-2014-12-01-preview"></a>从 2014-12-01-预览进行迁移的摘要
| 2014-12-01-预览 | 2016-06-01 |
| --- | --- |
| 单击“HTTP 侦听器”API 应用 |单击“手动触发器”（无需 API 应用） |
| HTTP 侦听器设置“自动发送响应” |包含**响应**操作或不在工作流定义中 |
| 配置基本或 OAuth 身份验证 |通过 API 管理 |
| 配置 HTTP 方法 |通过 API 管理 |
| 配置相对路径 |通过 API 管理 |
| 通过 `@triggerOutputs().body.Content` 引用传入正文 |通过 `@triggerOutputs().body` 引用 |
| 对 HTTP 侦听器执行的“发送 HTTP 响应”操作 |单击“响应 HTTP 请求”（无需 API 应用） |

[1]: ./media/app-service-logic-http-endpoint/manualtriggerurl.png
[2]: ./media/app-service-logic-http-endpoint/manualtrigger.png
[3]: ./media/app-service-logic-http-endpoint/response.png



<!--HONumber=Nov16_HO3-->


