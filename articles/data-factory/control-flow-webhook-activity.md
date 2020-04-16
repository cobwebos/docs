---
title: Azure 数据工厂中的 Webhook 活动
description: Webhook 活动不会继续执行管道，直到它使用用户指定的特定条件验证附加数据集。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 4056550ae0a71138d136878fc7e3aa5f6f8f4180
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417872"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure 数据工厂中的 Webhook 活动

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Webhook 活动可以通过自定义代码控制管道的执行。 使用 Webhook 活动时，客户代码可以调用终结点并将其传递回调 URL。 管道运行等待回调调用，然后再继续下一个活动。

## <a name="syntax"></a>语法

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Type 属性

properties | 说明 | 允许的值 | 必选
-------- | ----------- | -------------- | --------
name  | 网钩活动的名称。 | 字符串 | 是 |
**type** | 必须设置为"网络挂"。 | 字符串 | 是 |
**方法** | 目标终结点的 REST API 方法。 | 字符串。 支持的类型为"POST"。 | 是 |
**url** | 目标终结点和路径。 | 具有字符串**结果类型**值的字符串或表达式。 | 是 |
**头** | 发送到请求的标头。 下面是一个在请求上设置语言和类型的示例： `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`。 | 具有字符串**结果类型**值的字符串或表达式。 | 是的。 需要`Content-Type`这样的`"headers":{ "Content-Type":"application/json"}`标头。 |
**身体** | 表示要发送到终结点的有效负载。 | 有效的 JSON 或具有 JSON**的结果类型**值的表达式。 有关请求有效负载的架构，请参阅[请求有效负载架构](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#request-payload-schema)。 | 是 |
**认证** | 用于调用终结点的身份验证方法。 支持的类型是"基本"和"客户端证书"。 有关详细信息，请参阅[身份验证](https://docs.microsoft.com/azure/data-factory/control-flow-web-activity#authentication)。 如果不需要身份验证，则排除此属性。 | 具有字符串**结果类型**值的字符串或表达式。 | 否 |
**超时** | 活动等待**调用 callBackUri**指定的回调的时间。 默认值为 10 分钟（"00：10：00"）。 值具有时间跨度格式*d*。*hh*：*毫米*：*s.* | 字符串 | 否 |
**回调时报告状态** | 允许用户报告 Webhook 活动的失败状态。 | Boolean | 否 |

## <a name="authentication"></a>身份验证

Webhook 活动支持以下身份验证类型。

### <a name="none"></a>无

如果不需要身份验证，请不要包含**身份验证**属性。

### <a name="basic"></a>基本

指定用于基本身份验证的用户名和密码。

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>客户端证书

指定 PFX 文件和密码的 Base64 编码内容。

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>托管标识

使用数据工厂的托管标识指定为其请求访问令牌的资源 URI。 若要调用 Azure 资源管理 API，请使用 `https://management.azure.com/`。 有关托管标识的工作原理的详细信息，请参阅[Azure 资源概述的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)。

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> 如果数据工厂配置了 Git 存储库，则必须在 Azure 密钥保管库中存储凭据才能使用基本身份验证或客户端证书身份验证。 Azure 数据工厂不将密码存储在 Git 中。

## <a name="additional-notes"></a>附加说明

数据工厂传递发送到 URL 终结点的正文中的附加属性**调用 BackUri。** 数据工厂希望此 URI 在指定的超时值之前调用。 如果未调用 URI，则活动将失败，状态为"超时"。

当对自定义终结点的调用失败时，Webhook 活动将失败。 任何错误消息都可以添加到回调正文，并在以后的活动中使用。

对于每个 REST API 调用，如果终结点在一分钟内未响应，客户端都会超时。 此行为是标准 HTTP 最佳做法。 要解决此问题，实现 202 模式。 在当前情况下，终结点返回 202（已接受）和客户端轮询。

请求上的一分钟超时与活动超时无关。 后者用于等待**回调 Uri**指定的回调。

传递到回调 URI 的正文必须有效的 JSON。 将 `Content-Type` 标头设置为 `application/json`。

在**回调属性上使用"报表"状态**时，在进行回调时，必须向正文添加以下代码：

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>后续步骤

请参阅数据工厂支持的以下控制流活动：

- [If Condition 活动](control-flow-if-condition-activity.md)
- [执行管道活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [获取元数据活动](control-flow-get-metadata-activity.md)
- [查找活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
- [Until 活动](control-flow-until-activity.md)
