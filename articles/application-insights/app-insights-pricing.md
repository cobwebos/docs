---
title: 管理 Azure Application Insights 的定价和数据量 | Microsoft Docs
description: 在 Application Insights 中管理遥测量并监视成本。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 42c54509cedf48e9ebabb5b50865ed56e54bee05
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>在 Application Insights 中管理定价和数据量

[Azure Application Insights][start] 的定价以每个应用程序的数据量为基础。 每个 Application Insights 资源作为独立服务计费，并在 Azure 订阅的账单中产生相应费用。

目前有两种定价计划。 默认的计划称为“基本”，其中包括与企业计划相同的所有功能，对于引入的数据量基本没有额外的成本和费用。 如果使用 Operations Management Suite，则应当选择企业计划，它具有每节点费用和每日数据额度，将对超出了所包括额度的引入数据收取费用。

如果对 Application Insights 的定价方式存在疑问，欢迎在我们的[论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights)提出问题。

## <a name="the-price-plans"></a>价格计划

有关在你所在区域中以适合你的货币计价的当前价格，请参阅 [Application Insights 定价页][pricing]。

### <a name="basic-plan"></a>基本计划

创建新的 Application Insights 资源时，基本计划是默认的计划，也是满足大多数客户要求的最佳计划，使用 Operations Management Suite 订阅的客户除外。

* 在基本计划中，将按数据量（Application Insights 收到的遥测数据的字节数）对用户收费。 数据量的衡量标准是 Application Insights 从用户应用程序收到的未压缩 JSON 数据包的大小。
对于[导入到 Analytics 中的表格数据](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import)，按发送到 Application Insights 的文件的未压缩大小来度量数据量。
* 定价未考虑[实时指标流](app-insights-live-stream.md)数据。
* [连续导出](app-insights-export-telemetry.md)和 [Log Analytics 连接器](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409)已推出，在 2018 年 4 月之前的基本计划中不额外收费。

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>企业计划和 Operations Management Suite 订阅权利

按照[最近的公告](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/)，购买 Microsoft Operations Management Suite E1 和 E2 的客户能够以附加组件的形式获取 Application Insights 企业版，而没有额外费用。 具体而言，Operations Management Suite E1 和 E2 的每个单位都包含对 Application Insights 企业计划的 1 个节点的权利。 每个 Application Insights 节点包含每天最多 200 MB 的引入数据（独立于 Log Analytics 数据引入），并且数据会保留 90 天，无额外费用，请参阅下面的详述。 由于此服务仅适用于使用 Operations Management Suite 订阅的客户，没有订阅的客户将看不到用于选择此计划的选项。

> [!NOTE]
> 若要确保获取此权利，必须在企业定价计划中具有 Application Insights 资源。 此权利仅作为节点进行应用，因此基本计划中的 Application Insights 资源不会实现任何优势。 请注意，此权利不会出现在“使用情况和预估成本”页上显示的预估成本中。 此外，如果将订阅移到 2018 年 4 月新发布的 Azure 监视定价模型中，则基本计划是唯一可用的计划，因此，如果你使用 Operations Management Suite 订阅，则我们不建议此选项。

有关企业计划的详细信息，请访问[企业定价详细信息页。](app-insights-pricing-enterprise-details.md)

### <a name="multi-step-web-tests"></a>多步骤 Web 测试

[多步骤 Web 测试](app-insights-monitor-web-app-availability.md#multi-step-web-tests)会额外收费。 此类测试是指执行一系列操作的 Web 测试。

单页“ping 测试”不单独计费。 进行 ping 测试和多步测试时发送的遥测数据将与应用发送的其他遥测数据一起计费。

## <a name="review-pricing-plans-and-estimate-costs"></a>检查定价计划和估计成本

通过 Application Insights 可以轻松了解可用的定价计划以及基于最近使用模式的可能成本。 首先在 Azure 门户的 Application Insights 资源中打开“使用情况和预估成本”页：

![选择“定价”。](./media/app-insights-pricing/pricing-001.png)

**a.** 查看当月数据量。 这包括接收和保留的所有数据（在通过服务器和客户端应用以及可用性测试进行[采样](app-insights-sampling.md)后）。

**b.** 会单独对[多步骤 Web 测试](app-insights-monitor-web-app-availability.md#multi-step-web-tests)计费。 （这不包括简单可用性测试，它已包括在数据量费用中。）

**c.** 查看上个月的数据量趋势。

**d.** 启用数据引入[采样](app-insights-sampling.md)。 

**e.** 配置每日数据量上限。

Application Insights 费用将添加到 Azure 帐单。 可以在 Azure 门户的“计费”部分或在 [Azure Billing Portal](https://account.windowsazure.com/Subscriptions)（Azure 计费门户）查看 Azure 账单的详细信息。 

![在侧边菜单上，选择“计费”。](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>数据速率
发送数据时存在三种数量限值：

* **采样：**可以使用此机制减少从服务器和客户端应用发送的遥测量，同时最大程度减小指标失真。 这是你拥有的调整数据量的主要工具。 了解有关[采样功能](app-insights-sampling.md)的详细信息。 
* **每日上限：**从 Azure 门户创建 Application Insights 资源时，此项设置为 100 GB/天。 从 Visual Studio 创建 Application Insights 资源时，默认值很小（只有 32.3 MB/天），仅为了便于测试。 在这种情况下，可预期用户在将应用部署到生产环境之前，会提高每日上限。 除非为高流量应用程序请求了更高的最大值，否则最大上限是 1000 GB/天。 设置每日上限时要格外小心，因为意图应是**永远不达到每日上限**，因为达到了会在当天的剩余时间内丢失数据，并且无法监视应用程序。 若要更改它，请使用从“使用情况和预估成本”页链接的“每日数据量上限”选项（参阅下文）。 对于具有无法用于 Application Insights 的信用额度的某些订阅类型，我们已去除此限制。 以前，如果订阅有支出限制，“每日上限”对话框将提供如何去除此限制，并使每日上限提高到超过 32.3 MB/天的说明。
* **限制**：此项将数据速率限制为每秒 32,000 个事件，取 1 分钟的平均值。

*如果应用超过限制速率，会发生什么情况？*

* 每分钟会评估一次应用发送的数据量。 如果超出一分钟内的平均每秒速率，服务器将拒绝某些请求。 SDK 会缓冲数据，并尝试重新发送，导致数分钟内数据传输量激增。 如果应用连续以超出限制的速率发送数据，一些数据会被丢弃。 （ASP.NET、Java 和 JavaScript Sdk 会尝试以这种方式重新发送；其他 SDK 可能会丢弃超出限制的数据。）如果发生超出限制的情况，会显示一个通知，警告发生了这种情况。

*如何知道我的应用正在发送的数据量？*

* 打开“使用情况和预估成本”页查看每日数据量图表。 
* 或在“指标资源管理器”中，添加新图表并选择“数据点量”作为其指标。 启用“分组”，并按**数据类型**分组。

## <a name="to-reduce-your-data-rate"></a>降低数据速率
可通过以下操作降低数据量：

* 使用[采样](app-insights-sampling.md)。 此技术可减少数据速率，同时不会影响指标的准确性，也不会中断在“搜索”中的相关项目之间导航的能力。 在服务器应用中，它会自动运行。
* [限制可在每个页面视图中报告的 Ajax 调用数](app-insights-javascript.md#detailed-configuration)或关闭 Ajax 报告。
* 通过[编辑 ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 关闭不需要的集合模块。例如，用户可能认为不再需要性能计数器或依赖项数据。
* 将遥测拆分到单独的检测密钥。 
* 预先聚合指标。 如果已将对 TrackMetric 的调用放在应用中，则可通过使用重载降低流量，这种重载接受对一批度量值的平均偏差和标准偏差的计算结果。 也可使用 [预先聚合包](https://www.myget.org/gallery/applicationinsights-sdk-labs)。

## <a name="managing-the-maximum-daily-data-volume"></a>管理每日最大数据量

可使用每日数据上限来限制收集的数据量，但如果达到上限，会导致当天剩余时间内从应用程序发送的所有遥测丢失。 **不推荐**让应用程序达到每日上限，因为达到每日上限后将无法跟踪应用程序运行状况和性能。

相反，请使用[采样](app-insights-sampling.md)将数据量调整到所需级别，将每日上限仅用作防止应用程序意外发送过多遥测数据量的“最后手段”。

若要更改每日上限，请在“使用情况和预估成本”页中 Application Insights 资源的“配置”部分，单击“每日上限”。

![调整每日遥测数据量上限](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>采样
[采样](app-insights-sampling.md)是一种方法，可降低向应用发送遥测的速率，同时仍可在诊断搜索过程中查找相关事件，并保留正确的事件计数。

采样是降低费用同时又不超出每月配额的有效方式。 采样算法会保留遥测的相关项，这样，当使用“搜索”时便可查找与特定异常相关的请求。 该算法还保留正确计数，使用户可在指标资源管理器中看到请求率、异常率和其他计数的正确值。

有数种形式的采样。

* [自适应采样](app-insights-sampling.md)是用于 ASP.NET SDK 时的默认设置，它可自动调整为应用发送的遥测量。 它会在 Web 应用的 SDK 中自动运行，以便减少网络上的遥测流量。 
* *引入采样*是一种替代方法，会在应用的遥测进入 Application Insights 服务时运行。 这种形式的采样不会影响从应用发送的遥测量，但会减少服务保留的量。 可以使用这种采样来降低来自浏览器和其他 SDK 的遥测所使用的配额。

若要设置引入采样，请在“定价”对话框中设置控制措施：

![单击“配额和定价”对话框中的“示例”磁贴，并选择采样分数。](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> “数据采样”对话框仅控制引入采样的值。 它不反映 Application Insights SDK 在应用中应用的采样速率。 如果传入的遥测已在 SDK 处进行了采样，则不适用引入采样。
>

若要查找实际采样率（无论是否已应用），请使用如下所示的[分析查询](app-insights-analytics.md)：

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

在每个保留的记录中，`itemCount` 指示它表示的原始记录数，其等于 1 +（即以前已放弃的记录数）。 

## <a name="automation"></a>自动化

可使用 Azure 资源管理编写脚本进行价格计划设置。 [了解操作方法](app-insights-powershell.md#price)。

## <a name="limits-summary"></a>限制摘要

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>后续步骤

* [采样](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
