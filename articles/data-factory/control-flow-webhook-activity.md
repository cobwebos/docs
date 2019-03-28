---
title: Azure 数据工厂中的 Webhook 活动 |Microsoft Docs
description: Webhook 活动不会继续执行管道，直到它验证了用户指定特定条件的附加数据集。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 0b8b892f02e54c3b0ddb155af97ce63ff115bb1f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522887"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure 数据工厂中的 Webhook 活动
Web 挂钩活动可用于通过自定义代码控制执行管道。 使用 webhook 活动，客户可以调用终结点，并将传递回叫 URL。 管道运行等待下一个活动的继续操作之前调用的回调。

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



属性 | 说明 | 允许的值 | 需要
-------- | ----------- | -------------- | --------
名称 | Web 挂钩活动的名称 | String | 是 |
类型 | 必须设置为**WebHook**。 | String | 是 |
方法 | 目标终结点的 Rest API 方法。 | 字符串。 支持的类型：POST | 是 |
url | 目标终结点和路径 | 字符串（或带有 resultType 字符串的表达式）。 | 是 |
headers | 发送到请求的标头。 例如，若要在请求中设置的语言和类型:"标头": {"接受语言":"en-我们"、"内容类型":"application/json"}。 | 字符串（或带有 resultType 字符串的表达式） | 是，需要内容类型标头。 "headers":{ "Content-Type":"application/json"} |
正文 | 表示要发送到终结点的有效负载。 | 正文传递回调用返回 URI 应为有效的 JSON。 请参阅[请求有效负载架构](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23request-payload-schema&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=ljUZv5csQQux2TT3JtTU9ZU8e1uViRzuX5DSNYkL0uE%3D&amp;reserved=0)部分中的请求有效负载架构。 | 是 |
身份验证 | 用于调用该终结点的身份验证方法。 支持的类型为"基本"或"ClientCertificate"。 有关详细信息，请参阅[身份验证](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fdata-factory%2Fcontrol-flow-web-activity%23authentication&amp;data=02%7C01%7Cshlo%40microsoft.com%7Cde517eae4e7f4f2c408d08d6b167f6b1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636891457414397501&amp;sdata=GdA1%2Fh2pAD%2BSyWJHSW%2BSKucqoAXux%2F4L5Jgndd3YziM%3D&amp;reserved=0)部分。 如果不需要身份验证，则排除此属性。 | 字符串（或带有 resultType 字符串的表达式） | 否 |
超时 | 该活动将等待多长时间&#39;的 callbackuri&#39;调用。 该活动将等待时间 callbackuri 被调用。 默认值是 10 分钟入门 ("00: 10:00")。 格式为 Timespan 即 d.hh:mm:ss | String | 否 |

## <a name="additional-notes"></a>其他注释

Azure 数据工厂将传入的其他属性"的 callbackuri"在正文中的 url 终结点，并将预期此 uri 指定的超时值之前调用。 如果未调用的 uri，则活动会失败，状态超时。

无法正常工作的 web 挂钩活动本身，仅当对自定义终结点的调用失败。 可以添加到的回调的正文和后续活动中使用的任何错误消息。

## <a name="next-steps"></a>后续步骤
查看数据工厂支持的其他控制流活动：

- [If Condition 活动](control-flow-if-condition-activity.md)
- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
- [Until 活动](control-flow-until-activity.md)
