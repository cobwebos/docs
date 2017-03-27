---
title: "Azure Resource Manager 请求限制 | Microsoft Docs"
description: "介绍在达到订阅限制时，如何对 Azure Resource Manager 请求使用限制。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: e1047233-b8e4-4232-8919-3268d93a3824
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 4029b699b59bb12eaa9e24b487d2829b5fb26daf
ms.openlocfilehash: 6780b422138fbe18adfe256e9f7aa279dfed1cd9


---
# <a name="throttling-resource-manager-requests"></a>限制 Resource Manager 请求
对于每个订阅和租户，Resource Manager 将每小时的读取请求数限制为 15,000 个，将每小时的写入请求数限制为 1,200 个。 如果应用程序或脚本达到这些限制，则需要限制请求。 本主题说明如何在达到限制之前确定剩余的请求数，以及达到限制时如何做出响应。

达到限制时，你会收到 HTTP 状态代码“429 请求过多”。

请求数划归到订阅或租户。 如果订阅中有多个并发应用程序在发出请求，这些应用程序的请求数将会累加，共同确定剩余的请求数。

划归到订阅的请求涉及到传递订阅 ID，例如，检索订阅中的资源组。 划归到租户的请求不包括订阅 ID，例如，检索有效的 Azure 位置。

## <a name="remaining-requests"></a>剩余的请求数
可以通过检查响应标头来确定剩余的请求数。 每个请求包含剩余读取和写入请求数的值。 下表描述了可在其中检查这些值的标头：

| 响应标头 | 说明 |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |划归到订阅的剩余读取数 |
| x-ms-ratelimit-remaining-subscription-writes |划归到订阅的剩余写入数 |
| x-ms-ratelimit-remaining-tenant-reads |划归到租户的剩余读取数 |
| x-ms-ratelimit-remaining-tenant-writes |划归到租户的剩余写入数 |
| x-ms-ratelimit-remaining-subscription-resource-requests |划归到订阅的剩余资源请求类型数。<br /><br />仅当服务重写了默认限制时，才返回此标头值。 Resource Manager 将累加此值而不是订阅读取/写入数。 |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |划归到订阅的剩余资源请求集合数。<br /><br />仅当服务重写了默认限制时，才返回此标头值。 此值提供剩余集合请求数（列出资源）。 |
| x-ms-ratelimit-remaining-tenant-resource-requests |划归到租户的剩余资源请求类型数。<br /><br />仅当服务重写了默认限制时，才为租户级别的请求添加此标头。 Resource Manager 将累加此值而不是租户读取/写入数。 |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |划归到租户的剩余资源请求集合数。<br /><br />仅当服务重写了默认限制时，才为租户级别的请求添加此标头。 |

## <a name="retrieving-the-header-values"></a>检索标头值
检索代码或脚本中的这些标头值与检索任何标头值没有什么不同。 

例如，在 **C#** 中，可使用以下代码从名为 **response** 的 **HttpWebResponse** 对象中检索标头值：

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

在 **PowerShell** 中，可以从 Invoke-WebRequest 操作检索标头值。

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

或者，如果想要查看剩余请求数以进行调试，可以在 **PowerShell** cmdlet 中提供 **-Debug** 参数。

```powershell
Get-AzureRmResourceGroup -Debug
```

这会返回许多值，包括以下响应值：

```powershell
...
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 14999
...
```

在 **Azure CLI** 中，可以使用更详细的选项检索标头值。

```azurecli
azure group list -vv --json
```

这会返回许多值，包括以下对象：

```azurecli
...
silly: returnObject
{
  "statusCode": 200,
  "header": {
    "cache-control": "no-cache",
    "pragma": "no-cache",
    "content-type": "application/json; charset=utf-8",
    "expires": "-1",
    "x-ms-ratelimit-remaining-subscription-reads": "14998",
    ...
```

## <a name="waiting-before-sending-next-request"></a>发送下一个请求之前等待一段时间
达到请求限制时，Resource Manager 会在标头中返回 **429** HTTP 状态代码和 **Retry-After** 值。 **Retry-After** 值指定在发送下一个请求之前应用程序应该等待（或休眠）秒数。 如果在尚未到达重试时间值的情况下发送请求，该请求不会受到处理，同时会返回一个新的重试时间值。

## <a name="next-steps"></a>后续步骤

* 有关限制和配额的详细信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-subscription-service-limits.md)。
* 若要了解如何处理异步 REST 请求，请参阅[跟踪异步 Azure 操作](resource-manager-async-operations.md)。



<!--HONumber=Feb17_HO3-->


