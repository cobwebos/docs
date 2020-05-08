---
title: 异步操作的状态
description: 介绍如何在 Azure 中跟踪异步操作。 它显示用于获取长时间运行操作的状态的值。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: fb3479271733ee218da8a842956f3a8996e07f61
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82892012"
---
# <a name="track-asynchronous-azure-operations"></a>跟踪异步 Azure 操作
某些 Azure REST 操作以异步方式运行，因为操作无法快速完成。 本文介绍如何通过响应中返回的值跟踪异步操作的状态。  

## <a name="status-codes-for-asynchronous-operations"></a>异步操作的状态代码
最初，异步操作返回下面的一个 HTTP 状态代码：

* 201 (已创建)
* 202 (已接受) 

该操作在成功完成时返回下面的一个代码：

* 200 (正常)
* 204 (无内容) 

请参阅 [REST API 文档](/rest/api/)，了解要执行的操作的响应。

## <a name="monitor-status-of-operation"></a>监视操作状态
异步 REST 操作返回标头值，用于确定操作的状态。 可能有三个需要检查的标头值：

* `Azure-AsyncOperation` - URL，用于检查操作目前的状态。 如果操作返回此值，则应始终使用它（而不是 Location）来跟踪操作的状态。
* `Location` - URL，用于确定操作的完成时间。 只有在未返回 Azure-AsyncOperation 时，才使用此值。
* `Retry-After` - 在检查异步操作的状态之前等待的秒数。

但是，并非每个异步操作都会返回所有这些值。 例如，可能需要对一个操作计算 Azure-AsyncOperation 标头值，对另一个操作计算 Location 标头值。 

检索这些标头值与检索请求的任何标头值一样。 例如，在 C# 中，可以使用以下代码从名为 `HttpWebResponse` 的 `response` 对象检索标头值：

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Azure-AsyncOperation 请求和响应

若要获取异步操作的状态，请向 Azure-AsyncOperation 标头值中的 URL 发送 GET 请求。

来自此操作的响应的正文包含有关操作的信息。 以下示例显示从操作中返回的可能值：

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}", 
    "status" : "Succeeded | Failed | Canceled | {resource provider values}", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : { 
        "code": "{error code}",  
        "message": "{error description}" 
    }
}
```

所有响应都只返回 `status`。 当状态为“已失败”或“已取消”时，返回错误对象。 所有其他值都是可选的；因此，收到的响应看起来可能不同于示例。

## <a name="provisioningstate-values"></a>provisioningState 值

用于创建、更新或删除（PUT、PATCH、DELETE）资源的操作通常返回 `provisioningState` 值。 完成操作后，将返回以下三个值之一： 

* 已成功
* 失败
* 已取消

所有其他值表示该操作仍在运行。 资源提供程序可以返回自定义的值，用于指示其状态。 例如，当请求已收到且正在运行时，用户会收到“已接受”  。


## <a name="example-requests-and-responses"></a>示例请求和响应

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>启动虚拟机（Azure-AsyncOperation 标头出现 202 响应）
此示例演示如何确定虚拟机的“启动”  操作的状态。 初始请求采用以下格式：

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

它返回状态代码 202。 在标头值中可以看到：

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

若要检查异步操作的状态，请向该 URL 发送另一请求。

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

响应正文包含操作的状态：

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>部署资源（Azure-AsyncOperation 标头出现 201 响应）

此示例演示将资源部署到 Azure 时，如何确定“部署”  操作的状态。 初始请求采用以下格式：

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

它返回状态代码 201。 响应的正文包括：

```json
"provisioningState":"Accepted",
```

在标头值中可以看到：

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

若要检查异步操作的状态，请向该 URL 发送另一请求。

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

响应正文包含操作的状态：

```json
{"status":"Running"}
```

部署完成后，响应包含：

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>创建存储帐户（Location 和 Retry-After 标头出现 202 响应）

此示例演示如何确定存储帐户的“创建”  操作的状态。 初始请求采用以下格式：

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

请求正文包含存储帐户的属性：

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

它返回状态代码 202。 在标头值中会出现以下两个值：

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

等待特定的秒数（在 Retry-After 中指定）后，查看异步操作的状态，方法是向该 URL 发送另一请求。

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

如果请求仍在运行，则会收到状态代码 202。 如果请求已完成，则会收到状态代码 200，且响应的正文包含已创建存储帐户的属性。

## <a name="next-steps"></a>后续步骤

* 有关每个 REST 操作的文档，请参阅 [REST API 文档](/rest/api/)。
* 有关通过资源管理器 REST API 部署模板的信息，请参阅[使用资源管理器模板和资源管理器 REST API 部署资源](../azure-resource-manager/templates/deploy-rest.md)。