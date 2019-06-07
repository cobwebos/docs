---
title: 为 Azure Monitor 日志管理使用情况和成本 |Microsoft Docs
description: 了解如何更改定价计划和管理 Log Analytics 工作区 Azure Monitor 中的数据量和保留策略。
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: 8eeb29b2d1fe17ae5581dab81c34d5c2c635a6c2
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496338"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>通过 Azure Monitor 日志管理使用情况和成本

> [!NOTE]
> 本文介绍了如何通过为你的 Log Analytics 工作区设置数据保留期来在 Azure Monitor 中控制成本。  请参阅以下文章来了解相关信息。
> - [监视使用情况及预估成本](usage-estimated-costs.md)介绍了如何针对不同的定价模型查看多个 Azure 监视功能的使用情况及预估成本。 它还介绍了如何更改定价模型。

Azure Monitor 日志用于调整和支持来自任何源的巨量数据的每日收集、索引和存储，这些源部署在企业或 Azure 中。  尽管这可能是组织的主要驱动力，但成本效益最终是基本驱动力。 为此，必须了解 Log Analytics 工作区的成本不仅仅是基于收集的数据量，而且也取决于所选的计划，以及连接源生成的数据的存储时间长短。  

本文介绍如何主动监视数据量和存储增长，以及定义限制来控制这些关联的成本。 

根据以下因素，数据成本有时会很大： 

- 生成和引入到工作区的数据量 
    - 已启用的管理解决方案的数量
    - 受监视的系统数量
    - 从每个受监视资源收集的数据类型 
- 决定保留数据的时长 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>了解工作区的使用情况和估计成本

使用 Azure Monitor 日志可以轻松了解基于最近使用模式的可能成本。 若要执行此操作，请使用“Log Analytics 使用情况和预估成本”查看和分析数据使用情况  。 显示每个解决方案收集的数据量、保留的数据量，并根据引入的数据量和已包含量之外的其他保留量来估算成本。

![使用情况和预估成本](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

若要更详细地探索数据，请单击“使用情况和预估成本”页上任一图表右上侧的图标。  现在可以使用此查询来探索有关使用情况的更多详细信息。  

![日志视图](media/manage-cost-storage/logs.png)

从“使用情况和估计成本”页面，可以查看当月的数据量  。 这包括 Log Analytics 工作区中收到和保留的所有数据。  单击页面顶部的“使用情况详细信息”查看使用情况仪表板，其中按源、计算机和产品/服务显示了有关数据量趋势的信息。  若要查看和设置每日上限或修改保留期，请单击“数据量管理”。 
 
Log Analytics 费用将添加到 Azure 帐单。 可以在 Azure 门户的“计费”部分或在 [Azure 计费门户](https://account.windowsazure.com/Subscriptions)中查看 Azure 账单详细信息。  

## <a name="daily-cap"></a>每日上限

可以配置工作区的每日上限并限制每日引入量，但请谨慎设置，因为目标是避免达到每日限制。  否则，会丢失该天剩余时间的数据，这可能会影响其功能依赖于工作区中提供的最新数据的其他 Azure 服务和解决方案。  因此，需要具有在支持 IT 服务的资源的运行状况受到影响时监视和接收警报的能力。  每日上限旨在用作一种调控受管理资源数据量意外增长并使其保留在限制范围内，或者限制工作区产生计划外费用的方式。  

达到每日限制后，在当天的剩余时间，应计费数据类型的收集将会停止。 选定 Log Analytics 工作区的页面顶部会显示警告横幅，同时会将一个操作事件发送到“LogManagement”类别下的“操作”表。   在“每日限制设置时间”定义的重置时间过后，数据收集将会恢复。  我们建议基于此操作事件定义一个警报规则，并将其配置为在达到每日数据限制时发出通知。 

> [!NOTE]
> 每日上限不会停止从 Azure 安全中心的数据的集合。

### <a name="identify-what-daily-data-limit-to-define"></a>确定要定义的每日数据限制

查看 [Log Analytics 使用情况和预估成本](usage-estimated-costs.md)，了解数据引入趋势，以及要定义的每日数据量上限。 应该慎重考虑上限，因为在达到限制后，将无法监视资源。 

### <a name="manage-the-maximum-daily-data-volume"></a>管理每日最大数据量

以下步骤说明如何配置一个限制来管理 Log Analytics 工作区每日引入的数据量。  

1. 在工作区的左窗格中，选择“使用情况和预估成本”。 
2. 在所选工作区的“使用情况和预估成本”页面顶部，单击“数据量管理”。   
3. 每日上限默认为“关闭”– 单击“打开”将其启用，然后设置数据量限制（以 GB/天为单位）。  

    ![Log Analytics 配置数据限制](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>达到每日上限时发出警报

尽管在达到数据限制阈值时，Azure 门户中会显示视觉提示，但此行为不一定符合需要立即关注的操作问题的处理方式。  若要接收警报通知，可以在 Azure Monitor 中创建一个新的警报规则。  有关详细信息，请参阅[如何创建、查看和管理警报](alerts-metric.md)。

若要开始操作，请参考下面提供的建议警报设置：

- 目标：选择 Log Analytics 资源
- 条件： 
   - 信号名称：自定义日志搜索
   - 搜索查询：Operation | where Detail has 'OverQuota'
   - 依据：结果数
   - 条件：大于
   - 阈值：0
   - 时间段：5（分钟）
   - 频率：5（分钟）
- 警报规则名称：达到每日数据限制
- 严重性：警告（严重性 1）

定义警报并达到限制后，警报将会触发，并执行操作组中定义的响应。 该警报可通过电子邮件和短信通知团队，或者使用 Webhook、自动化 Runbook 或[与外部 ITSM 解决方案的集成](itsmc-overview.md#create-itsm-work-items-from-azure-alerts)来自动执行操作。 

## <a name="change-the-data-retention-period"></a>更改数据保留期

以下步骤说明如何配置日志数据在工作区中的保留期限。
 
1. 在工作区的左窗格中，选择“使用情况和预估成本”。 
2. 在“使用情况和预估成本”页面顶部，单击“数据量管理”。  
3. 在窗格中，移动滑块以增加或减少天数，然后单击“确定”。   如果位于“免费”层，则不能修改数据保留期，需要升级到付费层才能控制这一项设置。 

    ![更改工作区数据保留设置](media/manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="legacy-pricing-tiers"></a>旧版定价层

2018 年 4 月 2 日之前, 在其中具有 Log Analytics 工作区或 Application Insights 资源，或链接到在 2019 年 2 月 1 日之前, 启动了企业协议订阅将继续有权访问旧的定价层：免费版、 独立 （按 gb 计） 和每个节点 (OMS)。  在免费定价层中的工作区将有每日数据限制为 500 MB （除了 Azure 安全中心收集的安全数据类型） 的数据引入和数据保留期被限制为 7 天。 在免费定价层仅用于评估目的。 独立或每个节点定价层中的工作区包含到 2 年最多数据保留期的访问权限。 

> [!NOTE]
> 若要使用通过购买用于 System Center 的 OMS E1 套件、OMS E2 套件或 OMS 附加产品所获得的权利，请选择 Log Analytics 的“按节点”定价层  。

## <a name="changing-pricing-tier"></a>更改定价层

如果 Log Analytics 工作区可以访问旧版定价层，则可以在旧版定价层之间进行更改：

1. 在 Azure 门户 中的 Log Analytics 订阅窗格中选择一个工作区。

2. 在工作区窗格的“常规”下，选择“定价层”   。  

3. 在“定价层”下选择一个定价层，并单击“选择”   。  
    ![选择定价计划](media/manage-cost-storage/workspace-pricing-tier-info.png)

如果要将工作区移到当前定价层，则需要[在 Azure Monitor 中更改订阅的监视定价模型](usage-estimated-costs.md#moving-to-the-new-pricing-model)，这将更改该订阅中所有工作区的定价层。

> [!NOTE]
> 在[使用 Azure 资源管理器模板](template-workspace-configuration.md#create-a-log-analytics-workspace)创建工作区时，你可以详细了解如何设置定价层，并确保无论订阅采用旧的还是新的定价模型，你的 Azure 资源管理器模板部署都会成功。 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>排查 Log Analytics 不再收集数据的原因

如果采用的是旧版免费定价层并且某天已发送的数据超过 500 MB，则该天的剩余时间会停止数据收集。 达到每日限制是 Log Analytics 停止数据收集或者看起来缺少数据的常见原因。  在数据收集启动和停止时，Log Analytics 会创建一个类型为“操作”的事件。 请在搜索中运行以下查询来检查是否已达到每日限制并缺少数据： 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

当数据收集停止时，OperationStatus 为 **Warning**。 当数据收集启动时，OperationStatus 为 **Succeeded**。 下表描述了数据收集停止的原因以及用于恢复数据收集的建议操作：  

|停止收集的原因| 解决方案| 
|-----------------------|---------|
|达到旧版免费定价层的每日限制 |等到下一天收集自动重启，或者更改为付费定价层。|
|达到了工作区的每日上限|等到收集自动重启，或者根据“管理每日最大数据量”中所述提高每日数据量限制。 每日上限重置时间显示在“数据量管理”页面上  。 |
|Azure 订阅由于以下原因处于挂起状态：<br> 免费试用已结束<br> Azure 许可已过期<br> 已达到每月支出限制（例如，在 MSDN 或 Visual Studio 订阅上）|转换为付费订阅<br> 删除限制，或者等到限制重置|

若想在数据收集停止时收到通知，请使用*创建每日数据上限*警报中所述的步骤，以便在数据收集停止时收到通知。 使用[创建操作组](action-groups.md)中介绍的步骤，为警报规则配置电子邮件、Webhook 或 Runbook 操作。 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>排查使用量超出预期的原因

使用量较高是由下面的一个或两个原因引起的：
- 将数据发送到 Log Analytics 工作区的节点数超出预期
- 发送到 Log Analytics 工作区的数据量超出预期

## <a name="understanding-nodes-sending-data"></a>了解发送数据的节点

若要了解计算机报告在上个月中每一天的检测信号的数量，请使用

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

若要获取的早期每个节点定价层的工作区是否将按节点计费的计算机的列表，查找节点发送**计费数据类型**（某些数据类型是免费的）。 若要执行此操作，请使用`_IsBillable`[属性](log-standard-properties.md#_isbillable)和使用的完全限定的域名最左边的字段。 此命令返回计费数据的计算机的列表：

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

可计费所示的节点数可以通过如下方式计算： 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| billableNodes=dcount(computerName)
```

> [!NOTE]
> 请谨慎使用这些 `union withsource = tt *` 查询，因为跨数据类型执行扫描的开销很大。 此查询将使用 Usage 数据类型替换旧的查询单个计算机信息的方式。  

更准确地计算实际计费什么是获取每小时发送计费的数据类型的计算机数。 （旧的每个节点定价层中的工作区，Log Analytics 计算需要按小时计费的节点数。） 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>了解引入的数据量

在“使用情况和预估成本”页上，  “单个解决方案的数据引入”图表  显示发送的总数据量以及每个解决方案发送的量。 这样就可以确定趋势，例如总数据使用量（或特定解决方案的使用量）是正在增长、保持平稳还是正在下降。 用于生成此指标的查询是：

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

请注意，子句“where IsBillable = true”从某些解决方案中筛选掉没有引入费用的数据类型。 

可以进一步钻取，以查看特定数据类型的数据趋势，例如，可以研究 IIS 日志的数据：

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>按计算机的数据量

若要查看**大小**的可计费事件引入每台计算机，使用`_BilledSize`[属性](log-standard-properties.md#_billedsize)，它提供以字节为单位的大小：

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

`_IsBillable` [属性](log-standard-properties.md#_isbillable)指定引入的数据是否会产生费用。

若要查看的计数**计费**事件引入每台计算机，使用 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by count_ nulls last
```

若要查看向特定计算机发送数据的计费数据类型计数，请使用：

```kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>按 Azure 资源、资源组或订阅计算的数据量

对于在 Azure 中托管的节点中的数据可以获取**大小**的可计费事件引入__每台计算机__，使用 _ResourceId[属性](log-standard-properties.md#_resourceid)，它提供的完整路径资源：

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

对于托管在 Azure 中的节点中的数据，可以__按 Azure 订阅__获取引入的可计费事件的**大小**，并可将 `_ResourceId` 属性解析为：

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

将 `subscriptionId` 更改为 `resourceGroup` 后，就会显示可计费的已引入数据量（按 Azure 资源组计算）。 


> [!NOTE]
> 使用情况数据类型的某些字段虽然仍在架构中，但已弃用，其值将不再填充。 这些是**计算机**以及与引入相关的字段（**TotalBatches**、**BatchesWithinSla**、**BatchesOutsideSla**、**BatchesCapped** 和 **AverageProcessingTimeMs**）。

### <a name="querying-for-common-data-types"></a>查询常见的数据类型

若要更深入地了解特定数据类型的数据源，请使用下面这些有用的示例查询：

+ “安全”解决方案 
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ “日志管理”解决方案 
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ “性能”数据类型 
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ “事件”数据类型 
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ “Syslog”数据类型 
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ AzureDiagnostics  数据类型
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>有关如何减少数据量的提示

有关如何减少所收集日志的量的一些提示：

| 高数据量来源 | 如何减少数据量 |
| -------------------------- | ------------------------- |
| 安全性事件            | 选择[通用或最低安全性事件](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> 更改安全审核策略，只收集所需事件。 具体而言，请查看是否需要收集以下对象的事件： <br> - [审核筛选平台](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [审核注册表](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [审核文件系统](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [审核内核对象](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [审核句柄操作](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - 审核可移动存储 |
| 性能计数器       | 更改[性能计数器配置](data-sources-performance-counters.md)如下： <br> - 降低收集频率 <br> - 减少性能计数器数 |
| 事件日志                 | 更改[事件日志配置](data-sources-windows-events.md)如下： <br> - 减少收集的事件日志数 <br> - 仅收集必需的事件级别。 例如，不收集“信息”级别事件  |
| Syslog                     | 更改 [syslog 配置](data-sources-syslog.md)如下： <br> - 减少收集的设施数 <br> - 仅收集必需的事件级别。 例如，不收集“信息”和“调试”级别事件   |
| AzureDiagnostics           | 更改资源日志集合，以便： <br> - 减少向 Log Analytics 发送日志的资源数目 <br> - 仅收集必需的日志 |
| 不需解决方案的计算机中的解决方案数据 | 使用[解决方案目标](../insights/solution-targeting.md)，只从必需的计算机组收集数据。 |

### <a name="getting-security-and-automation-node-counts"></a>获取安全性和自动化节点计数

如果你位于“按节点(OMS)”定价层，则根据所用节点和解决方案数收费，需付费的 Insights and Analytics 节点数将显示在“使用情况和预估成本”页的表中  。  

若要查看不同安全节点的数目，可以使用以下查询：

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

若要查看不同自动化节点的数目，请使用以下查询：

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="create-an-alert-when-data-collection-is-high"></a>当数据收集量过高时创建警报

本部分介绍如何在以下情况下创建警报：
- 数据量超过指定的量。
- 预测数据量会超过指定的量。

Azure 警报支持使用搜索查询的[日志警报](alerts-unified-log.md)。 

如果在过去 24 小时内收集的数据超过 100 GB，则以下查询就会有结果：

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type 
| where DataGB > 100
```

以下查询使用简单的公式来预测在一天中发送的数据何时会超过 100 GB： 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type 
| where EstimatedGB > 100
```

若要针对其他数据量发出警报，请在查询中将 100 更改为要发出警报的 GB 数。

执行[创建新的日志警报](alerts-metric.md)中介绍的步骤，当数据收集量超出预期时，系统就会发出通知。

为第一个查询创建警报时，如果 24 小时内的数据超出 100 GB，则请进行如下设置：  

- **定义警报条件**将 Log Analytics 工作区指定为资源目标。
- **警报条件**指定下列项：
   - **信号名称**选择“自定义日志搜索”  。
   - 将“搜索查询”  设置为 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - **警报逻辑** **基于** *结果数*，**条件** *大于* **阈值** *0*
   - 将“时间段”设置为 1440 分钟，“警报频率”设置为每 60 分钟，因为使用情况数据一小时才更新一次。    
- **定义警报详细信息**指定以下项：
   - 将“名称”  设置为“24 小时内的数据量大于 100 GB” 
   - 将“严重性”设置为“警告”  

指定现有的操作组或创建一个新[操作组](action-groups.md)，以便当日志警报匹配条件时，你会收到通知。

为第二个查询创建警报时，如果预测 24 小时内的数据会超出 100 GB，则请进行如下设置：

- **定义警报条件**将 Log Analytics 工作区指定为资源目标。
- **警报条件**指定下列项：
   - **信号名称**选择“自定义日志搜索”  。
   - 将“搜索查询”  设置为 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - **警报逻辑** **基于** *结果数*，**条件** *大于* **阈值** *0*
   - 将“时间段”设置为 180 分钟，“警报频率”设置为每 60 分钟，因为使用情况数据一小时才更新一次。    
- **定义警报详细信息**指定以下项：
   - 将“名称”  设置为“预期 24 小时内的数据量大于 100 GB” 
   - 将“严重性”设置为“警告”  

指定现有的操作组或创建一个新[操作组](action-groups.md)，以便当日志警报匹配条件时，你会收到通知。

收到警报后，请执行以下部分介绍的步骤，排查使用量超出预期的原因。

## <a name="next-steps"></a>后续步骤

- 请参阅[Azure Monitor 日志中的日志搜索](../log-query/log-query-overview.md)若要了解如何使用搜索语言。 可以使用搜索查询，对使用情况数据执行其他分析。
- 执行[创建新的日志警报](alerts-metric.md)中介绍的步骤，当满足搜索条件时，系统就会通知你。
- 使用[解决方案目标](../insights/solution-targeting.md)，只从必需的计算机组收集数据。
- 若要配置有效的事件收集策略，请参阅 [Azure 安全中心筛选策略](../../security-center/security-center-enable-data-collection.md)。
- 更改[性能计数器配置](data-sources-performance-counters.md)。
- 若要修改事件收集设置，请参阅[事件日志配置](data-sources-windows-events.md)。
- 若要修改 syslog 收集设置，请参阅 [syslog 配置](data-sources-syslog.md)。