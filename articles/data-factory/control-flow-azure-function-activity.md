---
title: Azure 数据工厂中的 azure Function 活动
description: 了解如何使用 Azure 函数活动在数据工厂管道中运行 Azure 函数
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: ffb610634399594788afcb9b600ba00c6803dfdd
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207019"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure 数据工厂中的 Azure 函数活动

通过 Azure 函数活动可以在数据工厂管道中运行 [Azure Functions](../azure-functions/functions-overview.md)。 若要运行 Azure 函数，需要创建链接服务连接以及指定计划执行的 Azure 函数的活动。

有关此功能的 8 分钟简介和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure 函数链接服务

Azure 函数的返回类型必须是有效的 `JObject`。 （请记住， [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *不*是 `JObject`。）除 `JObject` 以外的任何返回类型都将失败，并且引发用户错误*响应内容不是有效的 JObject*。

| **属性** | **说明** | **必需** |
| --- | --- | --- |
| type   | Type 属性必须设置为： **AzureFunction** | 是 |
| 函数应用 URL | Azure 函数应用的 URL。 格式为 `https://<accountname>.azurewebsites.net`。 在 Azure 门户中查看函数应用时，此 URL 是 URL 部分下的值  | 是 |
| 函数密钥 | Azure 函数的访问密钥。 单击相应函数的“管理”部分，并复制“函数密钥”或“主机密钥”。 在此处了解详细信息： [AZURE FUNCTIONS HTTP 触发器和绑定](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | 是 |
|   |   |   |

## <a name="azure-function-activity"></a>Azure 函数活动

| **属性**  | **说明** | **允许的值** | **必需** |
| --- | --- | --- | --- |
| name  | 管道中活动的名称  | String | 是 |
| type  | 活动的类型为“AzureFunctionActivity” | String | 是 |
| 链接服务 | 相应 Azure 函数应用的 Azure 函数链接服务  | 链接服务引用 | 是 |
| 函数名称  | 此活动在 Azure 函数应用中调用的函数的名称 | String | 是 |
| method  | 函数调用的 Rest API 方法 | 字符串支持的类型： "GET"、"POST"、"PUT"   | 是 |
| 标头的值开始缓存响应  | 发送到请求的标头。 例如，若要在请求中设置语言和类型："headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | 字符串（或带有 resultType 字符串的表达式） | 否 |
| body  | 随请求一起发送到函数 API 方法的正文  | 字符串（或带有 resultType 字符串的表达式）或对象。   | PUT/POST 方法所必需 |
|   |   |   | |

请参阅 [请求有效负载架构](control-flow-web-activity.md#request-payload-schema) 部分中的请求有效负载架构。

## <a name="routing-and-queries"></a>路由和查询

Azure 函数活动支持**路由**。 例如，如果 Azure 函数的终结点 `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`，则 `<functionName>/<value>`Azure Function 活动中要使用的 `functionName`。 可以将此函数参数化，以便在运行时提供所需的 `functionName`。

Azure 函数活动还支持**查询**。 查询必须作为 `functionName`的一部分包含。 例如，当函数名称为 `HttpTriggerCSharp` 并且要包含的查询 `name=hello`时，可以将 Azure Function 活动中的 `functionName` 构造为 `HttpTriggerCSharp?name=hello`。 此函数可以参数化，以便可以在运行时确定该值。

## <a name="timeout-and-long-running-functions"></a>超时和长时间运行的函数

Azure Functions 在230秒后超时，而不考虑在设置中配置 `functionTimeout` 设置。 有关详细信息，请参阅[此文章](../azure-functions/functions-versions.md#timeout)。 若要解决此行为，请遵循异步模式或使用 Durable Functions。 Durable Functions 的好处在于它们提供自己的状态跟踪机制，因此您无需实现自己的状态跟踪机制。

在[本文](../azure-functions/durable/durable-functions-overview.md)中详细了解 Durable Functions。 可以设置 Azure Function 活动以调用持久性函数，该函数将返回具有不同 URI 的响应，如[本示例](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery)所示。 由于 `statusQueryGetUri` 在函数运行时返回 HTTP 状态202，因此你可以使用 Web 活动轮询函数的状态。 只需设置一个 Web 活动，并将 "`url`" 字段设置为 "`@activity('<AzureFunctionActivityName>').output.statusQueryGetUri`。 当持久性函数完成时，函数的输出将是 Web 活动的输出。


## <a name="sample"></a>示例

可在[此处](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)找到使用 Azure 函数提取 tar 文件内容的数据工厂的示例。

## <a name="next-steps"></a>后续步骤

在[Azure 数据工厂中的管道和活动](concepts-pipelines-activities.md)中了解有关数据工厂中的活动的详细信息。
