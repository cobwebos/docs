---
title: 管理 Azure Monitor 日志的使用情况和成本 |Microsoft Docs
description: 了解如何在 Azure Monitor 中更改定价计划和管理 Log Analytics 工作区的数据量和保留策略。
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 8c4169ccfb35b74b92ea4996cbc779bac35d6ccb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715865"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>使用 Azure Monitor 日志管理使用情况和成本

> [!NOTE]
> 本文介绍如何了解和控制 Azure Monitor 日志的成本。 相关文章[监视使用情况和估计成本](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs)介绍了如何在不同定价模型的多个 Azure 监视功能中查看使用情况和预估成本。

> [!NOTE]
> 本文中所示的所有价格和成本仅用于举例。 

Azure Monitor 日志旨在根据企业中的任何源或在 Azure 中部署的资源，缩放和支持每天收集、索引和存储大量数据。  尽管这可能是组织的主要驱动力，但成本效益最终是基本驱动力。 为此，必须了解 Log Analytics 工作区的成本不仅基于收集的数据量，它还依赖于所选的计划，以及你选择用于存储从连接的源生成的数据的时间。  

在本文中，我们将回顾如何主动监视引入数据量和存储增长，并定义限制来控制这些关联的成本。 

## <a name="pricing-model"></a>定价模型

Log Analytics 的默认定价是基于数据量引入的即**用即付**模型，还可以选择用于更长的数据保留。 数据量作为要存储的数据的大小来度量。 每个 Log Analytics 工作区都收费为单独的服务，并对 Azure 订阅计费。 数据引入量可能会很大，具体取决于以下因素： 

  - 已启用的管理解决方案的数量及其配置（例如 
  - 监视的 Vm 数
  - 从每个受监视的 VM 收集的数据的类型 
  
除了即用即付模型外，Log Analytics 还提供了**容量预留**层，使你可以将其保存为与即用即付价格相比最多25%。 产能预留价格使你可以购买起价 100 GB/天的保留。 将按现用现付费率对超出预订级别的任何使用量进行计费。 容量预留层具有31天承诺期。 在承诺期内，您可以更改为更高级的容量预留层（这将重新启动31天承诺期间），但您不能移回即用即付或更低的容量预留层，直到完. 
[详细了解](https://azure.microsoft.com/pricing/details/monitor/)Log Analytics 即用即付和产能预留定价。 

在所有定价层中，数据量是在准备存储数据时从数据的字符串表示形式计算得出的。 事件大小的计算中不包括[所有数据类型共有](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties)的几个属性，包括 `_ResourceId`、`_ItemId`、`_IsBillable` 和 `_BilledSize`。

另请注意，某些解决方案（例如[Azure 安全中心](https://azure.microsoft.com/pricing/details/security-center/)和[azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/)）有自己的定价模型。 

## <a name="estimating-the-costs-to-manage-your-environment"></a>估计管理环境的成本 

如果尚未使用 Azure Monitor 日志，可以使用[Azure Monitor 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=monitor)来估计使用 Log Analytics 的成本。 首先在搜索框中输入 "Azure Monitor"，然后单击生成的 Azure Monitor 磁贴。 向下滚动到 "Azure Monitor"，然后从 "类型" 下拉列表中选择 "Log Analytics"。  可在此处输入要从每个 VM 收集的 Vm 数和 GB 数据。 通常，从典型的 Azure VM 引入的数据月为1到 3 GB。 如果已在评估 Azure Monitor 的日志，则可以使用自己的环境中的数据统计信息。 请参阅下面有关如何确定[受监视 vm 的数目](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data)以及[工作区引入的数据量](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)。 

## <a name="understand-your-usage-and-estimate-costs"></a>了解你的使用情况和估计成本

如果你现在使用 Azure Monitor 日志，则可以很容易地了解基于最近使用模式的可能成本。 为此，请使用**Log Analytics 使用情况和估计成本**来查看和分析数据使用情况。 这会显示每个解决方案收集的数据量、正在保留的数据量，以及基于引入的数据量和超出包含数量的任何附加保留期的成本估算。

![使用情况和预估成本](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

若要更详细地探索数据，请单击“使用情况和预估成本”页上任一图表右上侧的图标。 现在可以使用此查询来探索有关使用情况的更多详细信息。  

![日志视图](media/manage-cost-storage/logs.png)

从“使用情况和估计成本”页面，可以查看当月的数据量。 这包括 Log Analytics 工作区中收到和保留的所有数据。  单击页面顶部的 "**使用情况详细信息**"，以查看 "使用情况" 仪表板，其中包含有关源、计算机和服务的数据量趋势的信息。 若要查看和设置每日上限或修改保留期，请单击“数据量管理”。
 
Log Analytics 费用将添加到 Azure 帐单。 可以在 Azure 门户的“计费”部分或在 [Azure 计费门户](https://account.windowsazure.com/Subscriptions)中查看 Azure 账单详细信息。  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>查看 Azure 帐单上的 Log Analytics 使用情况 

Azure 在[Azure 成本管理 + 计费](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json)中心提供了大量有用的功能。 例如，通过 "成本分析" 功能，可以查看 Azure 资源的花费。 按资源类型添加筛选器（到 Log Analytics 的 microsoft.operationalinsights/工作区），可以跟踪你的支出。

通过[从 Azure 门户下载你的使用](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)情况，可以更好地了解你的使用情况。 在下载的电子表格中，你可以查看每天每个 Azure 资源的使用情况（例如 Log Analytics 工作区）。 在此 Excel 电子表格中，可以通过 "计量类别" 列中的第一次筛选来找到 Log Analytics 工作区的使用情况，以显示 "Insights 和分析" （由某些旧的定价层使用）和 "Log Analytics"，然后在 "实例" 上添加筛选器ID "列是" 包含工作区 "。 使用情况显示在 "已消耗数量" 列中，每个条目的单位显示在 "度量单位" 列中。  更多详细信息可帮助你[了解 Microsoft Azure 帐单](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)。 

## <a name="changing-pricing-tier"></a>更改定价层

若要更改工作区的 Log Analytics 定价层，请 

1. 在 Azure 门户中，打开工作区中的 "**使用情况和估计成本**"，其中会显示此工作区可用的每个定价层的列表。

2. 查看每个定价层的预估成本。 这一估算基于最后31天的使用情况，因此，这种成本估算依赖于过去的31天，代表您的典型使用量。 在下面的示例中，你可以看到，基于过去31天的数据模式，此工作区将在即用即付层（#1）中的成本低于 100 GB/天容量预留层（#2）。  

    ![定价层](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. 根据最近31天的使用情况查看估计的成本后，如果决定更改定价层，请单击 "**选择**"。  

还可以通过 Azure 资源管理器使用 `sku` 参数（在 Azure 资源管理器模板中为`pricingTier`）来[设置定价层](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace)。 

## <a name="legacy-pricing-tiers"></a>旧版定价层

在2019企业协议2018年4月2日之前，具有 Log Analytics 工作区或 Application Insights 资源的订阅将继续具有使用旧版定价层的访问权限：**免费**版、**独立版（每 GB）** 和**每节点（OMS）** 。  免费定价层中的工作区将每日数据引入限制为 500 MB （Azure 安全中心收集的安全数据类型除外），并且数据保留期限制为7天。 免费定价层仅用于评估目的。 独立或按节点定价层中的工作区在用户可配置的保留期最多为2年。 

在2016年4月之前创建的工作区还可以访问原始**标准**和**高级**定价层，它们分别固定数据保持期30和365天。 无法在**标准**定价层或**高级**定价层中创建新工作区。如果工作区移出了这些层，则不能将其移回。 

有关定价层限制的更多详细信息，请参阅[此](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)文。

> [!NOTE]
> 若要使用通过购买用于 System Center 的 OMS E1 套件、OMS E2 套件或 OMS 附加产品所获得的权利，请选择 Log Analytics 的“按节点”定价层。

## <a name="change-the-data-retention-period"></a>更改数据保留期

以下步骤说明如何配置日志数据在工作区中的保留期限。

### <a name="default-retention"></a>默认保留期

若要设置工作区的默认保留期， 
 
1. 在 Azure 门户的工作区中，从左窗格中选择 "**使用情况和估计成本**"。
2. 在“使用情况和预估成本”页面顶部，单击“数据量管理”。
3. 在窗格中，移动滑块以增加或减少天数，然后单击“确定”。  如果位于“免费”层，则不能修改数据保留期，需要升级到付费层才能控制这一项设置。

    ![更改工作区数据保持期设置](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
还可以[通过 Azure 资源管理器](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace)使用 `retentionInDays` 参数设置保留。 此外，如果将数据保留期设置为30天，则可以使用 `immediatePurgeDataOn30Days` 参数触发立即清除旧数据，这对于符合性相关的方案可能很有用。 仅通过 Azure 资源管理器公开此功能。 

默认情况下，两种数据类型（`Usage` 和 `AzureActivity`）将保留90天，此90天保留期不收取任何费用。 这些数据类型还可从数据引入费用中免费接收。 

### <a name="retention-by-data-type"></a>按数据类型保留

还可以为单个数据类型指定不同的保留设置。 每个数据类型都是工作区的子资源。 例如，SecurityEvent 表可以在 Azure 中进行寻址[资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)如下所示：

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

请注意，数据类型（表）区分大小写。  若要获取特定数据类型（在此示例中为 SecurityEvent）中的每个数据类型保留设置的当前设置，请使用：

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

若要获取工作区中所有数据类型的当前每个数据类型保持设置，只需省略特定的数据类型，例如：

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

若要将特定数据类型（在本例中为 SecurityEvent）的保留期设置为730天，请执行

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

`Usage` 和 `AzureActivity` 数据类型不能通过自定义保留设置进行设置。 它们将采用默认工作区保持期或90天内的最大值。 

用于直接连接到 Azure 资源管理器以通过数据类型设置保留的极佳工具是 OSS 工具[ARMclient](https://github.com/projectkudu/ARMClient)。  详细了解 ARMclient [Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)和[Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)的文章。  下面是一个使用 ARMClient 的示例，将 SecurityEvent 数据设置为730天的保留期：

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!NOTE]
> 设置单个数据类型的保留期可用于降低数据保留成本。  对于从2019年10月（发布此功能时）开始收集的数据，减少某些数据类型的保留期可降低你的时间。  对于先前收集的数据，为单独类型设置较低的保留期不会影响你的保留费用。  

## <a name="manage-your-maximum-daily-data-volume"></a>管理每日最大数据量

可以配置工作区的每日上限并限制每日引入量，但请谨慎设置，因为目标是避免达到每日限制。  否则，会丢失该天剩余时间的数据，这可能会影响其功能依赖于工作区中提供的最新数据的其他 Azure 服务和解决方案。  因此，需要具有在支持 IT 服务的资源的运行状况受到影响时监视和接收警报的能力。  每日上限旨在用作一种方法，用于管理托管资源中意外增加的数据量并保持在限制范围内，或者当你希望限制工作区的计划外费用时。  

达到每日限制后，在当天的剩余时间，应计费数据类型的收集将会停止。 选定 Log Analytics 工作区的页面顶部会显示警告横幅，同时会将一个操作事件发送到“LogManagement”类别下的“操作”表。 在“每日限制设置时间”定义的重置时间过后，数据收集将会恢复。 我们建议基于此操作事件定义一个警报规则，并将其配置为在达到每日数据限制时发出通知。 

> [!NOTE]
> 除了在2017年6月19日之前安装了 Azure 安全中心的工作区之外，每日上限不会停止从 Azure 安全中心收集数据。 

> [!NOTE]
> 应用每日上限所固有的延迟可能意味着上限不会应用到指定的每日上限级别。 

### <a name="identify-what-daily-data-limit-to-define"></a>确定要定义的每日数据限制

查看 [Log Analytics 使用情况和预估成本](usage-estimated-costs.md)，了解数据引入趋势，以及要定义的每日数据量上限。 应该慎重考虑上限，因为在达到限制后，将无法监视资源。 

### <a name="set-the-daily-cap"></a>设置每日上限

以下步骤介绍了如何配置限制，以管理 Log Analytics 工作区将每天摄取的数据量。  

1. 在工作区的左窗格中，选择“使用情况和预估成本”。
2. 在所选工作区的“使用情况和预估成本”页面顶部，单击“数据量管理”。 
3. 每日上限默认为“关闭”– 单击“打开”将其启用，然后设置数据量限制（以 GB/天为单位）。

    ![Log Analytics 配置数据限制](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>达到每日上限时发出警报

尽管在达到数据限制阈值时，Azure 门户中会显示视觉提示，但此行为不一定符合需要立即关注的操作问题的处理方式。  若要接收警报通知，可以在 Azure Monitor 中创建一个新的警报规则。  若要了解详细信息，请参阅[如何创建、查看和管理警报](alerts-metric.md)。

若要开始操作，请参考下面提供的建议警报设置：

- 目标：选择 Log Analytics 资源
- 条件： 
   - 信号名称：自定义日志搜索
   - 搜索查询：Operation | where Detail has 'OverQuota'
   - 基于：结果数
   - 条件：大于
   - 阈值：0
   - 期限：5（分钟）
   - 频率：5（分钟）
- 警报规则名称：达到每日数据限制
- 严重性：警告（严重性 1）

定义警报并达到限制后，警报将会触发，并执行操作组中定义的响应。 该警报可通过电子邮件和短信通知团队，或者使用 Webhook、自动化 Runbook 或[与外部 ITSM 解决方案的集成](itsmc-overview.md#create-itsm-work-items-from-azure-alerts)来自动执行操作。 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>排查使用量超出预期的原因

使用量较高是由下面的一个或两个原因引起的：
- 与将数据发送到 Log Analytics 工作区相比，需要更多节点
- 超出了发送到 Log Analytics 工作区所需的数据

## <a name="understanding-nodes-sending-data"></a>了解发送数据的节点

若要了解上个月每天报告检测信号的计算机数，请使用

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

若要获取将按节点计费的计算机的列表（如果工作区位于 "基于每个节点的旧版" 定价层中），请查找发送**计费数据类型**的节点（某些数据类型是免费的）。 为此，请使用 `_IsBillable`[属性](log-standard-properties.md#_isbillable)，并使用完全限定的域名最左边的字段。 这会返回包含计费数据的计算机的列表：

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

可查看的可计费节点数估算如下： 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName)
```

> [!NOTE]
> 请谨慎使用这些 `union withsource = tt *` 查询，因为跨数据类型执行扫描的开销很大。 此查询替代了使用使用情况数据类型查询每台计算机的信息的旧方法。  

更准确地计算实际将计费的内容是获取每小时发送计费数据类型的计算机的计数。 （对于旧的按节点定价层中的工作区，Log Analytics 计算需要按小时计费的节点数。） 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>了解引入数据量

在“使用情况和预估成本”页上，“单个解决方案的数据引入”图表显示发送的总数据量以及每个解决方案发送的量。 这样就可以确定趋势，例如总数据使用量（或特定解决方案的使用量）是正在增长、保持平稳还是正在下降。 用于生成此指标的查询是：

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution| render barchart
```

请注意，子句“where IsBillable = true”从某些解决方案中筛选掉没有引入费用的数据类型。 

可以进一步钻取，以查看特定数据类型的数据趋势，例如，可以研究 IIS 日志的数据：

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>按计算机的数据量

若要查看每台计算机的可计费事件引入**大小**，请使用 `_BilledSize` 属性，该[属性](log-standard-properties.md#_billedsize)以字节为单位提供大小：

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

`_IsBillable`[属性](log-standard-properties.md#_isbillable)指定引入数据是否会产生费用。

若要查看每台计算机引入的可**计费**事件计数，请使用 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by eventCount nulls last
```

若要查看向特定计算机发送数据的计费数据类型计数，请使用：

```kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>按 Azure 资源、资源组或订阅的数据量

对于在 Azure 中托管的节点的数据，可以获取__每台计算机__的可计费事件引入的**大小**，使用 _ResourceId 属性，该[属性](log-standard-properties.md#_resourceid)提供资源的完整路径：

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

对于 Azure 中托管的节点的数据，可以获取__每个 azure 订阅__的可计费事件引入的**大小**，将 `_ResourceId` 属性分析为：

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

将 `subscriptionId` 更改为 `resourceGroup` 会按 Azure 资源组显示可计费引入数据量。 


> [!NOTE]
> 使用情况数据类型的某些字段虽然仍在架构中，但已弃用，其值将不再填充。 这些是**计算机**以及与引入相关的字段（**TotalBatches**、**BatchesWithinSla**、**BatchesOutsideSla**、**BatchesCapped** 和 **AverageProcessingTimeMs**）。

### <a name="querying-for-common-data-types"></a>查询常用数据类型

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
+ AzureDiagnostics 数据类型
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>有关如何减少数据量的提示

有关如何减少所收集日志的量的一些提示：

| 高数据量来源 | 如何减少数据量 |
| -------------------------- | ------------------------- |
| 安全性事件            | 选择[通用或最低安全性事件](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> 更改安全审核策略，只收集所需事件。 具体而言，请查看是否需要收集以下对象的事件： <br> - [审核筛选平台](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [审核注册表](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [审核文件系统](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [审核内核对象](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [审核句柄操作](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - 审核可移动存储 |
| 性能计数器       | 更改[性能计数器配置](data-sources-performance-counters.md)如下： <br> - 降低收集频率 <br> - 减少性能计数器数 |
| 事件日志                 | 更改[事件日志配置](data-sources-windows-events.md)如下： <br> - 减少收集的事件日志数 <br> - 仅收集必需的事件级别。 例如，不收集“信息”级别事件 |
| Syslog                     | 更改 [syslog 配置](data-sources-syslog.md)如下： <br> - 减少收集的设施数 <br> - 仅收集必需的事件级别。 例如，不收集“信息”和“调试”级别事件 |
| AzureDiagnostics           | 更改资源日志集合，以便： <br> - 减少向 Log Analytics 发送日志的资源数目 <br> - 仅收集必需的日志 |
| 不需解决方案的计算机中的解决方案数据 | 使用[解决方案目标](../insights/solution-targeting.md)，只从必需的计算机组收集数据。 |

### <a name="getting-security-and-automation-node-counts"></a>获取安全和自动化节点计数

如果你位于“按节点(OMS)”定价层，则根据所用节点和解决方案数收费，需付费的 Insights and Analytics 节点数将显示在“使用情况和预估成本”页的表中。  

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

## <a name="create-an-alert-when-data-collection-is-high"></a>在数据收集很高的情况时创建警报

本部分介绍如何在以下情况下创建警报：
- 数据量超过指定的量。
- 预测数据量会超过指定的量。

Azure 警报支持使用搜索查询的[日志警报](alerts-unified-log.md)。 

如果在过去 24 小时内收集的数据超过 100 GB，则以下查询就会有结果：

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type 
| where DataGB > 100
```

以下查询使用简单的公式来预测在一天中发送的数据何时会超过 100 GB： 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type 
| where EstimatedGB > 100
```

若要针对其他数据量发出警报，请在查询中将 100 更改为要发出警报的 GB 数。

执行[创建新的日志警报](alerts-metric.md)中介绍的步骤，当数据收集量超出预期时，系统就会发出通知。

为第一个查询创建警报时，如果 24 小时内的数据超出 100 GB，则请进行如下设置：  

- **定义警报条件**将 Log Analytics 工作区指定为资源目标。
- **警报条件**指定下列项：
   - **信号名称**选择“自定义日志搜索”。
   - 将“搜索查询”设置为 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`
   - **警报逻辑** **基于***结果数*，**条件***大于***阈值** *0*
   - 将“时间段”设置为 1440 分钟，“警报频率”设置为每 60 分钟，因为使用情况数据一小时才更新一次。
- **定义警报详细信息**指定以下项：
   - 将“名称”设置为“24 小时内的数据量大于 100 GB”
   - 将“严重性”设置为“警告”

指定现有的操作组或创建一个新[操作组](action-groups.md)，以便当日志警报匹配条件时，你会收到通知。

为第二个查询创建警报时，如果预测 24 小时内的数据会超出 100 GB，则请进行如下设置：

- **定义警报条件**将 Log Analytics 工作区指定为资源目标。
- **警报条件**指定下列项：
   - **信号名称**选择“自定义日志搜索”。
   - 将“搜索查询”设置为 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`
   - **警报逻辑** **基于***结果数*，**条件***大于***阈值** *0*
   - 将“时间段”设置为 180 分钟，“警报频率”设置为每 60 分钟，因为使用情况数据一小时才更新一次。
- **定义警报详细信息**指定以下项：
   - 将“名称”设置为“预期 24 小时内的数据量大于 100 GB”
   - 将“严重性”设置为“警告”

指定现有的操作组或创建一个新[操作组](action-groups.md)，以便当日志警报匹配条件时，你会收到通知。

收到警报后，请执行以下部分介绍的步骤，排查使用量超出预期的原因。

## <a name="data-transfer-charges-using-log-analytics"></a>使用 Log Analytics 的数据传输费用

向 Log Analytics 发送数据可能会导致数据带宽费用。 如[Azure 带宽定价页](https://azure.microsoft.com/pricing/details/bandwidth/)中所述，位于两个区域的 azure 服务之间的数据传输以正常费率作为出站数据传输收费。 入站数据传输是免费的。 但是，这种收费非常小（几%）与 Log Analytics 数据引入的成本比较。 因此，控制 Log Analytics 的成本需要专注于引入数据量，我们提供了有助于[了解这一点的指南。](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>排查 Log Analytics 不再收集数据的原因

如果采用的是旧版免费定价层并且某天已发送的数据超过 500 MB，则该天的剩余时间会停止数据收集。 达到每日限制是 Log Analytics 停止数据收集或者看起来缺少数据的常见原因。  在数据收集启动和停止时，Log Analytics 会创建一个类型为“操作”的事件。 请在搜索中运行以下查询来检查是否已达到每日限制并缺少数据： 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

数据收集停止时，OperationStatus 为**Warning**。 数据收集开始时，OperationStatus**成功**。 下表描述了数据收集停止的原因以及用于恢复数据收集的建议操作：  

|停止收集的原因| 解决方案| 
|-----------------------|---------|
|达到旧版免费定价层的每日限制 |等到下一天收集自动重启，或者更改为付费定价层。|
|达到了工作区的每日上限|等到收集自动重启，或者根据“管理每日最大数据量”中所述提高每日数据量限制。 每日上限重置时间显示在“数据量管理”页面上。 |
|Azure 订阅由于以下原因处于挂起状态：<br> 免费试用已结束<br> Azure 许可已过期<br> 已达到每月支出限制（例如，在 MSDN 或 Visual Studio 订阅上）|转换为付费订阅<br> 删除限制，或者等到限制重置|

若要在数据收集停止时得到通知，请使用*创建每日数据上限*警报中所述的步骤，以便在数据收集停止时收到通知。 使用[创建操作组](action-groups.md)中所述的步骤，为警报规则配置电子邮件、webhook 或 runbook 操作。 

## <a name="limits-summary"></a>限制摘要

还有其他 Log Analytics 限制，其中一些限制依赖于 Log Analytics 定价层。 [此处](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)介绍了这些内容。


## <a name="next-steps"></a>后续步骤

- 有关如何使用搜索语言的详细说明，请参阅[Azure Monitor 日志中的日志搜索](../log-query/log-query-overview.md)。 可以使用搜索查询，对使用情况数据执行其他分析。
- 执行[创建新的日志警报](alerts-metric.md)中介绍的步骤，当满足搜索条件时，系统就会通知你。
- 使用[解决方案目标](../insights/solution-targeting.md)，只从必需的计算机组收集数据。
- 若要配置有效的事件收集策略，请参阅 [Azure 安全中心筛选策略](../../security-center/security-center-enable-data-collection.md)。
- 更改[性能计数器配置](data-sources-performance-counters.md)。
- 若要修改事件收集设置，请参阅[事件日志配置](data-sources-windows-events.md)。
- 若要修改 syslog 收集设置，请参阅 [syslog 配置](data-sources-syslog.md)。
