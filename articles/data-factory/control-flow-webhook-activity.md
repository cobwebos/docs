---
title: Azure 数据工厂中的 Webhook 活动
description: Webhook 活动在用用户指定的特定条件验证附加的数据集之前，不会继续执行管道。
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
ms.openlocfilehash: 8c52bb21276071581a83fb3ee6a3a4a31ba0bb4a
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399996"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure 数据工厂中的 Webhook 活动
您可以使用 webhook 活动通过您的自定义代码来控制管道的执行。 使用 webhook 活动，客户可以调用端点并传递回调 URL。 管道运行将等待回调调用，然后再继续下一个活动。

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
name | Web 挂钩活动的名称 | String | 是 |
type | 必须设置为**WebHook**。 | String | 是 |
method | 目标终结点的 Rest API 方法。 | 字符串。 支持的类型： "POST" | 是 |
url | 目标终结点和路径 | 字符串（或带有 resultType 字符串的表达式）。 | 是 |
headers | 发送到请求的标头。 例如，若要在请求中设置语言和类型： "标头"： {"Accept-Language"： "en-us"，"Content-type"： "application/json"}。 | 字符串（或带有 resultType 字符串的表达式） | 是，需要内容类型标头。 "标头"： {"Content-type"： "application/json"} |
body | 表示要发送到终结点的有效负载。 | 有效的 JSON （or resultType 为 JSON 的表达式）。 请参阅[请求有效负载架构](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0)部分中的请求有效负载架构。 | 是 |
身份验证 | 用于调用该终结点的身份验证方法。 支持的类型为 "基本" 或 "ClientCertificate"。 有关详细信息，请参阅[身份验证](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0)部分。 如果不需要身份验证，则排除此属性。 | 字符串（或带有 resultType 字符串的表达式） | 否 |
timeout | 活动等待&#39;callBackUri&#39;调用的时间长度。 活动等待 "callBackUri" 调用的时间长度。 默认值为10分钟入门（"00:10:00"）。 格式为 Timespan，如 d. hh： mm： ss | String | 否 |
回拨时报告状态 | 允许用户报告 webhook 活动的失败状态，这会将活动标记为失败 | Boolean | 否 |

## <a name="authentication"></a>Authentication

下面是 webhook 活动中支持的身份验证类型。

### <a name="none"></a>无

如果不需要身份验证，请排除“身份验证”属性。

### <a name="basic"></a>基本

指定用户名和密码以用于基本身份验证。

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>客户端证书

指定 base64 编码的 PFX 文件内容和密码。

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>托管标识

使用数据工厂的托管标识指定要为其请求访问令牌的资源 URI。 若要调用 Azure 资源管理 API，请使用 `https://management.azure.com/`。 有关如何托管标识工作原理的详细信息，请参阅 [Azure 资源概述页面的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)。

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> 如果你的数据工厂配置了 git 存储库，则必须将你的凭据存储在 Azure Key Vault 中，才能使用基本身份验证或客户端证书身份验证。 Azure 数据工厂不会在 git 中存储密码。

## <a name="additional-notes"></a>附加说明

Azure 数据工厂会将正文中的附加属性 "callBackUri" 传递给 url 终结点，并将在指定超时值之前调用此 uri。 如果未调用 uri，则活动将失败，状态为 "超时"。

当对自定义终结点的调用失败时，webhook 活动本身会失败。 任何错误消息都可以添加到回调的主体中，并在后续活动中使用。

对于每个 REST API 调用，如果终结点未在1分钟内响应，则客户端将超时。这是标准的 http 最佳实践。 若要解决此问题，在这种情况下，需要实现202模式，在这种情况下，终结点将返回202（接受），客户端将轮询。

请求的1分钟超时与活动超时无关。 用于等待 callbackUri 的。

传递回回调 URI 的正文应是有效的 JSON。 必须将 Content-type 标头设置为 `application/json`。

当使用 "回叫时报告状态" 选项时，必须在执行回调时将以下代码片段添加到正文：

```
{
    "Output": {
        // output object will be used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity will be marked as failed
}
```



## <a name="next-steps"></a>后续步骤

查看数据工厂支持的其他控制流活动：

- [If Condition 活动](control-flow-if-condition-activity.md)
- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
- [Until 活动](control-flow-until-activity.md)
