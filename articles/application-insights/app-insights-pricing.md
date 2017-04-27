---
title: "管理 Azure Application Insights 的定价和数据量 | Microsoft Docs"
description: "在 Application Insights 中管理遥测量并监视成本。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 229dd21f3ab1ae716cd49611e720450ae5939eb8
ms.lasthandoff: 03/31/2017


---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>在 Application Insights 中管理定价和数据量


[Azure Application Insights][start] 的定价以每个应用程序的数据量为基础。 如果开发期间的应用使用率或小型应用的使用率较低，则可能不会产生费用，因为每月会提供 1 GB 的遥测数据限额。

每个 Application Insights 资源作为独立服务计费，并在 Azure 订阅的账单中产生相应费用。

目前有两种定价计划。 默认的计划称为基本定价计划。 可以选择按日计费的企业计划，但会启用某些其他功能，例如[连续导出](app-insights-export-telemetry.md)。

如果对 Application Insights 的定价方式存在疑问，欢迎在我们的[论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights)提出问题。 

## <a name="the-price-plans"></a>价格计划

如需当前价格（你所使用的货币），请参阅 [Application Insights 定价页][pricing]。

### <a name="basic-plan"></a>基本计划

创建新的 Application Insights 资源时，基本计划是默认的计划，满足大多数客户的要求。

* 在基本计划中，将按数据量（Application Insights 收到的遥测数据的字节数）对用户收费。 数据量的衡量标准是 Application Insights 从用户应用程序收到的未压缩 JSON 数据包的大小。
* 每个应用的头 1 GB 免费，因此，如果只是进行试验或开发，可能不需付费。
* 定价未考虑[实时指标流](app-insights-live-stream.md)数据。
* 已推出[连续导出](app-insights-export-telemetry.md)，在使用基本计划时需额外付费（按 GB），但在 2017 年 3 月初之前仍属免费。

### <a name="enterprise-plan"></a>企业计划

* 在企业计划中，应用可以使用 Application Insights 的所有功能。 [连续导出](app-insights-export-telemetry.md)和 [Log Analytics 连接器](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409)已推出，在使用企业计划时不额外收费。
* 将按在企业计划中为任何应用发送遥测数据的节点收费。 
 * *节点* 是托管应用的物理/虚拟服务器计算机或平台即服务角色实例。
 * 开发计算机、客户端浏览器和移动设备不计为节点。
 * 如果应用有多个组件（例如 Web 服务和后端辅助角色）发送遥测数据，则会对其分开计数。
 * 定价未考虑[实时指标流](app-insights-live-stream.md)数据。
* 在订阅中，将按节点而非应用计费。 如果有 5 个节点在为 12 个应用发送遥测数据，则按 5 个节点计费。
* 虽然是按月计费，但对于从应用发送遥测数据的节点来说，实际上是按小时计费。 小时费率为月费/744（每月的小时数，每月按 31 天算）。
* 每天为每个检测到的节点分配的数据量为 200 MB（时间粒度为小时）。 分配的数据如果未使用，不会从当天转到第二天。
 * 如果选择“企业”定价选项，将根据该订阅中向 Application Insights 资源发送遥测数据的节点数为每个订阅提供每日数据限额。 因此，如果全天有 5 个节点在发送数据，则该订阅中的所有 Application Insights 资源可以共用 1 GB 的数据限额。 无所谓某些节点是否比其他节点发送更多数据，因为所有节点发送的数据都计入同一个数据限额。 如果 Application Insights 资源在特定的某一天收到的数据量超出了为该订阅分配的每日数据限额，则会对超额数据按 GB 计费。 
 * 每日数据限额的计算方式为所有节点在一天中发送遥测数据的小时数（使用 UTC 计时方式）除以 24 再乘以 200 MB。 因此，如果有 4 个节点在一天 24 小时的 15 小时内发送遥测数据，则当天的数据限额为 ((4 x 15) / 24) x 200 MB = 500 MB。 超额数据的价格为 2.30 美元/GB，如果当天发送了 1 GB 的数据，则收费 1.15 美元。
 * 请注意，企业计划的每日限额不能与选择了“基本”选项的应用程序共享，未使用的限额不能从当天转到第二天。 
* 下面是一些示例，说明了如何确定不同的节点计数：
| 方案                               | 每日节点计数总数 |
|:---------------------------------------|:----------------:|
| 1 个应用程序使用 3 个 Azure 应用服务实例和 1 个虚拟服务器 | 4 |
| 3 个应用程序运行在 2 个 VM 上，这些应用程序的 Application Insights 资源属于同一订阅，所用计划为企业计划 | 2 | 
| 4 个应用程序的 Applications Insights 资源属于同一订阅。 每个应用程序在 16 小时的非高峰时段内运行 2 个实例，在 8 小时的高峰时段内运行 4 个实例。 | 13.33 | 
| 云服务有 1 个辅助角色和 1 个 Web 角色，每个角色运行 2 个实例 | 4 | 
| 5 节点型 Service Fabric 群集运行 50 个微服务，每个微服务运行 3 个实例 | 5|

* 若要进行精确的节点计数，必须了解应用程序在使用哪个 Application Insights SDK。 
  * 在 SDK 2.2 及更高版本中，Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 或 [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 都会将每个应用程序主机作为节点进行报告，例如会报告物理服务器和 VM 主机的计算机名称，而在使用云服务时，则会报告实例名称。  唯一的例外是应用程序仅使用 [.NET Core](https://dotnet.github.io/) 和 Application Insights Core SDK，这种情况下，所有主机只会报告一个节点，因为主机名不可用。 
  * 就早期版本的 SDK 来说，[Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 的行为与新版 SDK 并无二致，而 [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 则只会报告一个节点，不管实际应用程序主机的数目是多少。 
  * 请注意，如果应用程序通过 SDK 将 roleInstance 设置为自定义值，则会默认使用该值确定节点计数。 
  * 如果对应用使用新版 SDK，而该应用在客户端计算机或移动设备中运行，则节点计数可能会返回很大的数目（在客户端计算机或移动设备数目很大的情况下）。 

### <a name="multi-step-web-tests"></a>多步骤 Web 测试

[多步骤 Web 测试](app-insights-monitor-web-app-availability.md#multi-step-web-tests)会额外收费。 此类测试是指执行一系列操作的 Web 测试。 

单页“ping 测试”不单独计费。 进行 ping 测试和多步测试时发送的遥测数据将与应用发送的其他遥测数据一起计费。

## <a name="review-pricing-plan-and-estimate-costs-for-your-application-insights-resource"></a>查看 Application Insights 资源的定价计划并进行费用估算
在应用程序的 Application Insights 资源中打开“功能 + 定价”边栏选项卡。

![选择“定价”。](./media/app-insights-pricing/01-pricing.png)

**a.** 查看当月数据量。 这包括接收和保留的所有数据（在通过服务器和客户端应用以及可用性测试进行[采样](app-insights-sampling.md)后）。

**b.** 会单独对[多步骤 Web 测试](app-insights-monitor-web-app-availability.md#multi-step-web-tests)计费。 （这不包括简单可用性测试，它已包括在数据量费用中。）

**c.** 启用企业计划。

**d.** 单击数据管理选项查看上月的数据量、设置每日上限或设置引入采样。

Application Insights 费用将添加到 Azure 帐单。 可以在 Azure 门户的“计费”部分或在 [Azure Billing Portal](https://account.windowsazure.com/Subscriptions)（Azure 计费门户）查看 Azure 账单的详细信息。 

![在侧边菜单上，选择“计费”。](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>数据速率
发送数据时存在三种数量限值：

* **每日上限。** 最大上限为 500 GB/天。 使用 Visual Studio 创建 Application Insights 资源时，默认值很小（只有 32.3 MB/天）。 使用 Azure 门户创建 Application Insights 资源时，该值会设置为最大。 更改时请务必小心，因为达到上限将导致你在一天的剩余时间中丢失数据。 若要更改它，请使用从数据量管理边栏选项卡链接的每日数据量上限。
* **[采样](app-insights-sampling.md)。** 此机制可减少从服务器和客户端应用发送的遥测量，同时最大程度减小指标失真。
* **限制**：数据速率上限为每秒 32000 个事件，取 1 分钟的平均值。 


*如果应用超过限制速率，会发生什么情况？*

* 每分钟会评估一次应用发送的数据量。 如果超出一分钟内的平均每秒速率，服务器将拒绝某些请求。 SDK 会缓冲数据，然后尝试重新发送，导致数分钟内数据传输量激增。 如果应用连续以超出限制的速率发送数据，一些数据将被丢弃。 （ASP.NET、Java 和 JavaScript Sdk 会尝试以这种方式重新发送；其他 SDK 可能会丢弃超出限制的数据。）

如果发生超出限制的情况，将会显示一个通知，警告发生了这种情况。

*如何知晓应用发送了多少数据点？*

* 打开“定价”边栏选项卡，查看“数据量”图表。
* 或在“指标资源管理器”中，添加新图表并选择“数据点量”作为其指标。 启用“分组”，并按**数据类型**分组。

## <a name="to-reduce-your-data-rate"></a>降低数据速率
可通过以下操作降低数据量：

* 使用[采样](app-insights-sampling.md)。 此技术可减少数据速率，同时不会影响指标的准确性，也不会中断在“搜索”中的相关项目之间导航的能力。 在服务器应用中，它会自动运行。
* [限制可在每个页面视图中报告的 Ajax 调用数](app-insights-javascript.md#detailed-configuration)或关闭 Ajax 报告。
* 通过[编辑 ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 关闭不需要的集合模块。 例如，用户可能认为不再需要性能计数器或依赖项数据。
* 将遥测拆分到单独的检测密钥。 
* 预先聚合指标。 如果已将对 TrackMetric 的调用放在应用中，则可通过使用重载降低流量，这种重载接受对一批度量值的平均偏差和标准偏差的计算结果。 也可使用 [预先聚合包](https://www.myget.org/gallery/applicationinsights-sdk-labs)。

## <a name="managing-the-maximum-daily-data-volume"></a>管理每日最大数据量

可使用每日数据上限来限制收集的数据量，但如果达到上限，会导致当天剩余时间从应用程序发送的所有遥测丢失。 **不推荐**让应用程序达到每日上限，因为达到每日上限后将无法跟踪应用程序运行状况和性能。 

相反，请使用[采样](app-insights-sampling.md)将数据量调整到所需级别，将每日上限仅用作防止应用程序意外发送过多遥测数据量的“最后手段”。 

若要更改每日上限，请打开“功能+定价”>“数据管理”。

![调整每日遥测数据量上限](./media/app-insights-pricing/daily-cap.png) 

## <a name="sampling"></a>采样
[采样](app-insights-sampling.md)是一种方法，可降低向应用发送遥测的速率，同时仍可在诊断搜索过程中查找相关事件，并保留正确的事件计数。 

采样是降低费用同时又不超出每月配额的有效方式。 采样算法会保留遥测的相关项，这样，当使用“搜索”时便可查找与特定异常相关的请求。 该算法还保留正确计数，使用户可在指标资源管理器中看到请求率、异常率和其他计数的正确值。

有数种形式的采样。

* [自适应采样](app-insights-sampling.md)是用于 ASP.NET SDK 时的默认设置，它可自动调整为应用发送的遥测量。 它会在 Web 应用的 SDK 中自动运行，以便减少网络上的遥测流量。 
* *引入采样*是一种替代方法，会在应用的遥测进入 Application Insights 服务时运行。 这种形式的采样不会影响从应用发送的遥测量，但会减少服务保留的量。 可以使用这种采样来降低来自浏览器和其他 SDK 的遥测所使用的配额。

若要设置引入采样，请在定价边栏选项卡中设置控件：

![单击“配额和定价”边栏选项卡中的“示例”磁贴，然后选择采样分数。](./media/app-insights-pricing/04.png)

> [!WARNING]
> “保留的样本”磁贴上显示的值仅指示为引入采样设置的值。 它不会显示应用中 SDK 处运行的采样率。 
> 
> 如果传入的遥测已在 SDK 处进行了采样，则不适用引入采样。
> 
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

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/


