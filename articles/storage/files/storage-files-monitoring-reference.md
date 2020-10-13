---
title: Azure 文件监视数据引用 |Microsoft Docs
description: 用于监视来自 Azure 文件的数据的日志和指标参考。
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 11f89a0a42a42fd84d1dc0c8606128f5af9e751e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711228"
---
# <a name="azure-files-monitoring-data-reference"></a>Azure 文件监视数据引用

有关收集和分析 Azure 文件的监视数据的详细信息，请参阅 [监视 Azure 文件](storage-files-monitoring.md) 。

## <a name="metrics"></a>指标

下表列出了为 Azure 文件收集的平台指标。 

### <a name="capacity-metrics"></a>容量度量值

容量指标每隔一小时发送到 Azure Monitor。 值每日刷新。 时间粒度定义呈现指标值的时间间隔。 所有容量指标的受支持时间粒度为一小时 (PT1H)。

Azure 文件在 Azure Monitor 中提供了以下容量指标。

#### <a name="account-level"></a>帐户级别

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="azure-files"></a>Azure 文件

下表显示了 [Azure 文件指标](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices)。

| 指标 | 说明 |
| ------------------- | ----------------- |
| FileCapacity | 存储帐户使用的文件存储量。 <br/><br/> 单位：字节 <br/> 聚合类型：平均值 <br/> 值示例：1024 |
| FileCount   | 存储帐户中的文件数目。 <br/><br/> 单位：计数 <br/> 聚合类型：平均值 <br/> 值示例：1024 |
| FileShareCapacityQuota | Azure 文件服务可使用的存储量上限（以字节为单位）。 <br/><br/> 单元：字节 <br/> 聚合类型：平均值 <br/> 值示例：1024|
| FileShareCount | 存储帐户中的文件共享数目。 <br/><br/> 单位：计数 <br/> 聚合类型：平均值 <br/> 值示例：1024 |
| FileShareProvisionedIOPS | 文件共享上预配的 IOPS 数。 此指标仅适用于高级文件存储。 <br/><br/> 单位：字节 <br/> 聚合类型：平均值 |
| FileShareSnapshotCount | 存储帐户的 Azure 文件服务中的共享上存在的快照数。 <br/><br/> 单位：计数 <br/> 聚合类型：平均值 | 
|FileShareSnapshotSize|存储帐户的 Azure 文件服务中的快照使用的存储量。 <br/><br/> 单元：字节 <br/> 聚合类型：平均值|

### <a name="transaction-metrics"></a>事务指标

从 Azure 存储到 Azure Monitor 的每个存储帐户请求都会发出事务指标。 如果存储帐户中没有任何活动，则在此期间不会有关于事务指标的数据。 所有事务指标都在帐户和 Azure 文件服务级别提供。 时间粒度定义呈现指标值的时间间隔。 所有事务指标的受支持时间粒度为 PT1H 和 PT1M。

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>指标维度

对于 Azure Monitor 中的指标，Azure 文件支持以下维度。

> [!NOTE] 
> 文件共享维度不可用于标准文件共享， (仅) 高级文件共享。 使用标准文件共享时，提供的指标适用于存储帐户中的所有文件共享。 若要获取标准文件共享的每个共享指标，请为每个存储帐户创建一个文件共享。

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>资源日志（预览版）

> [!NOTE]
> Azure Monitor 中的 Azure 存储日志目前为公共预览版，可在所有公有云区域中用于预览测试。 若要注册预览版，请参阅[此页](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。  此预览版为常规用途 v1 和常规用途 v2 存储帐户中的 Blob（包括 Azure Data Lake Storage Gen2）、文件、队列、表和高级存储帐户启用日志。 不支持经典存储帐户。

下表列出了在 Azure Monitor 日志或 Azure 存储中收集 Azure 存储资源日志时这些资源日志的属性。 属性描述了操作、服务以及用来执行该操作的授权类型。

### <a name="fields-that-describe-the-operation"></a>描述操作的字段


[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>描述如何对操作进行身份验证的字段

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>描述服务的字段

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>请参阅

- 有关监视 Azure 存储的说明，请参阅 [监视 Azure 文件](storage-files-monitoring-reference.md) 。
- 有关监视 Azure 资源的详细信息，请参阅[使用 Azure Monitor 监视 Azure 资源](../../azure-monitor/insights/monitor-azure-resource.md)。