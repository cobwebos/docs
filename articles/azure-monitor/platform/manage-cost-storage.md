---
title: 管理 Azure Monitor 日志的使用情况和成本
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
ms.date: 08/06/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 391a5f054c5d80b255fd333ea416900c8c5ab6d1
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135413"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>使用 Azure Monitor 日志管理使用情况和成本    

> [!NOTE]
> 本文介绍如何了解和控制 Azure Monitor 日志的成本。 相关文章[监视使用情况及预估成本](usage-estimated-costs.md)介绍了如何针对不同的定价模型查看多个 Azure 监视功能的使用情况及预估成本。 本文中显示的所有价格和成本仅用作示例。 

Azure Monitor 日志用于调整和支持来自任何源的巨量数据的每日收集、索引和存储，这些源部署在企业或 Azure 中。  尽管这可能是组织的主要驱动力，但成本效益最终是基本驱动力。 为此，必须了解 Log Analytics 工作区的成本不仅仅是基于收集的数据量，而且也取决于所选的计划，以及连接源生成的数据的存储时间长短。  

本文介绍如何主动监视引入的数据量和存储增长，以及定义限制来控制这些关联的成本。 

## <a name="pricing-model"></a>定价模型

Log Analytics 的默认定价是基于引入的数据量的即用即付模型，还可以选择用于更长的数据保留。 数据量是以 GB（10^9 字节）为单位存储的数据大小来度量的。 每个 Log Analytics 工作区作为独立服务计费，并在 Azure 订阅的账单中产生相应费用。 根据以下因素，数据引入量有时会很大： 

  - 已启用的管理解决方案的数量及其配置
  - 受监视的 VM 数量
  - 从每个受监视 VM 收集的数据类型 
  
除了即用即付模型外，Log Analytics 还具有产能预留层，与即用即付价格相比，使你能够节省多达 25% 的成本。 产能预留定价使你可以购买起价为 100 GB/天的保留。 将按即用即付费率对超出预留级别的任何使用量进行计费。 产能预留层具有 31 天的承诺期。 在承诺期内，你可以更改为更高级别的产能预留层（这将重启 31 天的承诺期），但你不能返回到即用即付或较低的产能预留层，直到承诺期结束。 产能预留层按天计费。 [详细了解](https://azure.microsoft.com/pricing/details/monitor/)有关 Log Analytics 即用即付和产能预留定价。 

在所有定价层中，事件的数据大小都是根据存储在此事件 Log Analytics 中的属性的字符串表示形式进行计算（无论是从代理发送数据还是在引入过程中添加数据）。 这包括在收集数据并随后将数据存储在 Log Analytics 中时添加的任何[自定义字段](custom-fields.md)。 一些对于所有数据类型都通用的属性（包括一些 [Log Analytics 标准属性](log-standard-properties.md)）均未包括在事件大小的计算中。 这包括 `_ResourceId`、`_ItemId`、`_IsBillable`、`_BilledSize` 和 `Type`。 存储在 Log Analytics 中的所有其他属性均包括在事件大小的计算中。 某些数据类型完全免收数据引入费用，例如 AzureActivity、检测信号和使用情况类型。 若要确定事件是否被排除在数据引入计费之外，可使用 `_IsBillable` 属性，[如下所示](#data-volume-for-specific-events)。 使用情况以 GB（1.0E9 字节）为单位进行报告。 

另请注意，某些解决方案（如 [Azure 安全中心](https://azure.microsoft.com/pricing/details/security-center/)、[Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) 和[配置管理](https://azure.microsoft.com/pricing/details/automation/)）具有其自己的定价模型。 

### <a name="log-analytics-dedicated-clusters"></a>Log Analytics 专用群集

Log Analytics 专用群集是收集到单个托管 Azure 数据资源管理器群集中的工作区集合，用于支持高级方案，例如[客户托管的密钥](customer-managed-keys.md)。  与即用即付定价相比，Log Analytics 专用群集仅支持 1000 GB/天起、25% 折扣的产能预留定价模型。 将按即用即付费率对超出预留级别的任何使用量进行计费。 在增加预留级别后，群集产能预留具有 31 天的承诺期。 套餐周期期间，不能减少产能预留级别，但可以随时增加。 详细了解[创建 Log Analytics 群集](customer-managed-keys.md#create-cluster-resource)并[将工作区与其关联](customer-managed-keys.md#workspace-association-to-cluster-resource)。  

使用 `Sku` 下的 `Capacity` 参数，通过 Azure 资源管理器以编程方式配置群集产能预留级别。 `Capacity` 指定 GB 为单位，并且值可以为 1000 GB/天或更大，增量为 100 GB/天。 这是[Azure Monitor 客户管理的密钥](customer-managed-keys.md#create-cluster-resource)的详细信息。 如果群集需要的预留超过 2000 GB/天，请通过 [LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com) 联系我们。

对于群集上的使用情况，有两种计费模式。 `billingType`[配置群集](customer-managed-keys.md#cmk-management)时，可以通过参数指定这些参数。 这两种模式是： 

1. **群集**：在这种情况下 (这是默认) ，引入数据的计费在群集级别完成。 将聚合与群集关联的每个工作区中的引入数据数量，以计算群集的每日账单。 请注意，在跨群集中所有工作区的聚合数据聚合之前，将在工作区级别应用基于 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/)的按节点分配。 

2. **工作区**：在为每个工作区的[Azure 安全中心](https://docs.microsoft.com/azure/security-center/)中的每个节点分配记帐后，群集的容量保留成本将按节点分配给群集中的工作区 (。 ) 如果某一天的工作区中的总数据量引入小于产能预留量，则每个工作区都按每 GB 的有效容量预留费率对其引入数据进行计费，方法是将其计费，并将容量预留的未使用部分计费到群集资源。 如果某一天的工作区中的总数据量引入超过了容量预留量，则每个工作区都将根据该日期的引入数据的一小部分按容量预留量计费，并且每个工作区都按容量预留量的一小部分引入数据计费。 如果一天的工作区引入的总数据量超出容量预留，则不会对群集资源计费。

在群集计费选项中，数据保留期按工作区级别计费。 请注意，群集计费在创建群集时开始，无论工作区是否已关联到群集。 另请注意，与群集关联的工作区不再具有定价层。

## <a name="estimating-the-costs-to-manage-your-environment"></a>估计管理环境的成本 

如果尚未使用 Azure Monitor 日志，可以使用 [Azure Monitor 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=monitor)来估计使用 Log Analytics 的成本。 首先在搜索框中输入“Azure Monitor”，然后单击生成的 Azure Monitor 磁贴。 将页面向下滚动到“Azure Monitor”，然后从“类型”下拉列表中选择“Log Analytics”。  可在此处输入 VM 数和要从每个 VM 收集的 GB 数。 通常，每月从典型的 Azure VM 可引入 1 到 3 GB 数据。 如果已在评估 Azure Monitor 日志，则可以使用自己环境中的数据统计信息。 请参阅以下内容，了解如何确定[所监视的 VM 的数量](#understanding-nodes-sending-data)和[工作区引入的数据量](#understanding-ingested-data-volume)。 

## <a name="understand-your-usage-and-estimate-costs"></a>了解你的使用情况和估计成本

如果你现在正在使用 Azure Monitor 日志，根据最近的使用模式很容易理解可能的成本。 若要执行此操作，请使用“Log Analytics 使用情况和预估成本”查看和分析数据使用情况。 这显示每个解决方案收集的数据量、保留的数据量，并根据引入的数据量和已包含量之外的其他保留量来估算成本。

![使用情况和预估成本](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

若要更详细地探索数据，请单击“使用情况和预估成本”页上任一图表右上侧的图标。 现在可以使用此查询来探索有关使用情况的更多详细信息。  

![日志视图](media/manage-cost-storage/logs.png)

从“使用情况和估计成本”页面，可以查看当月的数据量。 这包括已接收并保留在 Log Analytics 工作区中的所有计费数据。  
 
Log Analytics 费用将添加到 Azure 帐单。 可以在 Azure 门户的“计费”部分或在 [Azure 计费门户](https://account.windowsazure.com/Subscriptions)中查看 Azure 账单详细信息。  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>查看 Azure 账单上的 Log Analytics 使用情况 

Azure 在 [Azure 成本管理和计费](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json)中心提供了大量实用功能。 例如，使用“成本分析”功能可以查看 Azure 资源的开支。 首先，按“资源类型”添加筛选器（对于 Log Analytics，将其添加到 microsoft.operationalinsights/workspace，对于Log Analytics 群集，将其添加到 microsoft.operationalinsights/workspace），你可以跟踪自己的 Log Analytics 支出。 然后，对于“分组依据”，选择“计量类别”或“计量”。  请注意，其他服务（例如 Azure 安全中心和 Azure Sentinel）还会根据 Log Analytics 工作区资源对其使用情况进行计费。 若要查看服务名称映射，可以选择表视图而不是图表。 

通过[在 Azure 门户中下载使用情况信息](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)，可以更好地了解你的使用情况。 在下载的电子表格中，你可以查看每天每个 Azure 资源（例如 Log Analytics 工作区）的使用情况。 在此 Excel 电子表格中，可以首先基于“计量类别”列进行筛选以显示“Log Analytics”、“Insights and Analytics”（由某些旧定价层使用）和“Azure Monitor”（由“产能预留”定价层使用），借此查找 Log Analytics 工作区中的使用情况，然后在“实例 ID”列上添加一个筛选器：“包含工作区”或“包含群集”（后者包括 Log Analytics 群集的使用情况）。 使用情况显示在“已耗用数量”列中，每个条目的单位显示在“度量单位”列中。  还有更多详细信息可帮助你[了解 Microsoft Azure 账单](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)。 

## <a name="changing-pricing-tier"></a>更改定价层

若要更改工作区的 Log Analytics 定价层，请执行以下操作： 

1. 在 Azure 门户中，打开工作区的“使用情况和预估成本”，你会在其中看到此工作区可用的每个定价层的列表。

2. 查看每个定价层的预估成本。 此估算基于最近 31 天的使用情况，因此，此成本估算依赖于最近 31 天有代表性的典型使用情况。 在下面的示例中，你可以看到，根据最近 31 天的数据模式，此工作区在即用即付层 (#1) 中的成本与产能预留层的 100 GB/天相比要少多少。  

    ![定价层](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. 查看基于最近 31 天的使用情况的预估成本后，如果决定更改定价层，请单击“选择”。  

你也可以使用 `sku` 参数（Azure 资源管理器模板中的 `pricingTier`）[通过 Azure 资源管理器设置定价层](template-workspace-configuration.md#configure-a-log-analytics-workspace)。 

## <a name="legacy-pricing-tiers"></a>旧版定价层

在 2018 年 4 月 2 日之前拥有 Log Analytics 工作区或 Application Insights 资源的订阅，或与 2019 年 2 月 1 日之前开始的企业协议链接的订阅，将继续有权使用旧定价层：免费定价层、独立定价层（每 GB）和按节点定价层 (OMS)。    免费定价层中的工作区将每日数据引入限制为 500 MB（[Azure 安全中心](https://docs.microsoft.com/azure/security-center/)收集的安全数据类型除外），数据保留期限制为 7 天。 免费定价层仅用于评估目的。 独立定价层或按节点定价层中的工作区具有用户可配置的 30 至 730 天的保留期。

独立定价层上的使用情况由引入数据量计费。 它在**Log Analytics**服务中报告，计量器名为 "数据已分析"。 

按节点定价层按小时粒度对每个受监视的 VM（节点）收费。 对于每个受监视的节点，每天为工作区分配 500 MB 的不计费数据。 此分配在工作区级别聚合。 超出每日数据分配聚合的引入数据在数据超额时按 GB 计费。 请注意，如果工作区位于“按节点”定价层中，则在账单上，对于 Log Analytics 使用情况，该服务将是 Insight and Analytics。 使用三计量报告：

1. 节点：这是 (Vm 的受监视节点数的使用情况) ，单位为 node * 个月。
2. 每个节点的数据超额数：这是超出聚合数据分配的引入的数据量（GB）。
3. 每个节点包含的数据：这是聚合数据分配所涵盖的引入数据量。 当工作区在所有定价层中时，也可以使用此计数来显示 Azure 安全中心涵盖的数据量。

> [!TIP]
> 如果你的工作区有权访问“按节点”定价层，但你想知道在即用即付层中成本是否更低，则可以 [使用以下查询](#evaluating-the-legacy-per-node-pricing-tier)轻松获取建议。 

2016 年 4 月之前创建的工作区还可以访问原始“标准”定价层和“高级”定价层，它们分别有 30 天和 365 天的固定数据保留期。  无法在**标准**或**高级**定价层中创建新的工作区，并且如果将工作区移出这些层，则无法将其移回。 这些旧式层的数据引入计量称为 "数据分析"。

在使用旧 Log Analytics 层和 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/)的使用情况计费方式之间还有一些行为。 

1. 如果工作区位于旧版标准或高级层中，则仅针对 Log Analytics 数据引入而不是按节点对 Azure 安全中心进行计费。
2. 如果工作区位于旧版按节点层，将使用当前 [Azure 安全中心基于节点的定价模型](https://azure.microsoft.com/pricing/details/security-center/)对 Azure 安全中心进行计费。 
3. 在其他定价层（包括产能预留）中，如果 Azure 安全中心在 2017 年 6 月 19 日之前已启用，将仅针对 Log Analytics 数据引入对 Azure 安全中心进行计费。 否则，将使用当前 Azure 安全中心基于节点的定价模型对 Azure 安全中心进行计费。

有关定价层限制的更多详细信息[，请参阅 Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)。

旧版定价层都没有基于区域的定价。  

> [!NOTE]
> 若要使用通过购买用于 System Center 的 OMS E1 套件、OMS E2 套件或 OMS 附加产品所获得的权利，请选择 Log Analytics 的“按节点”定价层。

## <a name="change-the-data-retention-period"></a>更改数据保留期

以下步骤说明如何配置日志数据在工作区中的保留期限。 对于所有工作区，数据保留期均可以配置为 30 到 730 天（2 年），除非它们使用旧版免费定价层。[详细了解](https://azure.microsoft.com/pricing/details/monitor/)更长数据保留期的定价。 

### <a name="default-retention"></a>默认保留期

若要设置工作区的默认保留期，请执行以下操作： 
 
1. 在 Azure 门户中，从工作区的左窗格中，选择“使用情况和预估成本”。
2. 在 "**使用情况和预估成本**" 页上，单击页面顶部的 "**数据保留**"。
3. 在窗格中，移动滑块以增加或减少天数，然后单击“确定”。  如果位于“免费”层，则不能修改数据保留期，需要升级到付费层才能控制这一项设置。

    ![更改工作区数据保留设置](media/manage-cost-storage/manage-cost-change-retention-01.png)

如果保留时间降低，则在删除早于新保留设置的数据之前，会有几天的宽限期。 

还可以使用 `retentionInDays` 参数[通过 Azure 资源管理器](template-workspace-configuration.md#configure-a-log-analytics-workspace)设置保留期。 将数据保留期设置为30天后，可以使用参数立即触发旧数据的清除， `immediatePurgeDataOn30Days` (消除几天的宽限期) 。 这对于符合性相关的方案非常有用，在这种情况下，需要立即删除数据。 这种立即清除功能只能通过 Azure 资源管理器公开。 

保留时间为30天的工作区实际上可能保留了31天的数据。 如果要求仅保留30天的数据，请使用 Azure 资源管理器将保留期设置为30天，并使用 `immediatePurgeDataOn30Days` 参数。  

默认情况下，两种数据类型（`Usage` 和 `AzureActivity`）将保留至少 90 天，并且对于这 90 天的保留期，不收取任何费用。 如果工作区的保留期超过 90 天，则这些数据类型的保留期也将增加。  这些数据类型也不收取数据引入费用。 

默认情况下，基于工作区的 Application Insights 资源（`AppAvailabilityResults`、`AppBrowserTimings`、`AppDependencies`、`AppExceptions`、`AppEvents`、`AppMetrics`、`AppPageViews`、`AppPerformanceCounters`、`AppRequests`、`AppSystemEvents` 和 `AppTraces`）中的数据类型也保留 90 天，并且对于这 90 天的保留期，不收取任何费用。 可以使用按数据类型保留功能调整其保留期。 

请注意，Log Analytics[清除 API](https://docs.microsoft.com/rest/api/loganalytics/workspacepurge/purge)不会影响保留计费，并且适用于非常有限的情况。 若要减少留成帐单，必须为工作区或特定数据类型减少保持期。 

### <a name="retention-by-data-type"></a>按数据类型保留

还可以为各个数据类型指定从 30 到 730 天的不同保留期设置（旧版免费定价层中的工作区除外）。 每个数据类型都是工作区的子资源。 例如，可以在 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)中对 SecurityEvent 表进行寻址，如下所示：

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

请注意，数据类型（表）区分大小写。  若要获取特定数据类型（在此示例中为 SecurityEvent）的当前每个数据类型的保留期设置，请使用：

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

若要获取工作区中所有数据类型的当前每个数据类型保留期设置，只需省略特定的数据类型，例如：

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

若要将特定数据类型（在本例中为 SecurityEvent）的保留期设置为 730 天，请执行以下操作：

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

`retentionInDays` 的有效值为 30 到 730。

`Usage` 和 `AzureActivity` 数据类型不能设置自定义保留期。 它们将采用默认工作区保留期的最大值或 90 天。 

用于直接连接到 Azure 资源管理器以按数据类型设置保留期的极佳工具是 OSS 工具 [ARMclient](https://github.com/projectkudu/ARMClient)。  从 [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) 和 [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/) 撰写的文章中了解有关 ARMclient 的详细信息。  下面是一个使用 ARMClient 的示例，为 SecurityEvent 数据设置 730 天的保留期：

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> 设置各个数据类型的保留期可用于降低数据保留成本。  对于从 2019 年 10 月（发布此功能时）开始收集的数据，减少某些数据类型的保留期可降低一段时间内的保留成本。  对于较早收集的数据，为单个类型设置较低的保留期不会影响你的保留成本。  

## <a name="manage-your-maximum-daily-data-volume"></a>管理每日最大数据量

可以配置工作区的每日上限并限制每日引入量，但请谨慎设置，因为目标是避免达到每日限制。  否则，会丢失该天剩余时间的数据，这可能会影响其功能依赖于工作区中提供的最新数据的其他 Azure 服务和解决方案。  因此，需要具有在支持 IT 服务的资源的运行状况受到影响时监视和接收警报的能力。  每日上限旨在用作一种方法，用于管理来自托管资源的**意外增加**的数据量，并保持在限制范围内，或者当你希望限制工作区的计划外费用时使用。 不适合设置每日上限，以便在工作区中每天都能满足这一要求。

每个工作区在一天中的不同时间应用每日上限。 "重置时间" 显示在 "**每日上限**" 页中 (参见下面) 。 无法配置此 reset 小时。 

达到每日限制后，在当天的剩余时间会停止收集应计费数据类型。 应用每日上限所固有的延迟意味着不会精确地在指定的每日上限级别应用 cap。 选定 Log Analytics 工作区的页面顶部会显示警告横幅，同时会将一个操作事件发送到“LogManagement”类别下的“操作”表。****** 在“每日限制设置时间”定义的重置时间过后，数据收集将会恢复。 我们建议基于此操作事件定义一个警报规则，并将其配置为在达到每日数据限制时发出通知。 

> [!NOTE]
> 每日上限不能正好按指定的 cap 级别停止数据收集，并且需要某些多余的数据，尤其是在工作区接收大量数据的情况下。  

> [!WARNING]
> 除了在2017年6月19日之前安装了 Azure 安全中心的工作区之外，每日上限不会停止从 Azure 前哨或 Azure 安全中心收集数据。 

### <a name="identify-what-daily-data-limit-to-define"></a>确定要定义的每日数据限制

查看 [Log Analytics 使用情况和预估成本](usage-estimated-costs.md)，了解数据引入趋势，以及要定义的每日数据量上限。 应该慎重考虑此上限，因为在达到限制后，将无法监视资源。 

### <a name="set-the-daily-cap"></a>设置每日上限

以下步骤说明如何配置一个限制来管理 Log Analytics 工作区每日引入的数据量。  

1. 在工作区的左窗格中，选择“使用情况和预估成本”。
2. 在所选工作区的 "**使用情况和预估成本**" 页上，单击页面顶部的 "**数据上限**"。 
3. 每日上限默认为“关闭”。 单击“打开”将其启用，然后设置数据量限制（以 GB/天为单位）。

    ![Log Analytics 配置数据限制](media/manage-cost-storage/set-daily-volume-cap-01.png)
    
可以通过 `dailyQuotaGb` 在 " `WorkspaceCapping` [工作区-创建" 或 "更新](https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate#workspacecapping)" 中所述的下设置参数，通过 ARM 配置每日上限。 

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

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>排查使用量超出预期的原因

使用量较高是由下面的一个或两个原因引起的：
- 将数据发送到 Log Analytics 工作区的节点数超出预期
- 发送到 Log Analytics 工作区的数据超出预期（可能由于开始使用新的解决方案或现有解决方案发生配置更改）

## <a name="understanding-nodes-sending-data"></a>了解发送数据的节点

若要了解在上个月每天报告来自代理的检测信号的节点数，请使用：

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
使用以下查询获取最近 24 小时内发送数据的节点数： 

```kusto
find where TimeGenerated > ago(24h) project Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

若要获取发送任何数据的节点列表（以及由每个节点发送的数据量），可以使用以下查询：

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!TIP]
> 请谨慎使用这些 `find` 查询，因为跨数据类型执行扫描会[占用大量资源](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane)。 如果不需要每台计算机的结果，则基于使用情况数据类型查询（见下文）。

## <a name="understanding-ingested-data-volume"></a>了解引入的数据量

在“使用情况和预估成本”页上，“单个解决方案的数据引入”图表显示发送的总数据量以及每个解决方案发送的量。 这样就可以确定趋势，例如总数据使用量（或特定解决方案的使用量）是正在增长、保持平稳还是正在下降。 

### <a name="data-volume-for-specific-events"></a>特定事件的数据量

若要查看一组特定事件的引入数据的大小，可以查询特定的表（在此示例中为 `Event`），然后将查询限制为相关事件（在此示例中，事件 ID 为 5145 或 5156）：

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

请注意，子句 `where _IsBillable = true` 从某些解决方案中筛选掉没有引入费用的数据类型。 [了解更多](log-standard-properties.md#_isbillable)相关信息 `_IsBillable` 。

### <a name="data-volume-by-solution"></a>按解决方案统计的数据量

用于按解决方案查看上个月（不包括最后不完整的一天）的计费数据量的查询是：

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

带有 `TimeGenerated` 的子句仅用于确保 Azure 门户中的查询体验将回溯到超出默认的 24 小时。 使用“使用情况”数据类型时，`StartTime` 和 `EndTime` 表示显示结果的时间存储桶。 

### <a name="data-volume-by-type"></a>按类型的数据量

可以进一步钻取，按数据类型查看数据趋势：

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

或者按解决方案和类型查看上个月的表，

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>按计算机的数据量

`Usage` 数据类型不包括计算机级别的信息。 若要查看每台计算机引入数据的大小，请使用 `_BilledSize` [属性](log-standard-properties.md#_billedsize)（以字节为单位提供大小）：

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName 
| sort by BillableDataBytes nulls last
```

`_IsBillable` [属性](log-standard-properties.md#_isbillable)指定引入的数据是否会产生费用。 

若要查看每台计算机引入的计费事件数，请使用： 

```kusto
find where TimeGenerated > ago(24h) project _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  
| sort by eventCount nulls last
```

> [!TIP]
> 请谨慎使用这些 `find` 查询，因为跨数据类型执行扫描会[占用大量资源](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane)。 如果不需要每台计算机的结果，则基于使用情况数据类型查询。

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>按 Azure 资源、资源组或订阅的数据量

对于在 Azure 托管节点中的数据，可以获取每台计算机的引入数据的大小，请使用 _ResourceId [属性](log-standard-properties.md#_resourceid)，它提供资源的完整路径：

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by BillableDataBytes nulls last
```

对于 Azure 中托管的节点的数据，可以获取__每个 azure 订阅__的引入数据的**大小**，获取订阅 ID 的属性，如下所示 `_ResourceId` ：

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend subscriptionId = tostring(split(_ResourceId, "/")[2]) 
| summarize BillableDataBytes = sum(BillableDataBytes) by subscriptionId | sort by BillableDataBytes nulls last
```

同样，若要按资源组获取数据量，请执行以下操作：

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend resourceGroup = tostring(split(_ResourceId, "/")[4] )
| summarize BillableDataBytes = sum(BillableDataBytes) by resourceGroup | sort by BillableDataBytes nulls last
```

如果需要，还可以 `_ResourceId` 使用来更完全地分析

```Kusto
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
```

> [!TIP]
> 请谨慎使用这些 `find` 查询，因为跨数据类型执行扫描会[占用大量资源](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane)。 如果你不需要每个订阅、资源组或资源名称的结果，则基于使用情况数据类型查询。

> [!WARNING]
> 使用情况数据类型的某些字段虽然仍在架构中，但已弃用，其值将不再填充。 这些是**计算机**以及与引入相关的字段（**TotalBatches**、**BatchesWithinSla**、**BatchesOutsideSla**、**BatchesCapped** 和 **AverageProcessingTimeMs**）。


### <a name="querying-for-common-data-types"></a>针对常见数据类型进行查询

若要更深入地了解特定数据类型的数据源，请使用下面这些有用的示例查询：

+ 基于工作区的 Application Insights 资源
  - 有关详细信息[，请参阅管理 Application Insights 的使用情况和成本](../app/pricing.md#data-volume-for-workspace-based-application-insights-resources)
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

## <a name="tips-for-reducing-data-volume"></a>有关如何减少数据量的提示

有关如何减少所收集日志的量的一些提示：

| 高数据量来源 | 如何减少数据量 |
| -------------------------- | ------------------------- |
| 容器见解         | [将 Container Insights 配置](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-cost#controlling-ingestion-to-reduce-cost)为仅收集你需要的数据。 |
| 安全性事件            | 选择[通用或最低安全性事件](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> 更改安全审核策略，只收集所需事件。 具体而言，请查看是否需要收集以下对象的事件： <br> - [审核筛选平台](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [审核注册表](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [审核文件系统](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [审核内核对象](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [审核句柄操作](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - 审核可移动存储 |
| 性能计数器       | 更改[性能计数器配置](data-sources-performance-counters.md)如下： <br> - 降低收集频率 <br> - 减少性能计数器数 |
| 事件日志                 | 更改[事件日志配置](data-sources-windows-events.md)如下： <br> - 减少收集的事件日志数 <br> - 仅收集必需的事件级别。 例如，不收集“信息”级别事件 |
| Syslog                     | 更改 [syslog 配置](data-sources-syslog.md)如下： <br> - 减少收集的设施数 <br> - 仅收集必需的事件级别。 例如，不收集“信息”和“调试”级别事件  |
| AzureDiagnostics           | 更改资源日志集合，以便： <br> - 减少向 Log Analytics 发送日志的资源数目 <br> - 仅收集必需的日志 |
| 不需解决方案的计算机中的解决方案数据 | 使用[解决方案目标](../insights/solution-targeting.md)，只从必需的计算机组收集数据。 |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>获取按节点定价层中的计费节点

若要获取将按节点计费的计算机列表（如果工作区位于旧版按节点定价层中），请查找发送计费数据类型（某些数据类型是免费的）的节点。 为此，请使用 `_IsBillable` [属性](log-standard-properties.md#_isbillable)，并使用完全限定的域名最左边的字段。 这将返回每小时（这是对节点进行计数和计费的粒度）具有计费数据的计算机数：

```kusto
find where TimeGenerated > ago(24h) project Computer, TimeGenerated
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

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

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>评估旧版按节点定价层

对于能够访问旧版按节点定价层的工作区，是留在该层更好，还是在当前的即用即付或产能预留层更好，客户通常很难评估这一决策。    这涉及了解按节点定价层中每个受监视节点的固定成本与其包括的每天每个节点 500 MB 数据分配之间的权衡，以及只需支付即用即付（每 GB）层中的引入数据的成本。 

为了促进此评估，可以使用以下查询根据工作区的使用情况模式提出最佳定价层的建议。  此查询可查看最近 7 天内受监视的节点和引入到工作区中的数据，并每天评估哪个定价层最佳。 若要使用查询，需要

1. 指定工作区是否通过将 `workspaceHasSecurityCenter` 设置为 `true` 或 `false` 来使用 Azure 安全中心， 
2. 更新价格（如果有特定折扣）以及
3. 通过设置 `daysToEvaluate` 指定要回溯和分析的天数。 如果查询花费太长时间尝试查看 7 天的数据，则这一点很有用。 

以下是定价层建议查询：

```kusto
// Set these parameters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let daysToEvaluate = 7; // Enter number of previous days look at (reduce if the query is taking too long)
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
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
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend Recommendation = iff(PerNodeDailyCost < PerGBDailyCost, "Per Node tier", 
             iff(NonSecurityDataGB > 85., "Capacity Reservation tier", "Pay-as-you-go (Per GB) tier"))
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, Recommendation | sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

此查询不是使用情况计算方式的精确复制，但在大多数情况下可用于提供定价层建议。  

> [!NOTE]
> 若要使用通过购买用于 System Center 的 OMS E1 套件、OMS E2 套件或 OMS 附加产品所获得的权利，请选择 Log Analytics 的“按节点”定价层。

## <a name="create-an-alert-when-data-collection-is-high"></a>当数据收集量很高时创建警报

本部分介绍如何使用 Azure Monitor[日志警报](alerts-unified-log.md)，在过去24小时内创建的数据卷超过指定的数量。 

若要在过去24小时内引入的计费数据量为大于 50 GB 时发出警报，请执行以下步骤： 

- **定义警报条件**将 Log Analytics 工作区指定为资源目标。
- **警报条件**指定下列项：
   - **信号名称**选择“自定义日志搜索”。
   - **搜索查询**到 `Usage | where IsBillable | summarize DataGB = sum(Quantity / 1000.) | where DataGB > 50` 。 如果需要不同 
   - 警报逻辑基于结果数，条件大于阈值 0 
   - 每隔*1440* minutesto 每隔一天运行一次的**时间段** *1440*分钟和**警报频率**。
- **定义警报详细信息**指定以下项：
   - *24 小时内大于 50 GB 的可计费数据卷的***名称**
   - 将“严重性”设置为“警告”

指定现有的操作组或创建一个新[操作组](action-groups.md)，以便当日志警报匹配条件时，你会收到通知。

当你收到警报时，请使用上述部分中有关如何排查使用量高于预期的原因的步骤。

## <a name="data-transfer-charges-using-log-analytics"></a>使用 Log Analytics 的数据传输费用

向 Log Analytics 发送数据可能会产生数据带宽费用。 如 [Azure 带宽定价页](https://azure.microsoft.com/pricing/details/bandwidth/)中所述，位于两个区域内的 Azure 服务之间的数据传输按出站数据传输以正常费率计费。 入站数据传输是免费的。 但是，与 Log Analytics 数据引入的成本相比，这项费用微不足道（几个百分点）。 因此控制 Log Analytics 的成本需要专注于[引入数据量](#understanding-ingested-data-volume)。 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>排查 Log Analytics 不再收集数据的原因

如果采用的是旧版免费定价层并且某天已发送的数据超过 500 MB，则该天的剩余时间会停止数据收集。 达到每日限制是 Log Analytics 停止数据收集或者看起来缺少数据的常见原因。  在数据收集启动和停止时，Log Analytics 会创建一个类型为“操作”的事件。 请在搜索中运行以下查询来检查是否已达到每日限制并缺少数据： 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

当数据收集停止时，OperationStatus 为“警告”。 当数据收集启动时，OperationStatus 为“成功”。 下表描述了数据收集停止的原因以及用于恢复数据收集的建议操作：  

|停止收集的原因| 解决方案| 
|-----------------------|---------|
|达到了工作区的每日上限|等到收集自动重启，或者根据“管理每日最大数据量”中所述提高每日数据量限制。 每日上限重置时间显示在 "**每日上限**" 页上。 |
| 你的工作区已达到[数据引入量速率](https://docs.microsoft.com/azure/azure-monitor/service-limits#log-analytics-workspaces) | 使用诊断设置从 Azure 资源发送的数据的默认引入量限制约为每个工作区 6 GB/分钟。 这是一个近似值，因为实际大小在数据类型之间可能会有所不同，具体取决于日志长度及其压缩率。 此限制不适用于从代理或数据收集器 API 发送的数据。 如果以更高速率将数据发送到单个工作区，则某些数据将丢弃，并且在继续超过阈值的情况下，每 6 小时将向工作区中的“操作”表发送一个事件。 如果引入量继续超过速率限制，或者希望很快达到该限制，则可以通过向 LAIngestionRate@microsoft.com 发送电子邮件或提交支持请求来请求增加工作区。 要查找的事件，该事件指示可通过查询找到数据引入速率限制 `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The rate of data crossed the threshold"` 。 |
|达到旧版免费定价层的每日限制 |等到下一天收集自动重启，或者更改为付费定价层。|
|Azure 订阅由于以下原因处于挂起状态：<br> 免费试用已结束<br> Azure 许可已过期<br> 已达到每月支出限制（例如，在 MSDN 或 Visual Studio 订阅上）|转换为付费订阅<br> 删除限制，或者等到限制重置|

若要在数据收集停止时收到通知，请使用“创建每日数据上限”警报中所述的步骤，以便在数据收集停止时收到通知。 使用[创建操作组](action-groups.md)中所述的步骤，为警报规则配置电子邮件、Webhook 或 Runbook 操作。 

## <a name="limits-summary"></a>限制摘要

还有一些其他 Log Analytics 限制，其中一些限制依赖于 Log Analytics 定价层。 它们在[Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)中进行了介绍。


## <a name="next-steps"></a>后续步骤

- 若要了解如何使用搜索语言，请参阅 [Azure Monitor 日志中的日志搜索](../log-query/log-query-overview.md)。 可以使用搜索查询，对使用情况数据执行其他分析。
- 执行[创建新的日志警报](alerts-metric.md)中介绍的步骤，当满足搜索条件时，系统就会通知你。
- 使用[解决方案目标](../insights/solution-targeting.md)，只从必需的计算机组收集数据。
- 若要配置有效的事件收集策略，请参阅 [Azure 安全中心筛选策略](../../security-center/security-center-enable-data-collection.md)。
- 更改[性能计数器配置](data-sources-performance-counters.md)。
- 若要修改事件收集设置，请参阅[事件日志配置](data-sources-windows-events.md)。
- 若要修改 syslog 收集设置，请参阅 [syslog 配置](data-sources-syslog.md)。