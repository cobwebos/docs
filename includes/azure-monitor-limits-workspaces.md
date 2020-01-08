---
title: include 文件
description: include 文件
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 49dc69eb73bd19509b5e14c5a4976cfa7208545a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392384"
---
**数据收集量和保留期** 

| 层 | 每日限制 | 数据保留 | 注释 |
|:---|:---|:---|:---|
| 当前每 GB 定价层<br>（2018年4月推出） | 无限制 | 30-730 天 | 超过31天的数据保留期可用于额外的费用。 了解有关 Azure Monitor 定价的详细信息。 |
| 旧版免费级别<br>（2016年4月推出） | 500 MB | 7 天 | 如果工作区达到每日 500 MB 的限制，数据引入将停止并在下一天开始时恢复。 一天的时间基于 UTC。 请注意，Azure 安全中心收集的数据不包括在此 500 MB 的每日限制内，并将继续收集此限制。  |
| 旧式独立每 GB 层<br>（2016年4月推出） | 无限制 | 30至730天 | 超过31天的数据保留期可用于额外的费用。 了解有关 Azure Monitor 定价的详细信息。 |
| 旧版每节点（OMS）<br>（2016年4月推出） | 无限制 | 30至730天 | 超过31天的数据保留期可用于额外的费用。 了解有关 Azure Monitor 定价的详细信息。 |
| 旧标准层 | 无限制 | 30 天  | 无法调整保留期 |
| 旧式高级层 | 无限制 | 365 天  | 无法调整保留期 |

**每个订阅的工作区数。**

| 定价层    | 工作区限制 | 注释
|:---|:---|:---|
| 免费层  | 10 | 不能增加此限制。 |
| 所有其他层 | 无限制 | 受资源组中的资源数和每个订阅的资源组数的限制。 |

**Azure 门户**

| 类别 | 限制 | 注释 |
|:---|:---|:---|
| 日志查询返回的最大记录数 | 10,000 | 使用查询中的查询范围、时间范围和筛选器减少结果。 |


**数据收集器 API**

| 类别 | 限制 | 注释 |
|:---|:---|:---|
| 单个 post 的最大大小 | 30 MB | 将更多的卷拆分为多个帖子。 |
| 字段值的最大大小  | 32 KB | 超过 32 KB 的字段会被截断。 |

**搜索 API**

| 类别 | 限制 | 注释 |
|:---|:---|:---|
| 单个查询中返回的最大记录数 | 500,000 | |
| 返回的数据的最大大小 | 64000000字节（约 61 MiB）| |
| 最长查询运行时间 | 10 分钟 | 有关详细信息，请参阅[超时](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts)。  |
| 最大请求速率 | 每个 AAD 用户或客户端 IP 地址每30秒200请求 | 有关详细信息，请参阅[速率限制](https://dev.loganalytics.io/documentation/Using-the-API/Limits)。 |

**常规工作区限制**

| 类别 | 限制 | 注释 |
|:---|:---|:---|
| 表中的最大列数         | 500 | |
| 列名称的最大字符数 | 500 | |
| 数据导出 | 当前不可用 | 使用 Azure 函数或逻辑应用来聚合和导出数据。 | 

**数据引入量速率**


Azure Monitor 是一种大规模数据服务，每月为成千上万的客户发送数 TB 的数据，并且此数据仍在不断增长。 使用[诊断设置](../articles/azure-monitor/platform/diagnostic-settings.md)从 Azure 资源发送的数据的默认引入量限制约为每个工作区**6 GB/分钟**。 这是一个近似值，因为每个数据类型的实际大小都可以根据日志长度和其压缩率变化。 此限制不适用于从代理或[数据收集器 API](../articles/azure-monitor/platform/data-collector-api.md)发送的数据。

如果以更高的速率将数据发送到单个工作区，则会删除某些数据，并且每隔6小时会将事件发送到工作区中的*操作*表，而阈值仍将会超出。 如果引入卷继续超出速率限制，或者你预计会在某个时间到达，则可以通过打开支持请求来请求增加工作区。
 
若要在工作区中收到此类事件的通知，请使用以下查询创建[日志警报规则](../articles/azure-monitor/platform/alerts-log.md)，并在结果大于数与零的情况下使用警报逻辑基数。

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>根据您使用 Log Analytics 的时间，您可能有权访问旧版定价层。 详细了解[Log Analytics 旧版定价层](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)。 