---
title: "通过 HTTP 终结点调用、触发或嵌套逻辑应用 - Azure 逻辑应用 | Microsoft Docs"
description: "在 Azure 中添加并配置 HTTP 终结点以调用、触发或嵌套逻辑应用的工作流"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 8c4cb0aefcd7241b17c6afcf33496c1fc2e6f675
ms.lasthandoff: 03/03/2017

---

# <a name="add-http-endpoints-to-call-trigger-or-nest-logic-app-workflows"></a>添加 HTTP 终结点以调用、触发或嵌套逻辑应用工作流

逻辑应用可将同步的 HTTP 终结点本机公开为触发器，使你能够调用逻辑应用。 还可以使用可调用终结点的模式，通过将“选择逻辑应用工作流”操作添加到逻辑应用以将逻辑应用作为嵌套工作流来调用。

可将这些触发器用于接收以下请求：

* 请求
* ApiConnectionWebhook
* HttpWebhook

本主题使用“请求”触发器为例，但所有的原则同样适用于其他的触发器类型。

## <a name="add-a-trigger-to-your-logic-app-definition"></a>将触发器添加到逻辑应用定义

1. 在逻辑应用设计器中，为逻辑应用定义添加一个可接收传入请求的触发器。 例如，将“请求”触发器添加到逻辑应用。

2.    在“请求正文 JSON 架构”下，为期望接收的有效负载输入一个 JSON 架构。 如果尚无架构，可选择“使用示例有效负载生成架构”以从示例有效负载生成 JSON 架构。

    设计器使用此架构生成有助于通过工作流使用、分析数据并从手动触发器传递数据的令牌。

    ![添加请求操作][2]

2.    保存逻辑应用定义后，在“指向此 URL 的 HTTP POST”下，将得到一个已生成的回叫 URL，例如：

    ``` text
    https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
    ```

    此 URL 在查询参数中包含用于进行身份验证的共享访问签名 (SAS) 密钥。 
    还可从 Azure 门户获取此终结点：

    ![URL 终结点][1]

    或者，通过调用：

    ``` text
    POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
    ```

### <a name="change-the-http-method-for-your-trigger"></a>更改触发器的 HTTP 方法

默认情况下，“请求”触发器需要 HTTP POST 请求。 若要配置不同的 HTTP 方法，请选择“显示高级选项”。

> [!NOTE]
> 仅允许使用一种类型方法。

### <a name="customize-the-relative-trigger-url"></a>自定义相对触发器 URL

还可以自定义请求 URL 的相对路径以接受参数。

1. 在“请求”触发器上，选择“显示高级选项”。 在“相对路径”下，输入 `customer/{customerId}`。

    ![相对 URL 触发器](./media/logic-apps-http-endpoint/relativeurl.png)

2.    若要使用参数，请更新“响应”操作。

    *    此时令牌选取器中应会显示 `customerId`。
    *    若要返回 `Hello {customerId}`，请更新此“响应”操作的正文。

    ![相对 URL 响应](./media/logic-apps-http-endpoint/relativeurlresponse.png)

3. 保存逻辑应用。 将使用相对路径更新请求 URL。

4. 复制新的请求 URL 并将其粘贴到新的浏览器窗口中。 将 `{customerId}` 替换为 `123`，然后按 Enter。

    应返回此文本：`Your customer Id is 123`

### <a name="security-for-the-trigger-url"></a>触发器 URL 的安全

使用共享访问签名 (SAS) 安全地生成逻辑应用回叫 URL。 签名将作为查询参数进行传递，且在逻辑应用触发之前必须经过验证。 签名通过每个逻辑应用的密钥、触发器名称和执行的操作的唯一组合而生成的。 除非用户具有对机密逻辑应用密钥的访问权限，否则他们将无法生成有效的签名。

## <a name="call-your-logic-app-triggers-endpoint"></a>调用逻辑应用触发器的终结点

为触发器创建了终结点之后，可以通过针对完整 URL 的 `POST` 触发逻辑应用。 可以在正文中包含更多的标头以及任何内容。 如果内容类型是 `application/json`，则可从请求内部引用属性。 否则，内容会被视为单个二进制单元，可以传递给其他 API，但除非内容经过转换，否则无法在工作流内部引用该内容。 例如，如果传递 `application/xml` 内容，则可以使用 `@xpath()` 执行 XPath 提取，或使用 `@json()` 从 XML 转换为 JSON。 了解[处理内容类型](../logic-apps/logic-apps-content-type.md)。

此外，可以在定义中指定 JSON 架构。 该架构会使设计器生成可以传入步骤的令牌。 以下示例使 `title` 和 `name` 令牌在设计器中可用：

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

## <a name="reference-the-content-from-the-incoming-request"></a>从传入请求引用内容

`@triggerOutputs()` 函数将输出传入请求的内容。 输出如下所示：

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

若要专门访问 `body` 属性，可使用 `@triggerBody()` 快捷方式。 

## <a name="respond-to-the-request"></a>响应请求

对于某些启动逻辑应用的请求，你可能要使用一些内容来响应调用方。 若要构造响应的状态代码、正文和标头，可使用名为“响应”的操作类型。 但是，如果不包含“响应”，则逻辑应用会立即响应并显示“202 已接受”。

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

响应具有以下属性：

| 属性 | 说明 |
| --- | --- |
| statusCode |用于响应传入请求的 HTTP 状态代码。 该代码可以是以 2xx、4xx 或 5xx 开头的任何有效状态代码。 但是，不允许使用 3xx 状态代码。 |
| body |正文对象，可以是字符串、JSON 对象甚至是从上一步引用的二进制内容。 |
| headers |可以定义要包含在响应中的标头的任意数量。 |

在逻辑应用中，除非调用作为嵌套逻辑应用的工作流，否则响应所需的所有步骤必须在&60; 秒内完成，使原始请求能够获取响应。 如果在 60 秒内未发生任何响应，传入请求则将超时，并将收到“408 客户端超时”HTTP 响应。 对于嵌套逻辑应用，父逻辑应用会继续等待响应，直到完成为止（不考虑所需的时间）。

## <a name="advanced-endpoint-configuration"></a>高级终结点配置

逻辑应用具有针对直接访问终结点的内置支持，始终使用 `POST` 方法开始运行逻辑应用。 **HTTP 侦听器** API 应用以前还支持更改 URL 段和 HTTP 方法。 甚至可以设置其他安全性或自定义域，具体方法是将这些项添加到 API 应用主机（承载 API 应用的 Web 应用）。 

此功能通过 **API 管理**提供：

* [更改请求方法](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [更改请求的 URL 段](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* 在经典 Azure 门户中的“配置”选项卡上设置 API 管理域。
* 设置策略以检查基本身份验证。

## <a name="summary-of-migration-from-2014-12-01-preview"></a>从 2014-12-01-预览进行迁移的摘要

| 2014-12-01-预览 | 2016-06-01 |
| --- | --- |
| 单击“HTTP 侦听器”API 应用 |单击“手动触发器”（无需 API 应用） |
| HTTP 侦听器设置“自动发送响应” |包含“响应”操作或不在工作流定义中。 |
| 配置基本或 OAuth 身份验证 |通过 API 管理 |
| 配置 HTTP 方法 |通过 API 管理 |
| 配置相对路径 |通过 API 管理 |
| 通过 `@triggerOutputs().body.Content` 引用传入正文 |通过 `@triggerOutputs().body` 引用 |
| 对 HTTP 侦听器执行的“发送 HTTP 响应”操作 |单击“响应 HTTP 请求”（无需 API 应用） |

[1]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[2]: ./media/logic-apps-http-endpoint/manualtrigger.png
[3]: ./media/logic-apps-http-endpoint/response.png

