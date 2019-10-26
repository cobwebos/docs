---
title: 请求限制 - Azure 资源管理器 | Microsoft Azure
description: 介绍在达到订阅限制时，如何对 Azure 资源管理器请求使用限制。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 29d319541e92abfc52cb3f351aeaf50fc5d5687b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931854"
---
# <a name="throttling-resource-manager-requests"></a>限制 Resource Manager 请求

本文介绍 Azure 资源管理器如何限制请求。 它向您演示如何在达到限制之前跟踪剩余的请求数，以及在达到限制时如何做出响应。

限制发生在两个级别。 Azure 资源管理器限制订阅和租户的请求。 如果请求低于订阅和租户的限制限制，资源管理器会将请求路由到资源提供程序。 资源提供程序会应用针对其操作定制的限制限制。 下图显示了如何在请求从用户到 Azure 资源管理器和资源提供程序时应用限制。

![请求限制](./media/resource-manager-request-limits/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>订阅和租户限制

每个订阅级别和租户级别操作都受到限制。 订阅请求涉及到传递订阅 ID，例如，检索订阅中的资源组。 租户请求（例如，检索有效的 Azure 位置）不包括订阅 ID。

下表显示了每小时的默认限制限制。

| 范围 | Operations | Limit |
| ----- | ---------- | ------- |
| Subscription | 内容 | 12000 |
| Subscription | 清除 | 15000 |
| Subscription | 写 | 1200 |
| 租户 | 内容 | 12000 |
| 租户 | 写 | 1200 |

这些限制的范围局限于发出请求的安全主体（用户或应用程序）以及订阅 ID 或租户 ID。 如果请求来自多个安全主体，则对订阅或租户的限制高于每小时 12,000 个和 1,200 个。

这些限制适用于每个 Azure 资源管理器实例。 每个 Azure 区域中有多个实例，Azure 资源管理器将部署到所有 Azure 区域。  因此，在实践中，限制高于这些限制。 来自用户的请求通常由 Azure 资源管理器的不同实例处理。

## <a name="resource-provider-limits"></a>资源提供程序限制

资源提供程序应用自己的限制限制。 由于资源管理器按主体 ID 和资源管理器实例进行限制，因此资源提供程序可能会收到比上一部分的默认限制更多的请求。

本部分讨论一些广泛使用的资源提供程序的限制。

### <a name="storage-throttling"></a>存储限制

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>网络限制

Microsoft 网络资源提供程序应用以下限制：

| Operation | Limit |
| --------- | ----- |
| 写入/删除（PUT） | 每5分钟1000 |
| 读取（GET） | 每5分钟10000 |

### <a name="compute-throttling"></a>计算限制

有关计算操作的限制限制的信息，请参阅[API 限制错误故障排除-计算](../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md)。

若要检查虚拟机规模集中的虚拟机实例，请使用[虚拟机规模集操作](/rest/api/compute/virtualmachinescalesetvms)。 例如，使用[虚拟机规模集 vm-](/rest/api/compute/virtualmachinescalesetvms/list)包含参数的列表来检查虚拟机实例的电源状态。 此 API 减少请求数。

### <a name="azure-resource-graph-throttling"></a>Azure 资源关系图限制

Azure 资源图限制对其操作的请求数。 本文中的步骤确定剩余的请求以及在达到限制时如何做出响应也适用于资源图。 但是，资源图表会设置其自己的限制和重置速率。 有关详细信息，请参阅 [Azure Resource Graph 中的限制](../governance/resource-graph/overview.md#throttling)。

## <a name="request-increase"></a>请求增加

有时，可以增加限制。 若要查看是否可以增加方案的限制限制，请创建支持请求。 将评估调用模式的详细信息。

## <a name="error-code"></a>错误代码

达到限制时，会收到 HTTP 状态代码“429 请求过多”。 响应包含 "**重试后**的值" 值，指定应用程序在发送下一个请求之前应等待（或休眠）的秒数。 如果在尚未达到重试时间值的情况下发送请求，该请求不会得到处理，并会返回一个新的重试时间值。

等待指定的时间后，还可以关闭并重新打开与 Azure 的连接。 通过重置连接，可以连接到 Azure 资源管理器的其他实例。

如果使用的是 Azure SDK，SDK 可能会自动重试配置。 有关详细信息，请参阅[Azure 服务的重试指南](/azure/architecture/best-practices/retry-service-specific)。

某些资源提供程序返回429，以报告临时问题。 此问题可能是不是由您的请求直接导致的重载情况。 或者，它可能表示有关目标资源或依赖资源的状态的临时错误。 例如，当目标资源被其他操作锁定时，网络资源提供程序将返回429并返回**RetryableErrorDueToAnotherOperation**错误代码。 若要确定错误是来自限制还是暂时性情况，请查看响应中的错误详细信息。

## <a name="remaining-requests"></a>剩余的请求数

可以通过检查响应标头来确定剩余的请求数。 读取请求会在标头中返回剩余读取请求数的值。 写入请求包含剩余写入请求数的值。 下表描述了可在其中检查这些值的标头：

| 响应标头 | 描述 |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |划归到订阅的剩余读取数。 执行读取操作时返回此值。 |
| x-ms-ratelimit-remaining-subscription-writes |划归到订阅的剩余写入数。 执行写入操作时返回此值。 |
| x-ms-ratelimit-remaining-tenant-reads |划归到租户的剩余读取数 |
| x-ms-ratelimit-remaining-tenant-writes |划归到租户的剩余写入数 |
| x-ms-ratelimit-remaining-subscription-resource-requests |划归到订阅的剩余资源类型请求数。<br /><br />仅当服务重写了默认限制时，才返回此标头值。 Resource Manager 将累加此值而不是订阅读取/写入数。 |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |划归到订阅的剩余资源类型集合请求数。<br /><br />仅当服务重写了默认限制时，才返回此标头值。 此值提供剩余集合请求数（列出资源）。 |
| x-ms-ratelimit-remaining-tenant-resource-requests |划归到租户的剩余资源类型请求数。<br /><br />仅当服务重写了默认限制时，才为租户级别的请求添加此标头。 Resource Manager 将累加此值而不是租户读取/写入数。 |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |划归到租户的剩余资源类型集合请求数。<br /><br />仅当服务重写了默认限制时，才为租户级别的请求添加此标头。 |

资源提供程序还可以返回包含剩余请求相关信息的响应标头。 有关计算资源提供程序返回的响应标头的信息，请参阅[调用速率信息响应标头](../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers)。

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

有关完整 PowerShell 示例的信息，请参阅[检查订阅的资源管理器限制](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)。

如果想要查看剩余的调试请求数，可以在“PowerShell”cmdlet 中提供“-Debug”参数。

```powershell
Get-AzResourceGroup -Debug
```

这会返回许多值，包括以下响应值：

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

若要获取写入限制，请使用写入操作： 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

这会返回许多值，包括以下值：

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

在 **Azure CLI** 中，可以使用更详细的选项检索标头值。

```azurecli
az group list --verbose --debug
```

这会返回许多值，包括以下值：

```azurecli
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

若要获取写入限制，请使用写入操作： 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

这会返回许多值，包括以下值：

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>后续步骤

* 有关完整 PowerShell 示例的信息，请参阅[检查订阅的资源管理器限制](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)。
* 有关限制和配额的详细信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-subscription-service-limits.md)。
* 若要了解如何处理异步 REST 请求，请参阅[跟踪异步 Azure 操作](resource-manager-async-operations.md)。
