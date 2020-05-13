---
title: 管理 Azure Application Insights 的使用情况和成本 | Microsoft docs
description: 在 Application Insights 中管理遥测量并监视成本。
ms.topic: conceptual
author: DaleKoetke
ms.author: dalek
ms.date: 5/7/2020
ms.reviewer: mbullwin
ms.openlocfilehash: b99c1c9348f8442233eeee8fd4442736c78ee4e4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199040"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>管理 Application Insights 的使用情况和成本

> [!NOTE]
> 本文介绍如何了解和控制 Application Insights 的成本。  相关文章[监视使用情况和估算成本](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs)介绍了如何针对不同的定价模型查看多个 Azure 监视功能的使用情况及估算成本。

无论 Web 应用程序托管在 Azure 中还是本地，Application Insights 都会提供所需的功能让你监视其可用性、性能和使用情况。 Application Insights 支持 .NET、Java 和 Node.js 等流行语言和框架，并与 Azure DevOps、Jira 和 PagerDuty 等 DevOps 流程和工具相集成。 必须了解哪些因素决定了应用程序的监视成本。 本文介绍应用程序监视成本的造成因素，以及如何主动监控这些成本。

如果对 Application Insights 定价工作原理存在疑问，欢迎在我们的[论坛](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights&filter=alltypes&sort=lastpostdesc)提出问题。

## <a name="pricing-model"></a>定价模型

[Azure Application Insights][start] 的定价采用**即用即付**模型，该模型基于数据引入量，有时还基于长期数据保留策略。 每个 Application Insights 资源作为独立服务计费，并在 Azure 订阅的帐单中产生相应费用。 数据量的衡量标准是 Application Insights 从用户应用程序收到的未压缩 JSON 数据包的大小。 使用[实时指标流](../../azure-monitor/app/live-stream.md)不会根据数据量收费。

[多步骤 Web 测试](../../azure-monitor/app/availability-multistep.md)会额外收费。 多步骤 Web 测试是指执行一系列操作的 Web 测试。 单页“ping 测试”不单独计费  。 进行 ping 测试和多步测试时发送的遥测数据与应用发送的其他遥测数据计费方式相同。

用于对[自定义指标维度启用警报](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation)的 Application Insights 选项也可能会产生额外的费用，因为这可能会导致创建其他预聚合度量值。 [详细了解](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics)Application Insights 中基于日志和预聚合的度量值，以及有关 Azure Monitor 自定义指标的[定价](https://azure.microsoft.com/pricing/details/monitor/)。

### <a name="workspace-based-application-insights"></a>基于工作区的 Application Insights

对于将数据发送到 Log Analytics 工作区的 Application Insights 资源（称为[基于工作区的 Application Insights 资源](create-workspace-resource.md)），数据引入和保留的计费由 Application Insights 数据所在的工作区完成。 这使客户能够利用除即用即付之外的 Log Analytics[定价模型](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#pricing-model)的所有选项。 Log Analytics 还提供了更多的数据保留选项，包括[按数据类型进行保留](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#retention-by-data-type)。 工作区中的 Application Insights 数据类型接收90天的保留期，无需支付费用。 使用 web 测试和启用自定义指标维度上的警报时，仍通过 Application Insights 来报告。 了解如何使用[使用情况和估计成本](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs)、 [Azure 成本管理 + 计费](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#viewing-log-analytics-usage-on-your-azure-bill)和[Log Analytics 查询](#data-volume-for-workspace-based-application-insights-resources)，在 Log Analytics 中跟踪数据引入和保留成本。 

## <a name="estimating-the-costs-to-manage-your-application"></a>估算应用程序的管理成本

如果你尚未使用 Application Insights，可以使用 [Azure Monitor 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=monitor)来估算 Application Insights 的使用成本。 首先在搜索框中输入“Azure Monitor”，然后单击生成的“Azure Monitor”磁贴。 在页面中向下滚动到“Azure Monitor”，然后从“类型”下拉列表中选择“Application Insights”。  您可以在此输入每月要收集的数据的 GB 数，因此问题是 Application Insights 收集监视应用程序的数据量。

可通过两种方法找到此问题的解答：使用 ASP.NET SDK 中提供的默认监视和自适应采样，或根据其他类似客户遇到的情况估算可能的数据引入量。

### <a name="data-collection-when-using-sampling"></a>使用采样时的数据收集

使用 ASP.NET SDK 的[自适应采样](sampling.md#adaptive-sampling)时，数据量会自动调整，以保持在默认 Application Insights 监视功能的最大指定流量速率范围内。 如果应用程序产生的遥测数据很少（例如在调试时或由于用量较小），则只要数量低于配置的每秒事件数级别，采样处理器就不会丢弃项。 对于大容量应用程序，默认阈值为每秒5个事件，自适应采样会将每日事件的数目限制为432000。 使用典型的平均事件大小 1 KB 相当于托管应用程序的每个节点每月（31 天）可引入 13.4 GB 的遥测数据（因为采样是在每个节点本地完成的。） 

对于不支持自适应采样的 SDK，可以使用[引入采样](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling)（Application Insights 收到数据时根据要保留的数据百分比采样），或使用 [ASP.NET、ASP.NET Core 和 Java 网站的固定速率采样](sampling.md#fixed-rate-sampling)来减少从 Web 服务器和 Web 浏览器发送的流量

### <a name="learn-from-what-similar-customers-collect"></a>参考类似客户收集的信息

在 Application Insights 的 Azure 监视定价计算器中，如果启用“基于应用程序活动估算数据量”功能，则可以提供有关应用程序的输入（如果收集客户端遥测数据，请提供每月请求数和页面视图数），然后，计算器会告知类似应用程序收集的中间值和第 90 百分位的数据量。 这些应用程序在 Application Insights 配置范围内（例如，某些应用程序具有默认采样、有些[采样](../../azure-monitor/app/sampling.md)没有采样等），因此您仍然可以使用采样来减少在中间级别下引入的数据量。 但这只是了解其他类似客户看到的情况的一个起点。

## <a name="understand-your-usage-and-estimate-costs"></a>查看自己的使用情况和估算成本

通过 Application Insights 可以根据最近的使用模式轻松估算成本。 若要开始使用，请在 Azure 门户中，转到 Application Insights 资源的“使用情况和估算成本”页****：

![选择定价](./media/pricing/pricing-001.png)

A. 查看当月数据量。 这包括接收和保留的所有数据（在通过服务器和客户端应用以及可用性测试进行[采样](../../azure-monitor/app/sampling.md)后）。  
B. 会单独对[多步骤 Web 测试](../../azure-monitor/app/availability-multistep.md)计费。 （这不包括简单可用性测试，它已包括在数据量费用中。）  
C. 查看上个月的数据量趋势。  
D. 启用数据引入[采样](../../azure-monitor/app/sampling.md)。
E. 设置每日数据量上限。  

（请注意，本文的屏幕截图中显示的所有价格仅用于举例。 有关货币和地区的当前价格，请参阅[Application Insights 定价][pricing]。）

若要更深入地调查 Application Insights 使用情况，请打开“指标”**** 页，添加名为“数据点容量”的指标，然后选择“应用拆分”** 选项以按“遥测项类型”拆分数据。

Application Insights 费用将添加到 Azure 帐单。 可以在 Azure 门户的 "**成本管理 + 计费**" 部分或在[Azure 计费门户](https://account.windowsazure.com/Subscriptions)中查看 azure 账单的详细信息。  有关将此用于 Application Insights 的详细信息，[请参阅下面](https://docs.microsoft.com/azure/azure-monitor/app/pricing#viewing-application-insights-usage-on-your-azure-bill)的详细信息。 

![在左侧菜单中，选择“账单”](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>使用数据量指标
<a id="understanding-ingested-data-volume"></a>

若要了解有关数据量的详细信息，请为 Application Insights 资源选择“指标”****，添加一个新图表。 对于图表指标，请在**基于日志的指标**下，选择“数据点容量”****。 单击“应用拆分”****，然后按“`Telemetryitem` 类型”**** 选择组。

![使用“指标”查看数据量](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>用于了解数据量详细信息的查询

可通过两种方法调查 Application Insights 的数据量。 第一个使用表中的聚合信息 `systemEvents` ，第二个使用 `_BilledSize` 属性，该属性可在每个引入事件上使用。 `systemEvents`对于[基于工作区的应用程序](#data-volume-for-workspace-based-application-insights-resources)，不会有数据大小信息。

#### <a name="using-aggregated-data-volume-information"></a>使用聚合数据卷信息

例如，可以通过查询使用 `systemEvents` 表查看过去 24 小时内引入的数据量：

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

或者，要按数据类型查看最近 30 天的数据量图表（以字节为单位），可以使用：

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

请注意，可以在 [Azure 日志警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)中使用此查询来对数据量设置警报。  

若要了解有关遥测数据更改的详细信息，可以使用查询按类型获取事件计数：

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>对每个事件信息使用数据大小

若要了解有关数据卷源的详细信息，可以使用 `_BilledSize` 每个引入事件上出现的属性。

例如，若要查看最近30天内哪些操作生成的数据量最多，可以 `_BilledSize` 对所有依赖项事件求和：

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

#### <a name="data-volume-for-workspace-based-application-insights-resources"></a>基于工作区的 Application Insights 资源的数据量

若要查看上周工作区中所有[基于工作区的 Application Insights 资源](create-workspace-resource.md)的数据量趋势，请参阅 Log Analytics 工作区并运行查询：

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d) and TimeGenerated < startofday(now())
| summarize sum(_BilledSize) by _ResourceId, bin(TimeGenerated, 1d)
| render areachart
```

若要在 "Log Analytics" 工作区中按类型为特定于工作区的 Application Insights 资源查询数据量趋势，请使用：

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d) and TimeGenerated < startofday(now())
| where _ResourceId contains "<myAppInsightsResourceName>"
| summarize sum(_BilledSize) by Type, bin(TimeGenerated, 1d)
| render areachart
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>查看 Azure 帐单上的 Application Insights 使用情况

Azure 在 [Azure 成本管理 + 计费](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json)中心提供大量有用的功能。 例如，使用“成本分析”功能可以查看 Azure 资源的开支。 按资源类型添加筛选器（添加到 Application Insights 的 microsoft.insights/components）可以跟踪开支。 然后，对于 "分组依据"，选择 "计量类别" 或 "计量器"。  对于当前定价计划 Application Insights 资源，大多数使用情况都将显示为计量器类别的 Log Analytics，因为所有 Azure Monitor 组件都有一个登录后端。 

[从 Azure 门户下载使用情况](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)可以更好地了解使用情况。
在下载的电子表格中，可以看到每天每个 Azure 资源的使用情况。 在此 Excel 电子表格中，可通过以下方式查找 Application Insights 资源的使用情况：先按“计量类别”列进行筛选，以显示“Application Insights”和“Log Analytics”，然后按“实例 ID”列添加一个“包含 microsoft.insights/components”的筛选器。  由于所有 Azure Monitor 组件只有一个日志后端，因此，大多数 Application Insights 使用情况都是根据计量类别为 Log Analytics 的计量表报告的。  只有传统定价层和多步骤 Web 测试中的 Application Insights 资源才使用计量类别 Application Insights 进行报告。  使用情况显示在“使用的数量”列中，每个条目的单位显示在“度量单位”列中。  有更多详细信息可帮助你[了解自己的 Microsoft Azure 帐单](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)。

## <a name="managing-your-data-volume"></a>管理数据量

可使用以下方法管理发送的数据量：

* **采样：** 可以使用采样减少从服务器和客户端应用发送的遥测量，同时最大程度减小指标失真。 采样是你可用来调整发送数据量的主要工具。 了解有关[采样功能](../../azure-monitor/app/sampling.md)的详细信息。

* **限制 ajax 调用**：可以限制每个页面视图中[可报告的 ajax 调用数](../../azure-monitor/app/javascript.md#configuration)，或关闭 ajax 报表。

* **禁用**不需要的模块：[编辑 applicationinsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)以关闭不需要的收集模块。 例如，用户可能认为不再需要性能计数器或依赖项数据。

* **预先聚合的度量值**：如果在应用程序中放置对 TrackMetric 的调用，则可以使用接受计算一批度量值的平均偏差和标准偏差的重载来减少流量。 也可使用[预先聚合包](https://www.myget.org/gallery/applicationinsights-sdk-labs)。
 
* **每日上限：** 在 Azure 门户中创建 Application Insights 资源时，每日上限设置为 100 GB/天。 在 Visual Studio 中创建 Application Insights 资源时，默认值很小（只有 32.3 MB/天）。 设置每日上限默认值，以便进行测试。 可预期用户在将应用部署到生产环境之前，会提高每日上限。 

    除非为高流量应用程序请求了更高的最大值，否则最大上限是 1000 GB/天。
    
    有关每日上限的警告电子邮件将发送到作为 Application Insights 资源的这些角色的成员的帐户： "ServiceAdmin"、"全局"、"CoAdmin"、"Owner"。

    设置每日上限时，务必小心。 你的目标应该是“永远不会达到每日上限”**。 如果达到每日上限，会丢失当天剩余时间的数据，无法监视应用程序。 若要更改每日上限，请使用“每日容量上限”选项****。 可在“使用情况和预估成本”窗格中访问此选项（本文后面会详述）****。
    
    对于具有无法用于 Application Insights 的额度的某些订阅类型，我们已去除此限制。 以前，如果订阅有支出限制，“每日上限”对话框将提供如何去除此限制，并使每日上限提高到超过 32.3 MB/天的说明。
    
* **限制**：此项将数据速率限制为每秒 32,000 个事件，取每个检测密钥 1 分钟的平均值。 每分钟会评估一次应用发送的数据量。 如果超出一分钟内的平均每秒速率，服务器将拒绝某些请求。 SDK 将缓冲数据，然后尝试重新发送该数据。 它会在几分钟内出现传播高峰。 如果应用连续以超出限制的速率发送数据，一些数据会被丢弃。 （ASP.NET、Java 和 JavaScript Sdk 会尝试以这种方式重新发送数据; 其他 Sdk 可能只是删除已限制的数据。）如果发生限制，则会出现一个通知警告，通知你已发生此情况。

## <a name="manage-your-maximum-daily-data-volume"></a>管理每日最大数据量

可以使用每日容量上限，限制所收集的数据。 但是，如果达到上限，会丢失当天剩余时间从应用程序发送的所有遥测数据。 不建议使应用程序达到每日上限**。 达到每日上限后，无法跟踪应用程序的运行状况和性能。

不使用每日容量上限，而是使用[采样](../../azure-monitor/app/sampling.md)，将数据量调整到所需级别。 然后，仅在应用程序意外开始发送远高于预期的遥测量时，才使用每日上限作为“最后的手段”。

### <a name="identify-what-daily-data-limit-to-define"></a>确定要定义的每日数据限制

查看 Application Insights 使用情况和预估成本，了解数据引入趋势，以及要定义的每日数据量上限。 应谨慎考虑，因为在达到限制后，你将无法监视资源。

### <a name="set-the-daily-cap"></a>设置每日上限

若要更改每日上限，请在“使用情况和预估成本”页中 Application Insights 资源的“配置”部分中，选择“每日上限”************。

![调整每日遥测数据量上限](./media/pricing/pricing-003.png)

若要[通过 Azure 资源管理器更改每日上限](../../azure-monitor/app/powershell.md)，需要更改属性 `dailyQuota`。  还可以通过 Azure 资源管理器设置 `dailyQuotaResetTime` 以及每日上限的 `warningThreshold`。

### <a name="create-alerts-for-the-daily-cap"></a>为每日上限创建警报

当引入数据卷达到警告级别或每日上限级别时，Application Insights 每日上限在 Azure 活动日志中创建事件。  您可以[基于这些活动日志事件创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log#create-with-the-azure-portal)。 这些事件的信号名称如下：

* 已达到 Application Insights 组件每日上限警告阈值

* 已达到 Application Insights 组件每日上限

## <a name="sampling"></a>采样
[采样](../../azure-monitor/app/sampling.md)是一种方法，可降低向应用发送遥测的速率，同时仍可在诊断搜索过程中查找相关事件。 此外，还可保留正确的事件计数。

采样是降低费用同时又不超出每月配额的有效方式。 采样算法会保留遥测的相关项，这样，当使用“搜索”时便可查找与特定异常相关的请求。 该算法还保留正确计数，使用户可在指标资源管理器中看到请求率、异常率和其他计数的正确值。

有数种形式的采样。

* [自适应采样](../../azure-monitor/app/sampling.md)是用于 ASP.NET SDK 的默认设置。 自适应采样可自动调整为应用发送的遥测量。 它会在 Web 应用的 SDK 中自动运行，以便减少网络上的遥测流量。 
* *引入采样*是一种替代方法，会在应用的遥测进入 Application Insights 服务时运行。 引入采样不会影响从应用发送的遥测量，但会减少服务保留的量。 可以使用引入采样来降低来自浏览器和其他 SDK 的遥测所使用的配额。

若要设置引入采样，请转到“定价”窗格****：

![选择“配额和定价”窗格中的“示例”磁贴，并选择采样分数](./media/pricing/pricing-004.png)

> [!WARNING]
> “数据采样”窗格仅控制引入采样的值****。 它不反映 Application Insights SDK 在应用中应用的采样速率。 如果传入的遥测已在 SDK 处进行了采样，则引入采样不适用。
>

若要查找实际采样率（无论是否已应用），请使用[分析查询](analytics.md)。 该查询如下所示：

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

在每个保留的记录中，`itemCount` 指示它表示的原始记录数。 它等于 1 +（即以前已放弃的记录数）。

## <a name="change-the-data-retention-period"></a>更改数据保留期

Application Insights 资源的默认保留期为 90 天。 可以为每个 Application Insights 资源选择不同的保留期。 完整的可用保留期集为 30、60、90、120、180、270、365、550 或 730 天。 [详细了解更](https://azure.microsoft.com/pricing/details/monitor/)长的数据保留期。 

若要更改保留期，请从 Application Insights 资源转到“使用情况和预估成本”**** 页，然后选择“数据保留”**** 选项：

![调整每日遥测数据量上限](./media/pricing/pricing-005.png)

如果保留时间降低，则在删除最旧的数据之前，会有几天的宽限期。

还可以使用 PowerShell 通过参数以[编程方式设置](powershell.md#set-the-data-retention)保留 `retentionInDays` 。 如果将数据保留设置为30天，则可以使用参数触发对旧数据的立即清除 `immediatePurgeDataOn30Days` ，这对于符合性相关的方案可能很有用。 此清除功能仅通过 Azure 资源管理器公开，使用时应十分小心。 可以使用 Azure 资源管理器配置数据卷上限的每日重置时间来设置 `dailyQuotaResetTime` 参数。

## <a name="data-transfer-charges-using-application-insights"></a>使用 Application Insights 时的数据传输费

向 Application Insights 发送数据可能会产生数据带宽费。 如 [Azure 带宽定价页](https://azure.microsoft.com/pricing/details/bandwidth/)中所述，在两个区域中的 Azure 服务之间传输数据将按正常费率收取出站数据传输费。 入站数据传输是免费的。 但是，相比 Application Insights 日志数据引入费，此传输费很低（只占几个百分比）。 因此，控制 Log Analytics 的成本需要注重引入的数据量，[此处](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume)提供了相关的指导。

## <a name="limits-summary"></a>限制摘要

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>禁用每日上限电子邮件

若要禁用每日容量上限电子邮件，请在“使用情况和预估成本”窗格中 Application Insights 资源的“配置”部分下，选择“每日上限”************。 有一些在达到上限时以及达到可调整的警告等级时发送电子邮件的设置。 如果要禁用所有每日 cap 相关电子邮件，请取消选中这两个框。

## <a name="legacy-enterprise-per-node-pricing-tier"></a>旧的企业（按节点）定价层

对于 Azure 应用程序 Insights 的早期采用者，仍有两个可能的定价层： "基本" 和 "企业"。 “基本”定价层如上所述，它是默认层。 其中包括所有的“企业”层功能，而不另外收费。 “基本”层主要针对所引入的数据量计费。

> [!NOTE]
> 这些旧的定价层已重命名。 “企业”定价层现在称为“按节点”**** 层，而“基本”定价层现在称为“按 GB”**** 层。 这些新名称将在下文和 Azure 门户中使用。  

“按节点”（以前是“企业”）层按节点收费，并且每个节点都会收到每日数据限额。 在“按节点”定价层中，你需要为所引入的数据中超出规定限额的数据付费。 如果使用的是 Operations Management Suite，则应选择“按节点”层。

有关在你所在区域中以适合你的货币计价的当前价格，请参阅 [Application Insights 定价](https://azure.microsoft.com/pricing/details/application-insights/)

> [!NOTE]
> 在 2018 年 4 月，我们[引入了](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) Azure 监视的新定价模型。 此模型在监视服务的完整组合中采用简单的“即用即付”模型。 深入了解新的[定价模型](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs)，如何根据使用模式[评估移到此模型的影响](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#understanding-your-azure-monitor-costs)以及[如何选择加入新模型](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#azure-monitor-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>“按节点”层和 Operations Management Suite 订阅权利

按照[以前的公告](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/)，客户如果购买了 Operations Management Suite E1 和 E2，则能够免费获取 Application Insights 的“按节点”作为附加组件。 具体而言，Operations Management Suite E1 和 E2 的每个单位都包含对 Application Insights“按节点”层的 1 个节点的权利。 每个 Application Insights 节点包含每天最多 200 MB 的引入数据（独立于 Log Analytics 数据引入），并且数据会保留 90 天，无额外费用。 该层将在本文的后面进行更详细的介绍。

由于此层仅适用于具有 Operations Management Suite 订阅的客户，因此，没有 Operations Management Suite 订阅的客户看不到用于选择此层的选项。

> [!NOTE]
> 若要确保你获取此权利，Application Insights 资源必须位于“按节点”定价层中。 此权利仅以节点的形式应用。 “按 GB”层中的 Application Insights 资源不会实现任何权益。 此权利不会出现在“使用情况和预估成本”窗格中显示的预估成本中****。 此外，如果在 2018 年 4 月将订阅移到了新的 Azure 监视定价模型，则只有“按 GB”层可用。 如果具有 Operations Management Suite 订阅，不建议将订阅移到新的 Azure 监视定价模型。

### <a name="how-the-per-node-tier-works"></a>“按节点”层的工作原理

* 你需要针对为“按节点”层中的任何应用发送遥测数据的每个节点付费。
  * *节点*是托管应用的物理或虚拟服务器计算机或平台即服务角色实例。
  * 开发计算机、客户端浏览器和移动设备不计为节点。
  * 如果应用有多个组件（例如 Web 服务和后端辅助角色）发送遥测数据，则会对组件分开计数。
  * 定价未考虑[实时指标流](../../azure-monitor/app/live-stream.md)数据。 在订阅中，将按节点而非应用计费。 如果有 5 个节点在为 12 个应用发送遥测数据，则按 5 个节点计费。
* 虽然是按月计费，但对于从应用发送遥测数据的节点来说，实际上是按小时计费。 小时费率为月费除以 744（每月的小时数，每月按 31 天算）。
* 每天为每个检测到的节点分配的数据量为 200 MB（时间粒度为小时）。 分配的数据如果未使用，不会从当天转到第二天。
  * 如果你选择了“按节点”定价层，则每个订阅都会收到基于向该订阅中的 Application Insights 资源发送遥测数据的节点数的每日数据限额。 因此，如果全天有 5 个节点在发送数据，则该订阅中的所有 Application Insights 资源可以共用 1 GB 的数据限额。 无所谓某些节点是否比其他节点发送更多数据，因为包含的数据在所有节点之间共享。 如果 Application Insights 资源在给定的某天收到的数据量超出了为此订阅分配的每日数据限额，则会按 GB 对超额数据收费。 
  * 每日数据限额的计算方式为所有节点在一天中发送遥测数据的小时数（使用 UTC 计时方式）除以 24 再乘以 200 MB。 因此，如果有 4 个节点一天 24 小时中有 15 个小时在发送遥测，则当天包含的数据为 ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB。 超额数据的价格为 2.30 美元/GB，如果当天发送了 1 GB 的数据，则收费 1.15 美元。
  * “按节点”层的每日限额不与已为其选择“每 GB”层的应用程序共享。 未使用的限额不能从当天转到第二天。

### <a name="examples-of-how-to-determine-distinct-node-count"></a>演示如何确定不同节点计数的示例

| 方案                               | 每日节点计数总数 |
|:---------------------------------------|:----------------:|
| 1 个应用程序使用 3 个 Azure 应用服务实例和 1 个虚拟服务器 | 4 |
| 3 个应用程序正运行在 2 个 VM 上，这些应用程序的 Application Insights 资源属于同一订阅，并且位于“按节点”层中 | 2 | 
| 4 个应用程序的 Applications Insights 资源属于同一订阅；每个应用程序在 16 小时的非高峰时段内运行 2 个实例，在 8 小时的高峰时段内运行 4 个实例 | 13.33 |
| 云服务有 1 个辅助角色和 1 个 Web 角色，每个角色运行 2 个实例 | 4 | 
| 5 节点 Azure Service Fabric 群集运行 50 个微服务；每个微服务运行 3 个实例 | 5|

* 若要进行精确的节点计数，必须了解应用程序在使用哪个 Application Insights SDK。 
  * 在 SDK 2.2 及更高版本中，Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 或 [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 都会将每个应用程序主机作为节点进行报告。 例如，会报告物理服务器和 VM 主机的计算机名称，而对于云服务，则报告实例名称。  唯一的例外是应用程序仅使用 [.NET Core](https://dotnet.github.io/) 和 Application Insights Core SDK。 在这种情况下，所有主机只会报告一个节点，因为主机名不可用。
  * 就早期版本的 SDK 来说，[Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 的行为与新版 SDK 并无二致，而 [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 则只会报告一个节点，不管应用程序主机的数目是多少。
  * 如果应用程序通过 SDK 将 **roleInstance** 设置为自定义值，则会默认使用该值确定节点计数。
  * 如果对从客户端计算机或移动设备运行的应用使用新版 SDK，则节点计数可能会返回很大的数目（因为客户端计算机或移动设备数目很大）。

## <a name="automation"></a>自动化

可使用 Azure 资源管理编写脚本来设置定价层。 [了解操作方法](powershell.md#price)。

## <a name="next-steps"></a>后续步骤

* [采样](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/