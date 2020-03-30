---
title: 使用诊断日志监视 Azure 数据资源管理器引入操作
description: 了解如何设置诊断日志，使 Azure 数据资源管理器能够监视引入操作。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 3e10979e26cacdc0c2071a6030c945adad21a51c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277428"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>使用诊断日志监视 Azure 数据资源管理器引入操作（预览版）

Azure 数据资源管理器是一项快速、完全托管的数据分析服务，用于实时分析从应用程序、网站和 IoT 设备等资源流式传输的海量数据。 若要使用 Azure 数据资源管理器，请先创建群集，再在该群集中创建一个或多个数据库。 然后将数据引入（加载）到数据库中的某个表，以便对其运行查询。 [Azure Monitor 诊断日志](/azure/azure-monitor/platform/diagnostic-logs-overview)提供有关 Azure 资源操作的数据。 Azure 数据资源管理器使用诊断日志获取有关引入成功和失败的见解。 可将操作日志导出到 Azure 存储、事件中心或 Log Analytics 以监视引入状态。 可将 Azure 存储和 Azure 事件中心的日志路由到 Azure 数据资源管理器群集中的某个表，以进一步分析。

## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请创建[一个免费的 Azure 帐户](https://azure.microsoft.com/free/)。
* 创建[群集和数据库](create-cluster-database-portal.md)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 Azure[门户](https://portal.azure.com/)。

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>设置 Azure 数据资源管理器群集的诊断日志

诊断日志可用于配置以下日志数据的收集：
* 成功引入操作：这些日志包含有关成功完成引入操作的信息。
* 失败的引入操作：这些日志包含有关失败的引入操作的详细信息，包括错误详细信息。 

然后可根据规范将数据存档到存储帐户、流式传输到事件中心，或发送到 Log Analytics。

### <a name="enable-diagnostic-logs"></a>启用诊断日志

诊断日志默认已禁用。 若要启用诊断日志，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，选择要监视的 Azure 数据资源管理器群集资源。
1. 在“监视”下，选择“诊断设置”********。
  
    ![添加诊断日志](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. 选择“添加诊断设置”。****
1. 在“诊断设置”窗口中：****
 
    ![诊断设置配置](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. 选择诊断设置的**名称**。
    1. 选择一个或多个目标：存储帐户、事件中心或 Log Analytics。
    1. 选择要收集的日志：`SucceededIngestion` 或 `FailedIngestion`。
    1. 选择要收集的[指标](using-metrics.md#supported-azure-data-explorer-metrics)（可选）。  
    1. 选择“保存”以保存新的诊断日志设置和指标。****
    1. 在 Azure 门户中创建**新的支持请求**，以请求激活诊断日志。

在几分钟内即会完成新的设置。 日志随后会显示在配置的存档目标（存储帐户、事件中心或 Log Analytics）中。 

## <a name="diagnostic-logs-schema"></a>诊断日志架构

所有 [Azure Monitor 诊断日志共享一个通用的顶级架构](/azure/azure-monitor/platform/diagnostic-logs-schema)。 Azure 数据资源管理器对其自身的事件使用唯一属性。 所有日志均以 JSON 格式存储。

### <a name="ingestion-logs-schema"></a>引入日志架构

日志 JSON 字符串包含下表中列出的元素：

|“属性”               |描述
|---                |---
|time               |报告时间
|resourceId         |Azure 资源管理器资源 ID
|operationName      |操作的名称：'MICROSOFT。库斯特托/集群/INGEST/行动'
|operationVersion   |架构版本："1.0" 
|category           |操作类别。 `SucceededIngestion` 或 `FailedIngestion`。 [成功的操作](#successful-ingestion-operation-log)或[失败的操作](#failed-ingestion-operation-log)的属性不同。
|properties         |操作的详细信息。

#### <a name="successful-ingestion-operation-log"></a>成功引入操作日志

**例子：**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**成功操作诊断日志的属性**

|“属性”               |描述
|---                |---
|succeededOn        |引入完成时间
|operationId        |Azure 数据资源管理器引入操作 ID
|database           |目标数据库的名称
|表              |目标表的名称
|ingestionSourceId  |引入数据源的 ID
|ingestionSourcePath|引入数据源或 Blob URI 的路径
|rootActivityId     |活动 ID

#### <a name="failed-ingestion-operation-log"></a>失败引入操作日志

**例子：**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**失败操作诊断日志的属性**

|“属性”               |描述
|---                |---
|failedOn           |引入完成时间
|operationId        |Azure 数据资源管理器引入操作 ID
|database           |目标数据库的名称
|表              |目标表的名称
|ingestionSourceId  |引入数据源的 ID
|ingestionSourcePath|引入数据源或 Blob URI 的路径
|rootActivityId     |活动 ID
|详细信息            |失败和错误消息的详细说明
|errorCode          |错误代码 
|failureStatus      |`Permanent` 或 `Transient`。 重试暂时性故障可能会成功。
|originatesFromUpdatePolicy|如果故障源自更新策略，则为 True
|shouldRetry        |如果重试可以成功，则为 True

## <a name="next-steps"></a>后续步骤

* [教程：在 Azure 数据资源管理器中引入和查询监视数据](ingest-data-no-code.md)
* [使用指标来监视群集运行状况](using-metrics.md)

