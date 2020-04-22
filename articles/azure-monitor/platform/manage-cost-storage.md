---
title: 管理 Azure Monitor 日志的使用情况和成本 | Microsoft Docs
description: 了解如何在 Azure Monitor 中更改定价计划和管理 Log Analytics 工作区的数据量与保留策略。
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
ms.date: 04/20/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 9a7d0530c4f03138fad3e4aaa473d54e1cfd5b0a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686559"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>通过 Azure Monitor 日志管理使用情况和成本

> [!NOTE]
> 本文介绍如何了解和控制 Azure Monitor 日志的成本。 相关文章[监视使用情况和估算成本](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs)介绍了如何针对不同的定价模型查看多个 Azure 监视功能的使用情况及估算成本。 本文中显示的所有价格和成本仅用于举例。 

Azure Monitor 日志用于调整和支持来自任何源的巨量数据的每日收集、索引和存储，这些源部署在企业或 Azure 中。  尽管这可能是组织的主要驱动力，但成本效益最终是基本驱动力。 为此，必须了解 Log Analytics 工作区的成本不仅仅是基于收集的数据量，而且也取决于所选的计划，以及连接源生成的数据的存储时间长短。  

本文介绍如何主动监视引入的数据量和存储增长，以及定义限制来控制这些关联的成本。 

## <a name="pricing-model"></a>定价模型

Log Analytics 的默认定价采用**即用即付**模型，该模型基于数据引入量，有时还基于长期数据保留策略。 数据量是指将要存储的数据的大小。 每个 Log Analytics 工作区作为独立服务计费，并在 Azure 订阅的帐单中产生相应费用。 数据引入量可能相当大，具体取决于以下因素： 

  - 已启用的管理解决方案的数量及其配置（例如 
  - 受监视的 VM 数量
  - 从每个受监视 VM 收集的数据类型 
  
除了即用即付模型以外，Log Analytics 还提供了**产能预留**层，与即用即付价格相比，该模型最多可将成本节省 25%。 容量预留定价模型允许每天购买 100 GB 的初始预留容量。 超过预留级别的任何用量将按即用即付费率计费。 产能预留层具有 31 天的套餐周期。 在套餐周期内，你可以更改为更高级别的产能预留层（这将重启 31 天的套餐周期），但在套餐周期结束之前，你不能返回到即用即付或更低级别的产能预留层。 容量预留层的计费每天完成。 [详细了解](https://azure.microsoft.com/pricing/details/monitor/) Log Analytics 即用即付和产能预留定价。 

在所有定价层中，数据量都是在准备存储时从数据的字符串表示形式计算的。 计算事件大小（`_ResourceId`包括`_ItemId`、`_IsBillable`和`_BilledSize`） 不包括[所有数据类型共有的几个属性](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties)。

另请注意，某些解决方案（例如 [Azure 安全中心](https://azure.microsoft.com/pricing/details/security-center/)和 [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/)）具有其自己的定价模型。 

## <a name="estimating-the-costs-to-manage-your-environment"></a>估算环境的管理成本 

如果尚未使用 Azure 监视器日志，则可以使用[Azure 监视器定价计算器](https://azure.microsoft.com/pricing/calculator/?service=monitor)来估计使用日志分析的成本。 首先在搜索框中输入“Azure Monitor”，然后单击生成的“Azure Monitor”磁贴。 向下滚动页面到 Azure 监视器，然后从"类型"下拉下下拉列表选择"日志分析"。  在这里，您可以输入要从每个 VM 收集的 VM 数量和 GB 数据。 通常从典型的 Azure VM 中摄录 1 到 3 GB 的数据月。 如果已经评估了 Azure Monitor 日志，则可以使用自己环境中的数据统计信息。 请参阅下文来了解如何确定[受监视 VM 数](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data)和[工作区引入的数据量](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)。 

## <a name="understand-your-usage-and-estimate-costs"></a>查看自己的使用情况和估算成本

如果现在正在使用 Azure Monitor 日志，就很容易了解根据最近的使用模式可能会产生什么样的成本。 为此，请使用**日志分析使用情况和估计成本**来查看和分析数据使用情况。 此项显示每个解决方案收集的数据量、保留的数据量，并根据引入的数据量和已包含量之外的其他保留量来估算成本。

![使用情况和预估成本](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

若要更详细地探索数据，请单击“使用情况和预估成本”页上任一图表右上侧的图标。**** 现在可以使用此查询来探索有关使用情况的更多详细信息。  

![日志视图](media/manage-cost-storage/logs.png)

从“使用情况和估计成本”页面，可以查看当月的数据量****。 这包括 Log Analytics 工作区中收到和保留的所有数据。  单击页面顶部的**使用情况详细信息**，查看使用情况仪表板，并查看有关数据量趋势的信息（按源、计算机和产品/服务）。 若要查看和设置每日上限或修改保留期，请单击“数据量管理”。****
 
Log Analytics 费用将添加到 Azure 帐单。 可以在 Azure 门户的“计费”部分或在 [Azure 计费门户](https://account.windowsazure.com/Subscriptions)中查看 Azure 账单详细信息。  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>在 Azure 帐单上查看 Log Analytics 使用情况 

Azure 在 [Azure 成本管理 + 计费](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json)中心提供大量有用的功能。 例如，使用“成本分析”功能可以查看 Azure 资源的开支。 按资源类型添加筛选器（添加到 Log Analytics 的 microsoft.operationalinsights/workspace）可以跟踪开支。

[从 Azure 门户下载使用情况](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)可以更好地了解使用情况。 在下载的电子表格中，可以看到每天每个 Azure 资源（例如 Log Analytics 工作区）的使用情况。 在此 Excel 电子表格中，可通过以下方式查找 Log Analytics 工作区的使用情况：先按“计量类别”列进行筛选，以显示“Insights and Analytics”（由某些旧的定价层使用）和“Log Analytics”，然后按“实例 ID”列添加一个“包含工作区”的筛选器。 使用情况显示在“使用的数量”列中，每个条目的单位显示在“度量单位”列中。  有更多详细信息可帮助你[了解自己的 Microsoft Azure 帐单](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)。 

## <a name="changing-pricing-tier"></a>更改定价层

若要更改工作区的 Log Analytics 定价层，请执行以下操作： 

1. 在 Azure 门户中，从你的工作区中打开“使用情况和预估成本”****，你会在其中看到此工作区可用的每个定价层的列表。

2. 查看每个定价层的预估成本。 此预估基于过去 31 天的使用情况，因此，此成本估计依赖于过去的 31 天，能代表你的典型使用情况。 在下面的示例中，你可以看到，基于过去 31 天的数据模式，与 100 GB/天产能预留层 (#2) 相比，此工作区采用即用即付层 (#1) 时的成本更低。  

    ![定价层](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. 查看基于过去 31 天使用情况的估计成本后，如果决定更改定价层，请单击“选择”****。  

还可以使用 `sku` 参数（在 Azure 资源管理器模板中为 `pricingTier`）[通过 Azure 资源管理器设置定价层](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace)。 

## <a name="legacy-pricing-tiers"></a>旧版定价层

在 2018 年 4 月 2 日之前在其中具有日志分析工作区或应用程序见解资源的订阅，或链接到 2019 年 2 月 1 日之前启动的企业协议，将继续有权访问使用旧版定价层：**免费**、**独立（每 GB）** 和**每个节点 （OMS）。**  对于“免费”定价层中的工作区，其每日数据引入量限制为 500 MB（由 Azure 安全中心收集的安全数据类型除外），并且其数据保留期限制为 7 天。 “免费”定价层仅用于评估目的。 独立或每个节点定价层中的工作区具有 30 到 730 天的用户可配置保留期。

每个节点定价层收费每个受监视的 VM（节点）在一小时的粒度。 对于每个受监视的节点，工作区每天分配 500 MB 的数据，这些数据不计费。 此分配在工作区级别聚合。 聚合每日数据分配上方引入的数据按 GB 计费为数据超额。 请注意，如果您的帐单上，如果工作区位于"每个节点"定价层中，则服务将为日志分析使用情况的**见解和分析**。 

> [!TIP]
> 如果您的工作区有权访问 **"每个节点"** 定价层，但您想知道在即用即付层中是否成本更低，则可以使用[下面的查询](#evaluating-the-legacy-per-node-pricing-tier)轻松获取建议。 

在 2016 年 4 月之前创建的工作区还可以访问**标准**和**高级**定价层，这些层的数据保留期是固定的，分别为 30 天和 365 天。 无法在**标准**或**高级**定价层中创建新的工作区，并且如果将工作区移出这些层，则无法将其移回。 

[此处](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)提供了定价层限制的更多详细信息。

> [!NOTE]
> 若要使用通过购买用于 System Center 的 OMS E1 套件、OMS E2 套件或 OMS 附加产品所获得的权利，请选择 Log Analytics 的“按节点”定价层**。

## <a name="change-the-data-retention-period"></a>更改数据保留期

以下步骤说明如何配置日志数据在工作区中的保留期限。 所有工作区的数据保留时间可配置为 30 到 730 天（2 年），除非它们使用传统的免费定价层。 

### <a name="default-retention"></a>默认保留期

若要设置工作区的默认保留期，请执行以下操作： 
 
1. 在 Azure 门户中，从你的工作区的左窗格中，选择“使用情况和预估成本”。****
2. 在“使用情况和预估成本”页面顶部，单击“数据量管理”。********
3. 在窗格中，移动滑块以增加或减少天数，然后单击“确定”。****  如果位于“免费”层，则不能修改数据保留期，需要升级到付费层才能控制这一项设置。**

    ![更改工作区数据保留设置](media/manage-cost-storage/manage-cost-change-retention-01.png)

降低保留率后，删除最旧数据之前有几天宽限期。 
    
保留期也可使用 `retentionInDays` 参数[通过 Azure 资源管理器进行设置](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace)。 此外，如果将数据保留期设置为 30 天，则可以使用 `immediatePurgeDataOn30Days` 参数对旧数据触发立即清除，这对于合规性相关的方案可能很有用。 此功能仅通过 Azure 资源管理器公开。 

两种数据类型（`Usage` 和 `AzureActivity`）默认保留 90 天，在这 90 天的保留期内不收费。 这些数据类型也没有数据引入费用。 



### <a name="retention-by-data-type"></a>按数据类型分类的保留期

还可以为单个数据类型指定不同的保留设置，从 30 天到 730 天（旧版免费定价层中的工作区除外）。 每个数据类型都是工作区的子资源。 例如，SecurityEvent 表可以在 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)中寻址，如下所示：

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

请注意，数据类型（表）区分大小写。  若要获取特定数据类型（在此示例中为 SecurityEvent）的当前每数据类型保留期设置，请使用：

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

若要获取工作区中所有数据类型的当前每数据类型保留期设置，请直接省略特定的数据类型，例如：

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

若要将特定数据类型（在此示例中为 SecurityEvent）的保留期设置为 730 天，请执行以下代码

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

的有效`retentionInDays`值为 30 到 730。

`Usage` 和 `AzureActivity` 数据类型不能使用自定义保留期进行设置。 它们会使用最大的默认工作区保留期（或 90 天）。 

OSS 工具 [ARMclient](https://github.com/projectkudu/ARMClient) 是一个很好的工具，可以直接连接到 Azure 资源管理器，以便按数据类型设置保留期。  从[大卫·埃博](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)和[丹尼尔·鲍比斯](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)的文章了解更多关于ARMclient。  下面是使用 ARMClient 的示例，将安全事件数据设置为 730 天保留：

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> 可以根据单个数据类型设置保留期，这样可以降低数据保留成本。  如果数据是从 2019 年 10 月（此时发布了该功能）开始收集的，则缩短某些数据类型的保留期可以降低一段时间的保留成本。  如果数据是更早之前收集的，则为单个类型设置较短的保留期不会影响保留成本。  

## <a name="manage-your-maximum-daily-data-volume"></a>管理每日最大数据量

可以配置工作区的每日上限并限制每日引入量，但请谨慎设置，因为目标是避免达到每日限制。  否则，会丢失该天剩余时间的数据，这可能会影响其功能依赖于工作区中提供的最新数据的其他 Azure 服务和解决方案。  因此，需要具有在支持 IT 服务的资源的运行状况受到影响时监视和接收警报的能力。  每日上限旨在用作一种调控受管理资源数据量意外增长并使其保留在限制范围内，或者限制工作区产生计划外费用的方式。  

到达每日限制后不久，计费数据类型的收集将停止一天的剩余时间。 （应用每日上限所固有的延迟可能意味着未精确地应用上限作为指定的每日上限水平。所选日志分析工作区的页面顶部会显示一个警告横幅，操作事件将发送到**日志管理**类别下的 *"操作*"表。 在“每日限制设置时间”定义的重置时间过后，数据收集将会恢复。** 我们建议基于此操作事件定义一个警报规则，并将其配置为在达到每日数据限制时发出通知。 

> [!WARNING]
> 每日上限不会停止从 Azure 安全中心收集数据，2017 年 6 月 19 日之前安装 Azure 安全中心的工作区除外。 

### <a name="identify-what-daily-data-limit-to-define"></a>确定要定义的每日数据限制

查看 [Log Analytics 使用情况和预估成本](usage-estimated-costs.md)，了解数据引入趋势，以及要定义的每日数据量上限。 应该慎重考虑上限，因为在达到限制后，将无法监视资源。 

### <a name="set-the-daily-cap"></a>设置每日上限

以下步骤说明如何配置一个限制来管理 Log Analytics 工作区每日引入的数据量。  

1. 在工作区的左窗格中，选择“使用情况和预估成本”。****
2. 在所选工作区的“使用情况和预估成本”页面顶部，单击“数据量管理”。******** 
3. 默认情况下，每日上限为 **"关闭**"，单击 **"打开**"以启用它，然后以 GB/天设置数据量限制。

    ![Log Analytics 配置数据限制](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>达到每日上限时发出警报

尽管在达到数据限制阈值时，Azure 门户中会显示视觉提示，但此行为不一定符合需要立即关注的操作问题的处理方式。  若要接收警报通知，可以在 Azure Monitor 中创建一个新的警报规则。  要了解更多信息，请参阅[如何创建、查看和管理警报](alerts-metric.md)。

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
- 将数据发送到 Log Analytics 工作区的节点数超出预期
- 发送到 Log Analytics 工作区的数据比预期多（可能是由于开始使用新解决方案或对现有解决方案的配置更改）

## <a name="understanding-nodes-sending-data"></a>了解发送数据的节点

要了解上个月每天从代理报告检测信号的节点数，请使用

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
获取过去 24 小时内发送数据的节点计数使用查询： 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

要获取发送任何数据的节点列表（以及每个数据发送的数据量），可以使用以下查询：

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!TIP]
> 请谨慎使用这些 `union withsource = tt *` 查询，因为跨数据类型执行扫描的开销很大。 此查询将使用 Usage 数据类型替换旧的查询单个计算机信息的方式。  

## <a name="understanding-ingested-data-volume"></a>了解引入的数据量

在“使用情况和预估成本”页上，****“单个解决方案的数据引入”图表** 显示发送的总数据量以及每个解决方案发送的量。 这样就可以确定趋势，例如总数据使用量（或特定解决方案的使用量）是正在增长、保持平稳还是正在下降。 

### <a name="data-volume-for-specific-events"></a>特定事件的数据量

要查看特定事件集的引入数据的大小，可以查询特定表（在此示例中`Event`），然后将查询限制为感兴趣的事件（在此示例中为 ID 5145 或 5156）：

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

请注意，子句`where IsBillable = true`从某些没有引入费用的解决方案中筛选出数据类型。 

### <a name="data-volume-by-solution"></a>按解决方案统计的数据量

用于查看上个月（不包括最后一个部分）的计费数据量的查询是：

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

中的`TimeGenerated`子句仅旨在确保 Azure 门户中的查询体验将回顾默认 24 小时之外。 使用"使用情况"数据类型时，`StartTime`并`EndTime`表示显示结果的时间桶。 

### <a name="data-volume-by-type"></a>按类型进行的数据量

您可以进一步钻取以查看数据类型的数据趋势：

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

或者查看按解决方案和上个月类型的表，

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>按计算机的数据量

数据类型`Usage`不包括完整级别的信息。 要查看每台计算机引入的数据**的大小**，`_BilledSize`请使用 属性 ，该[属性](log-standard-properties.md#_billedsize)以字节为单位提供大小：

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

`_IsBillable` [属性](log-standard-properties.md#_isbillable)指定引入的数据是否会导致收费。

要查看每台计算机引入的计费事件**计数**，请使用 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>按 Azure 资源、资源组或订阅计算的数据量

对于 Azure 中托管节点中的数据，可以获取__每台计算机__的引入数据**的大小**，请使用_ResourceId属性 ，该[属性](log-standard-properties.md#_resourceid)提供资源的完整路径：

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

对于 Azure 中托管节点中的数据，可以__获取每个 Azure 订阅__的引入数据`_ResourceId`**的大小**，将属性分析为：

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

将 `subscriptionId` 更改为 `resourceGroup` 后，就会显示可计费的已引入数据量（按 Azure 资源组计算）。 

> [!WARNING]
> 使用情况数据类型的某些字段虽然仍在架构中，但已弃用，其值将不再填充。 这些是**计算机**以及与引入相关的字段（**TotalBatches**、**BatchesWithinSla**、**BatchesOutsideSla**、**BatchesCapped** 和 **AverageProcessingTimeMs**）。

### <a name="querying-for-common-data-types"></a>查询常见的数据类型

若要更深入地了解特定数据类型的数据源，请使用下面这些有用的示例查询：

+ “安全”解决方案****
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ “日志管理”解决方案****
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ “性能”数据类型****
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ “事件”数据类型****
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ “Syslog”数据类型****
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ AzureDiagnostics**** 数据类型
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>有关如何减少数据量的提示

有关如何减少所收集日志的量的一些提示：

| 高数据量来源 | 如何减少数据量 |
| -------------------------- | ------------------------- |
| 安全性事件            | 选择[通用或最低安全性事件](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> 更改安全审核策略，只收集所需事件。 具体而言，请查看是否需要收集以下对象的事件： <br> - [审核筛选平台](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [审核注册表](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [审核文件系统](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [审核内核对象](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [审核句柄操作](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - 审核可移动存储 |
| 性能计数器       | 更改[性能计数器配置](data-sources-performance-counters.md)如下： <br> - 降低收集频率 <br> - 减少性能计数器数 |
| 事件日志                 | 更改[事件日志配置](data-sources-windows-events.md)如下： <br> - 减少收集的事件日志数 <br> - 仅收集必需的事件级别。 例如，不收集“信息”级别事件** |
| Syslog                     | 更改 [syslog 配置](data-sources-syslog.md)如下： <br> - 减少收集的设施数 <br> - 仅收集必需的事件级别。 例如，不收集“信息”和“调试”级别事件**** |
| AzureDiagnostics           | 更改资源日志集合，以便： <br> - 减少向 Log Analytics 发送日志的资源数目 <br> - 仅收集必需的日志 |
| 不需解决方案的计算机中的解决方案数据 | 使用[解决方案定位](../insights/solution-targeting.md)从所需的计算机组收集数据。 |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>获取按每个节点定价层计费的节点

若要获取将按节点计费的计算机的列表（如果工作区位于旧的“按节点”定价层中），请查找要发送“计费数据类型”****（某些数据类型免费）的节点。 为此，请使用 `_IsBillable` [属性](log-standard-properties.md#_isbillable)，并使用完全限定域名最左边的字段。 这将返回每小时计费数据的计算机计数（即计算和计费节点的粒度）：

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>获取安全性和自动化节点计数

如果你位于“按节点(OMS)”定价层，则根据所用节点和解决方案数收费，需付费的 Insights and Analytics 节点数将显示在“使用情况和预估成本”页的表中****。  

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

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>评估每个节点的旧版定价层

客户通常难以评估在该层或当前**即付**即用或**容量预留**层中有权访问旧**节点**定价层的工作区是否更好。  这包括了解"每个节点"定价层中每个受监视节点的固定成本与其包含的数据分配 500 MB/节点/天之间的权衡，以及只需支付即用即付 （每 GB） 层中引入数据的费用。 

为了便于进行此评估，可以使用以下查询根据工作区的使用模式对最佳定价层提出建议。  此查询查看过去 7 天内引入工作区的受监视节点和数据，并每天评估哪个定价层是最佳的。 要使用查询，您需要通过设置为`workspaceHasSecurityCenter``true`或`false`指定工作区是否使用 Azure 安全中心，然后（可选）更新组织接收的"每个节点"和"每 GB"价格。 

```kusto
// Set these parameters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your price per node / month 
let PerGBPrice = 2.30; // Enter your price per GB 
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
union withsource = tt * 
| where TimeGenerated >= startofday(now(-7d)) and TimeGenerated < startofday(now())
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join (
    Heartbeat 
    | where TimeGenerated >= startofday(now(-7d)) and TimeGenerated < startofday(now())
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated > ago(8d)
    | where StartTime >= startofday(now(-7d)) and EndTime < startofday(now())
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend PerGBDailyCost = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)) * PerGBPrice,
             DataGB * PerGBPrice)
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerGBPrice
| extend Recommendation = iff(PerNodeDailyCost < PerGBDailyCost, "Per Node tier", 
             iff(NonSecurityDataGB > 85., "Capacity Reservation tier", "Pay-as-you-go (Per GB) tier"))
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, Recommendation | sort by day asc
| project day, Recommendation // Comment this line to see details
| sort by day asc
```

此查询不是计算使用情况的确切复制方式，但在大多数情况下，该查询可用于提供定价层建议。  

## <a name="create-an-alert-when-data-collection-is-high"></a>当数据收集量过高时创建警报

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
   - **信号名称**选择**自定义日志搜索**
   - 将“搜索查询”**** 设置为 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`
   - **警报逻辑****基于** *结果数*，**条件***大于***阈值***0*
   - 将“时间段”设置为 1440 分钟，“警报频率”设置为每 60 分钟，因为使用情况数据一小时才更新一次。************
- **定义警报详细信息**指定以下项：
   - 将“名称”**** 设置为“24 小时内的数据量大于 100 GB”**
   - 将“严重性”设置为“警告”******

指定现有的操作组或创建一个新[操作组](action-groups.md)，以便当日志警报匹配条件时，你会收到通知。

为第二个查询创建警报时，如果预测 24 小时内的数据会超出 100 GB，则请进行如下设置：

- **定义警报条件**将 Log Analytics 工作区指定为资源目标。
- **警报条件**指定下列项：
   - **信号名称**选择**自定义日志搜索**
   - 将“搜索查询”**** 设置为 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`
   - **警报逻辑****基于** *结果数*，**条件***大于***阈值***0*
   - 将“时间段”设置为 180 分钟，“警报频率”设置为每 60 分钟，因为使用情况数据一小时才更新一次。************
- **定义警报详细信息**指定以下项：
   - 将“名称”**** 设置为“预期 24 小时内的数据量大于 100 GB”**
   - 将“严重性”设置为“警告”******

指定现有的操作组或创建一个新[操作组](action-groups.md)，以便当日志警报匹配条件时，你会收到通知。

收到警报后，请执行以下部分介绍的步骤，排查使用量超出预期的原因。

## <a name="data-transfer-charges-using-log-analytics"></a>使用 Log Analytics 时的数据传输费用

向 Log Analytics 发送数据可能会产生数据带宽费。 如 [Azure 带宽定价页](https://azure.microsoft.com/pricing/details/bandwidth/)中所述，在两个区域中的 Azure 服务之间传输数据将按正常费率收取出站数据传输费。 入站数据传输是免费的。 但是，相比 Log Analytics 日志数据引入费，此传输费很低（只占几个百分比）。 因此，控制 Log Analytics 的成本需要注重引入的数据量，[此处](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)提供了相关的指导。   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>排查 Log Analytics 不再收集数据的原因

如果采用的是旧版免费定价层并且某天已发送的数据超过 500 MB，则该天的剩余时间会停止数据收集。 达到每日限制是 Log Analytics 停止数据收集或者看起来缺少数据的常见原因。  在数据收集启动和停止时，Log Analytics 会创建一个类型为“操作”的事件。 请在搜索中运行以下查询来检查是否已达到每日限制并缺少数据： 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

当数据收集停止时，OperationStatus 为 **Warning**。 当数据收集启动时，OperationStatus 为 **Succeeded**。 下表描述了数据收集停止的原因以及用于恢复数据收集的建议操作：  

|停止收集的原因| 解决方案| 
|-----------------------|---------|
|达到旧版免费定价层的每日限制 |等到下一天收集自动重启，或者更改为付费定价层。|
|达到了工作区的每日上限|等到收集自动重启，或者根据“管理每日最大数据量”中所述提高每日数据量限制。 每日上限重置时间显示在“数据量管理”页面上****。 |
|Azure 订阅由于以下原因处于挂起状态：<br> 免费试用已结束<br> Azure 许可已过期<br> 已达到每月支出限制（例如，在 MSDN 或 Visual Studio 订阅上）|转换为付费订阅<br> 删除限制，或者等到限制重置|

若想在数据收集停止时收到通知，请使用*创建每日数据上限*警报中所述的步骤，以便在数据收集停止时收到通知。 使用[创建操作组](action-groups.md)中介绍的步骤，为警报规则配置电子邮件、Webhook 或 Runbook 操作。 

## <a name="limits-summary"></a>限制摘要

存在一些其他的 Log Analytics 限制，其中一些限制取决于 Log Analytics 定价层。 这些限制在[此处](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)进行了详细介绍。


## <a name="next-steps"></a>后续步骤

- 若要了解如何使用搜索语言，请参阅 [Azure Monitor Logs 中的日志搜索](../log-query/log-query-overview.md)。 可以使用搜索查询，对使用情况数据执行其他分析。
- 执行[创建新的日志警报](alerts-metric.md)中介绍的步骤，当满足搜索条件时，系统就会通知你。
- 使用[解决方案定位](../insights/solution-targeting.md)从所需的计算机组收集数据。
- 若要配置有效的事件收集策略，请参阅 [Azure 安全中心筛选策略](../../security-center/security-center-enable-data-collection.md)。
- 更改[性能计数器配置](data-sources-performance-counters.md)。
- 若要修改事件收集设置，请参阅[事件日志配置](data-sources-windows-events.md)。
- 若要修改 syslog 收集设置，请参阅 [syslog 配置](data-sources-syslog.md)。