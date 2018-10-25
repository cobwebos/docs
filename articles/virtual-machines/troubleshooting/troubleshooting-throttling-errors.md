---
title: 排查 Azure 中的限制错误 | Microsoft Docs
description: Azure 计算中的限制错误、重试和回退。
services: virtual-machines
documentationcenter: ''
author: changov
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: vashan, rajraj, changov
ms.openlocfilehash: b951d0b8d91729340cf382e70f72511fb009053e
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386546"
---
# <a name="troubleshooting-api-throttling-errors"></a>排查 API 限制错误 

Azure 计算请求可能会根据订阅和区域进行限制，以便优化服务的总体性能。 我们会确保对 Azure 计算资源提供程序（CRP，用于管理 Microsoft.Compute 命名空间中的资源）的所有调用不超出允许的最大 API 请求速率。 本文档介绍 API 限制、有关如何排查限制问题的详细信息，以及如何避免受限的最佳做法。  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Azure 资源管理器限制与资源提供程序限制  

作为 Azure 的“前门”，Azure 资源管理器会对所有传入的 API 请求进行身份验证、第一级验证和限制。 [此处](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-request-limits)介绍了 Azure 资源管理器调用速率限制和相关的诊断响应 HTTP 标头。
 
当 Azure API 客户端收到限制错误时，HTTP 状态为“429 请求过多”。 若要了解请求限制是由 Azure 资源管理器施加的还是由基础资源提供程序（例如 CRP）施加的，请检查 `x-ms-ratelimit-remaining-subscription-reads`（针对 GET 请求）和 `x-ms-ratelimit-remaining-subscription-writes` 响应标头（针对非 GET 请求）。 如果剩余调用计数接近 0，则表明已达到订阅的常规调用限制（由 Azure 资源管理器定义）。 所有订阅客户端的活动会一起计数。 否则，限制由目标资源提供程序（请求 URL 的 `/providers/<RP>` 段所指的提供程序）施加。 

## <a name="call-rate-informational-response-headers"></a>调用速率信息响应标头 

| 标头                            | 值格式                           | 示例                               | Description                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-remaining-resource |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | 限制策略（涵盖资源 Bucket 或操作组，包括此请求的目标）的剩余 API 调用计数                                                                   |
| x-ms-request-charge               | ```<count>   ```                             | 1                                     | 针对此 HTTP 请求进行的调用计数计入相应策略的限制。 这通常为 1。 针对特殊情况（例如针对虚拟机规模集的缩放）的批请求可以有多个计数。 |


请注意，一个 API 请求可能受多个限制策略的约束。 每个策略会有单独的 `x-ms-ratelimit-remaining-resource` 标头。 

下面是删除虚拟机规模集请求的示例响应。

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>限制错误详细信息

429 HTTP 状态通常用于在达到调用速率限制时拒绝某个请求。 来自计算资源提供程序的常规限制错误响应将如以下示例所示（仅显示相关的标头）：

```
HTTP/1.1 429 Too Many Requests
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet3Min;46
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet30Min;0
Retry-After: 1200
Content-Type: application/json; charset=utf-8
{
  "code": "OperationNotAllowed",
  "message": "The server rejected the request because too many requests have been received for this subscription.",
  "details": [
    {
      "code": "TooManyRequests",
      "target": "HighCostGet30Min",
      "message": "{\"operationGroup\":\"HighCostGet30Min\",\"startTime\":\"2018-06-29T19:54:21.0914017+00:00\",\"endTime\":\"2018-06-29T20:14:21.0914017+00:00\",\"allowedRequestCount\":800,\"measuredRequestCount\":1238}"
    }
  ]
}

```

剩余调用计数为 0 时，将根据相关策略返回限制错误。 在此示例中，该策略为 `HighCostGet30Min`。 响应正文的总体格式是 Azure 资源管理器 API 的常规错误格式（与 OData 相符）。 主要错误代码 `OperationNotAllowed` 是计算资源提供程序用来报告限制错误（以及其他类型的客户端错误）的代码。 内部错误的 `message` 属性包含一个具有限制冲突详细信息的序列化 JSON 结构。

如上所述，每个限制错误都包含 `Retry-After` 标头，其提供的最小秒数是客户端在重试请求之前应该等待的时间。 

## <a name="best-practices"></a>最佳做法 

- 请勿无条件地以及（或者）立即地重试 Azure 服务 API 错误。 遇到不可重试的错误时，常见的情况是客户端代码会进入快速的重试循环。 重试最终会耗光目标操作对应的组的允许调用限制，影响订阅的其他客户端。 
- 在大容量 API 自动化示例中，如果目标操作组的可用调用计数掉到某个较低的阈值以下，则可考虑实施前摄性客户端自动限制。 
- 跟踪异步操作时，请遵循 Retry-After 标头提示。 
- 如果客户端代码需要特定虚拟机的信息，请直接查询该 VM，不需先列出包含资源组或整个订阅中的所有 VM，然后选取客户端的所需 VM。 
- 如果客户端代码需要特定 Azure 位置的 VM、磁盘和快照，请使用基于位置的查询形式，不需先查询所有订阅 VM，然后在客户端按位置进行筛选：`GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` 查询针对计算资源提供程序区域终结点。 
-   创建或更新 API 资源（尤其是 VM 和虚拟机规模集）时，跟踪返回的异步操作直至完成比针对资源 URL 本身进行轮询（基于 `provisioningState`）要有效得多。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 中的其他服务的重试指南，请参阅[特定服务的重试指南](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)
