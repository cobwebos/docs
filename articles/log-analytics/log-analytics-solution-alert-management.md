---
title: "Operations Management Suite (OMS) 中的警报管理解决方案 | Microsoft Docs"
description: "Log Analytics 中的警报管理解决方案可帮助你分析环境中的所有警报。  除了整合 OMS 内生成的警报之外，它还会将连接的 System Center Operations Manager (SCOM) 管理组中的警报导入到 Log Analytics。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: fe5d534e-0418-4e2f-9073-8025e13271a8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2caa3118785fab6919dd088e440bf3483a77bd69


---
# <a name="alert-management-solution-in-operations-management-suite-oms"></a>Operations Management Suite (OMS) 中的警报管理解决方案
![警报管理图标](media/log-analytics-solution-alert-management/icon.png) 警报管理解决方案可帮助你分析环境中的所有警报。  除了整合 OMS 内生成的警报之外，它还会将连接的 System Center Operations Manager (SCOM) 管理组中的警报导入到 Log Analytics。  在具有多个管理组的环境中，警报管理解决方案会提供来自所有管理组的警报的整合视图。

## <a name="prerequisites"></a>先决条件
* 若要导入 SCOM 中的警报，该解决方案要求使用[将 Operations Manager 连接到 Log Analytics](log-analytics-om-agents.md) 中所述的过程连接 OMS 工作区和 SCOM 管理组。  

## <a name="configuration"></a>配置
使用[添加解决方案](log-analytics-add-solutions.md)中所述的过程，将警报管理解决方案添加到 OMS 工作区。  无需进一步配置。

## <a name="management-packs"></a>管理包
如果 SCOM 管理组已连接到 OMS 工作区，则添加该解决方案时会在 SCOM 中安装以下管理包。  无需对这些管理包进行任何配置或维护。  

* Microsoft System Center Advisor 警报管理 (Microsoft.IntelligencePacks.AlertManagement)

有关如何更新解决方案管理包的详细信息，请参阅[将 Operations Manager 连接到 Log Analytics](log-analytics-om-agents.md)。

## <a name="data-collection"></a>数据收集
### <a name="agents"></a>代理
下表介绍了该解决方案支持的连接的源。

| 连接的源 | 支持 | 说明 |
|:--- |:--- |:--- |
| [Windows 代理](log-analytics-windows-agents.md) |否 |直接 Windows 代理不会生成 SCOM 警报。 |
| [Linux 代理](log-analytics-linux-agents.md) |否 |直接 Linux 代理不会生成 SCOM 警报。 |
| [SCOM 管理组](log-analytics-om-agents.md) |是 |SCOM 代理上生成的警报传送到管理组，然后转发给 Log Analytics。<br><br>不需要从 SCOM 代理直接连接到 Log Analytics。 警报数据从管理组转发到 OMS 存储库。 |
| [Azure 存储帐户](log-analytics-azure-storage.md) |否 |不会将 SCOM 警报存储在 Azure 存储帐户中。 |

### <a name="collection-frequency"></a>收集频率
OMS 内生成的警报会立即提供给解决方案。  警报数据每隔 3 分钟从 SCOM 管理组发送到 Log Analytics。  

## <a name="using-the-solution"></a>使用解决方案
在 OMS 工作区中添加警报管理解决方案时，“警报管理”磁贴将添加到 OMS 仪表板。  此磁贴显示在过去 24 小时内生成的当前活动警报的数目的计数与图形表示。  不能更改此时间范围。

![警报管理磁贴](media/log-analytics-solution-alert-management/tile.png)

单击“警报管理”磁贴打开“警报管理”仪表板。  仪表板包含下表中的列。  每个列按照指定范围和时间范围内符合该列条件的计数列出了前十个警报。  可通过以下方式运行提供整个列表的日志搜索：单击该列底部的“查看全部”或单击列标题。

| 列 | 说明 |
|:--- |:--- |
| 严重警报 |按警报名称分组并且严重级别为“严重”的所有警报。  单击某个警报名称，以运行会返回该警报所有记录的日志搜索。 |
| 警告警报 |按警报名称分组并且严重级别为“警告”的所有警报。  单击某个警报名称，以运行会返回该警报所有记录的日志搜索。 |
| 活动 SCOM 警报 |按生成警报的源分组并且状态为非“已关闭”的所有 SCOM 警报。 |
| 所有活动警报 |按警报名称分组并且具有任意严重级别的所有警报。 仅包括状态为非“已关闭”的 SCOM 警报。 |

向右滚动时，仪表板会列出几个常见查询，可以单击这些查询执行[日志搜索](log-analytics-log-searches.md)以获取警报数据。

![警报管理仪表板](media/log-analytics-solution-alert-management/dashboard.png)

## <a name="scope-and-time-range"></a>范围和时间范围
默认情况下，在警报管理解决方案中分析的警报的范围涵盖过去 7 天内生成的所有连接的管理组。  

![警报管理范围](media/log-analytics-solution-alert-management/scope.png)

* 若要更改分析中包含的管理组，请单击仪表板顶部的“范围”。  可以针对所有连接的管理组选择“全局”，也可以选择“按管理组”以选择单个管理组。
* 若要更改警报的时间范围，请选择仪表板顶部的“数据来源”。  可以选择过去 7 天、1 天或 6 小时内生成的警报。  也可以选择“自定义”，然后指定自定义的日期范围。

## <a name="log-analytics-records"></a>Log Analytics 记录
警报管理解决方案会分析类型为 **Alert** 的任何记录。  它还会导入 SCOM 中的警报，并为类型为 **Alert** 且 SourceSystem 为 **OpsManager** 的每个警报创建相应的记录。  这些记录的属性在下表中列出。  

| 属性 | 说明 |
|:--- |:--- |
| 类型 |*Alert* |
| SourceSystem |*OpsManager* |
| AlertContext |导致生成警报的数据项的详细信息（XML 格式）。 |
| AlertDescription |警报的详细说明。 |
| AlertId |警报的 GUID。 |
| AlertName |警报的名称。 |
| AlertPriority |警报的优先级。 |
| AlertSeverity |警报的严重级别。 |
| AlertState |警报最新的解决状态。 |
| LastModifiedBy |上次修改警报的用户的名称。 |
| ManagementGroupName |生成警报的管理组的名称。 |
| RepeatCount |针对同一个监视对象生成的相同警报的次数（自该警报解决之后）。 |
| ResolvedBy |解决警报的用户的名称。 空（如果警报尚未解决）。 |
| SourceDisplayName |已生成警报的监视对象的显示名称。 |
| SourceFullName |已生成警报的监视对象的完整名称。 |
| TicketId |警报的票证 ID（如果 SCOM 环境与分配警报票证的过程集成）。  空（如果未分配任何票证 ID）。 |
| TimeGenerated |警报的创建日期和时间。 |
| TimeLastModified |上次更改警报的日期和时间。 |
| TimeRaised |警报的生成日期和时间。 |
| TimeResolved |警报的解决日期和时间。 空（如果警报尚未解决）。 |

## <a name="sample-log-searches"></a>示例日志搜索
下表提供了此解决方案收集的警报记录的示例日志搜索。  

| 查询 | 说明 |
|:--- |:--- |
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR |过去 24 小时引发的严重警报 |
| Type=Alert AlertSeverity=warning TimeRaised>NOW-24HOUR |过去 24 小时引发的警告警报 |
| Type=Alert SourceSystem=OpsManager AlertState!=Closed TimeRaised>NOW-24HOUR &#124; measure count() as Count by SourceDisplayName |过去 24 小时引发的活动警报的源 |
| Type=Alert SourceSystem=OpsManager AlertSeverity=error TimeRaised>NOW-24HOUR AlertState!=Closed |过去 24 小时引发的严重警报（这些警报仍处于活动状态） |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-24HOUR AlertState=Closed |过去 24 小时引发的警报（这些警报现在已解决） |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY &#124; measure count() as Count by AlertSeverity |过去 1 天引发的警报（这些警报按其严重程度分组） |
| Type=Alert SourceSystem=OpsManager TimeRaised>NOW-1DAY &#124; sort RepeatCount desc |过去 1 天引发的警报（这些警报按其重复计数值排序） |

## <a name="next-steps"></a>后续步骤
* 有关从 Log Analytics 生成警报的详细信息，请参阅 [Log Analytics 中的警报](log-analytics-alerts.md)。




<!--HONumber=Nov16_HO3-->


