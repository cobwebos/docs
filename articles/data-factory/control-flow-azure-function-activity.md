---
title: Azure 数据工厂中的 Azure 函数活动 | Microsoft Docs
description: 了解如何使用 Azure 函数活动在数据工厂管道中运行 Azure 函数
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: b98d20a1f96a6ab4a0dc72330e85fdc98ba04eae
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576372"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure 数据工厂中的 Azure 函数活动

通过 Azure 函数活动可以在数据工厂管道中运行 [Azure Functions](../azure-functions/functions-overview.md)。 若要运行 Azure 函数，需要创建链接服务连接以及指定计划执行的 Azure 函数的活动。

有关此功能的 8 分钟简介和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure 函数链接服务

Azure 函数的返回类型必须是有效的 `JObject`。 （请记住：[JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) 不是 `JObject`。）除 `JObject` 之外的任何其他返回类型都会失败并引发通用用户错误“调用终结点时出错”。

| **属性** | **说明** | **必需** |
| --- | --- | --- |
| type   | type 属性必须设置为：**AzureFunction** | 是 |
| 函数应用 URL | Azure 函数应用的 URL。 格式为 `https://<accountname>.azurewebsites.net`。 在 Azure 门户中查看函数应用时，此 URL 是 URL 部分下的值  | 是 |
| 函数密钥 | Azure 函数的访问密钥。 单击相应函数的“管理”部分，并复制“函数密钥”或“主机密钥”。 在此处了解详细信息：[Azure Functions HTTP 触发器和绑定](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | 是 |
|   |   |   |

## <a name="azure-function-activity"></a>Azure 函数活动

| **属性**  | **说明** | **允许的值** | **必需** |
| --- | --- | --- | --- |
| 名称  | 管道中活动的名称  | String | 是 |
| type  | 活动的类型为“AzureFunctionActivity” | String | 是 |
| 链接服务 | 相应 Azure 函数应用的 Azure 函数链接服务  | 链接服务引用 | 是 |
| 函数名称  | 此活动在 Azure 函数应用中调用的函数的名称 | String | 是 |
| 方法  | 函数调用的 Rest API 方法 | 字符串支持的类型：“GET”、“POST”、“PUT”   | 是 |
| 标头的值开始缓存响应  | 发送到请求的标头。 例如，若要在请求中设置语言和类型："headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | 字符串（或带有 resultType 字符串的表达式） | 否 |
| body  | 随请求一起发送到函数 API 方法的正文  | 字符串（或带有 resultType 字符串的表达式）或对象。   | PUT/POST 方法所必需 |
|   |   |   | |

请参阅 [请求有效负载架构](control-flow-web-activity.md#request-payload-schema) 部分中的请求有效负载架构。

## <a name="more-info"></a>更多信息

Azure 函数活动支持**路由**。 例如，如果应用使用路由 `https://functionAPP.azurewebsites.net/api/functionName/{value}?code=<secret>`，则 `functionName` 为 `functionName/{value}`，你可以将其参数化以在运行时提供所需的 `functionName`。

Azure 函数活动还支持**查询**。 查询必须是 `functionName` 的一部分（例如，`HttpTriggerCSharp2?name=hello`），其中 `function name` 为 `HttpTriggerCSharp2`。

## <a name="next-steps"></a>后续步骤

在[Azure 数据工厂中的管道和活动](concepts-pipelines-activities.md)中了解有关数据工厂中的活动的详细信息。
