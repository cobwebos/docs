---
title: "收集和分析 OMS Log Analytics 中的 Windows 事件日志 | Microsoft Docs"
description: "Windows 事件日志 Log Analytics 使用的最常见的数据源之一。  本文介绍如何配置 Windows 事件日志集合，以及它们在 OMS 存储库中创建记录的详细信息。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: ee52f564-995b-450f-a6ba-0d7b1dac3f32
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2017
ms.author: bwren
ms.openlocfilehash: ddead0903c7c5f29bc996e305699ced596d0a4f5
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2017
---
# <a name="windows-event-log-data-sources-in-log-analytics"></a>Log Analytics 中的 Windows 事件日志数据源
由于许多应用程序都会写入 Windows 事件日志，因此 Windows 事件日志是使用 Windows 代理收集数据的最常见[数据源](log-analytics-data-sources.md)之一。  除了指定由需要监视的应用程序创建的任何自定义日志，还可以从标准日志（如系统和应用程序）中收集事件。

![Windows 事件](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>配置 Windows 事件日志
可以从 [Log Analytics 设置中的数据菜单](log-analytics-data-sources.md#configuring-data-sources)配置 Windows 事件日志。

Log Analytics 仅从在设置中指定的 Windows 事件日志收集事件。  可以通过键入日志名称并单击“+”添加事件日志。  对于每个日志，仅收集具有所选严重级别的事件。  检查要收集的特定日志的严重级别。  不能向筛选事件提供任何其他条件。

键入事件日志名称时，Log Analytics 会提供常见事件日志名称的建议。 如果要添加的日志未显示在列表中，仍可以通过键入日志全名添加。 可以使用事件查看器查找日志全名。 在事件查看器中，打开日志的“属性”页面，并从“全名”字段复制字符串。

![配置 Windows 事件](media/log-analytics-data-sources-windows-events/configure.png)

## <a name="data-collection"></a>数据收集
Log Analytics 在事件创建时从受监视的事件日志中收集与所选严重级别相匹配的每个事件。  代理会在将其收集到的每个事件日志的位置记录下来。  如果代理在一段时间内处于脱机状态，则 Log Analytics 从其上次脱机的位置收集事件，即使这些事件是在代理脱机期间创建的。  如果事件日志在代理脱机时，还有未收集的事件正在被覆盖，则可能无法收集这些事件。

>[!NOTE]
>对于其中包含带关键字 - 经典或审核成功以及关键字 0xa0000000000000 的事件 ID 为 18453 的源 MSSQLSERVER，Log Analytics 不会从中收集 SQL Server 创建的审核事件。
>

## <a name="windows-event-records-properties"></a>Windows 事件的记录属性
Windows 事件记录都有一个**事件**类型，并且具有下表中的属性：

| 属性 | 说明 |
|:--- |:--- |
| 计算机 |从中收集事件的计算机的名称。 |
| EventCategory |事件的类别。 |
| EventData |所有原始格式的事件数据。 |
| EventID |事件数。 |
| EventLevel |以数字形式指示的事件严重性。 |
| EventLevelName |以文本形式指示的事件严重性。 |
| EventLog |从中收集事件的事件日志名称。 |
| ParameterXml |XML 格式的事件参数值。 |
| ManagementGroupName |System Center Operations Manager 代理的管理组名称。  对于其他代理，该值为 AOI-<workspace ID> |
| RenderedDescription |具有参数值的事件描述 |
| 源 |事件源。 |
| SourceSystem |从中收集事件的代理类型。 <br> OpsManager – Windows 代理，直接连接或 Operations Manager 管理 <br> Linux - 所有 Linux 代理  <br> AzureStorage – Azure 诊断 |
| TimeGenerated |在 Windows 中创建事件的日期和时间。 |
| Username |记录事件的帐户的用户名。 |

## <a name="log-searches-with-windows-events"></a>使用 Windows 事件的日志搜索
下表提供了检索 Windows 事件记录的不同日志搜索的示例。

| 查询 | 说明 |
|:---|:---|
| 事件 |所有 Windows 事件。 |
| Event &#124; where EventLevelName == "error" |所有 Windows 事件与错误的严重性。 |
| Event &#124; summarize count() by Source |按源计数 Windows 事件。 |
| Event &#124; where EventLevelName == "error" &#124; summarize count() by Source |按源计数 Windows 错误事件。 |


## <a name="next-steps"></a>后续步骤
* 配置 Log Analytics 以收集其他[数据源](log-analytics-data-sources.md)进行分析。
* 了解[日志搜索](log-analytics-log-searches.md)以便分析从数据源和解决方案中收集的数据。  
* 使用[自定义字段](log-analytics-custom-fields.md)将事件记录解析为单独字段。
* 配置来自 Windows 代理的[性能计数器集合](log-analytics-data-sources-performance-counters.md)。
