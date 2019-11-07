---
title: Azure 数据工厂中的 Webhook 活动
description: Webhook 活动在使用用户指定的某些条件验证附加的数据集之前，不会继续执行管道。
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
ms.openlocfilehash: 3734472d9026d4e355c08b36d5ba58974288daac
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678229"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure 数据工厂中的 Webhook 活动
可以使用 Webhook 活动通过自定义代码控制管道的执行。 使用 Webhook 活动，客户可以调用终结点并传递回调 URL。 管道运行在继续下一个活动之前，等待调用回调。

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



属性 | 说明 | 允许的值 | 必选
-------- | ----------- | -------------- | --------
name | Webhook 活动的名称 | String | 是 |
type | 必须设置为 **WebHook**。 | String | 是 |
method | 目标终结点的 Rest API 方法。 | 字符串。 支持的类型： "POST" | 是 |
url | 目标终结点和路径 | 字符串（或带有 resultType 字符串的表达式）。 | 是 |
headers | 发送到请求的标头。 例如，若要在请求中设置语言和类型："headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }。 | 字符串（或带有 resultType 字符串的表达式） | 是，需要内容类型标头。 "headers":{ "Content-Type":"application/json"} |
body | 表示要发送到终结点的有效负载。 | 传递回回调 URI 的主体应该是有效的 JSON。 请参阅[请求有效负载架构](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0)部分中的请求有效负载架构。 | 是 |
authentication | 用于调用该终结点的身份验证方法。 支持的类型为“Basic”或“ClientCertificate”。 有关详细信息，请参阅[身份验证](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0)部分。 如果不需要身份验证，则排除此属性。 | 字符串（或带有 resultType 字符串的表达式） | 否 |
timeout | 活动将等待多长时间才能调用 &#39;callBackUri&#39;。 活动将等待多长时间才能调用“callBackUri”。 默认值为 10 分钟 (“00:10:00”)。 格式为 Timespan，即 d.hh:mm:ss | String | 否 |

## <a name="additional-notes"></a>附加说明

Azure 数据工厂会将正文中的附加属性“callBackUri”传递给 url 终结点，并且需要在达到指定超时值之前调用此 uri。 如果未调用此 uri，则活动将失败，其状态为“超时”。

仅当对自定义终结点的调用失败时，Webhook 活动本身才会失败。 可以将任何错误消息添加到回调正文中，并在后续活动中使用。

## <a name="next-steps"></a>后续步骤
请参阅数据工厂支持的其他控制流活动：

- [If Condition 活动](control-flow-if-condition-activity.md)
- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
- [Until 活动](control-flow-until-activity.md)
