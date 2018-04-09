---
title: Azure Application Insights 的旧式企业定价计划详细信息 | Microsoft Docs
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
ms.openlocfilehash: 6e7591ccf0f21099474a08dda088422c377135f6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="enterprise-plan-details"></a>企业计划详细信息

Application Insights 有两个定价计划。 默认的计划称为[基本](app-insights-pricing.md)，其中包括与企业计划相同的所有功能，对于引入的数据量基本没有额外的成本和费用。 如果使用 Operations Management Suite，则应当选择企业计划，它具有每节点费用和每日数据额度，将对超出了所包括额度的引入数据收取费用。

有关在你所在区域中以适合你的货币计价的当前价格，请参阅 [Application Insights 定价页](http://azure.microsoft.com/pricing/details/application-insights/)。

## <a name="heres-how-the-enterprise-plan-works"></a>以下是企业计划的工作情况

* 将按在企业计划中为任何应用发送遥测数据的节点收费。
 * *节点* 是托管应用的物理/虚拟服务器计算机或平台即服务角色实例。
 * 开发计算机、客户端浏览器和移动设备不计为节点。
 * 如果应用有多个组件（例如 Web 服务和后端辅助角色）发送遥测数据，则会对其分开计数。
 * 定价未考虑[实时指标流](app-insights-live-stream.md)数据。在订阅中，将按节点而非应用计费。 如果有 5 个节点在为 12 个应用发送遥测数据，则按 5 个节点计费。
* 虽然是按月计费，但对于从应用发送遥测数据的节点来说，实际上是按小时计费。 小时费率为月费/744（每月的小时数，每月按 31 天算）。
* 每天为每个检测到的节点分配的数据量为 200 MB（时间粒度为小时）。 分配的数据如果未使用，不会从当天转到第二天。
 * 如果选择“企业”定价选项，会根据该订阅中向 Application Insights 资源发送遥测数据的节点数为每个订阅提供每日数据限额。 因此，如果全天有 5 个节点在发送数据，则该订阅中的所有 Application Insights 资源可以共用 1 GB 的数据限额。 无所谓某些节点是否比其他节点发送更多数据，因为所有节点发送的数据都计入同一个数据限额。 如果 Application Insights 资源在特定的某一天收到的数据量超出了为该订阅分配的每日数据限额，则会对超额数据按 GB 计费。 
 * 每日数据限额的计算方式为所有节点在一天中发送遥测数据的小时数（使用 UTC 计时方式）除以 24 再乘以 200 MB。 因此，如果有 4 个节点在一天 24 小时的 15 小时内发送遥测数据，则当天的数据限额为 ((4 x 15) / 24) x 200 MB = 500 MB。 超额数据的价格为 2.30 美元/GB，如果当天发送了 1 GB 的数据，则收费 1.15 美元。
 * 企业计划的每日限额不能与选择了“基本”选项的应用程序共享，未使用的限额不能从当天转到第二天。 

## <a name="here-are-some-examples-of-determining-distinct-node-count"></a>下面是一些示例，说明了如何确定不同的节点计数

| 场景                               | 每日节点计数总数 |
|:---------------------------------------|:----------------:|
| 1 个应用程序使用 3 个 Azure 应用服务实例和 1 个虚拟服务器 | 4 |
| 3 个应用程序运行在 2 个 VM 上，这些应用程序的 Application Insights 资源属于同一订阅，所用计划为企业计划 | 2 | 
| 4 个应用程序的 Applications Insights 资源属于同一订阅。 每个应用程序在 16 小时的非高峰时段内运行 2 个实例，在 8 小时的高峰时段内运行 4 个实例。 | 13.33 | 
| 云服务有 1 个辅助角色和 1 个 Web 角色，每个角色运行 2 个实例 | 4 | 
| 5 节点型 Service Fabric 群集运行 50 个微服务，每个微服务运行 3 个实例 | 5|

* 若要进行精确的节点计数，必须了解应用程序在使用哪个 Application Insights SDK。 
  * 在 SDK 2.2 及更高版本中，Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 或 [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 都会将每个应用程序主机作为节点进行报告，例如会报告物理服务器和 VM 主机的计算机名称，而在使用云服务时，则会报告实例名称。  唯一的例外是应用程序仅使用 [.NET Core](https://dotnet.github.io/) 和 Application Insights Core SDK，这种情况下，所有主机只会报告一个节点，因为主机名不可用。 
  * 就早期版本的 SDK 来说，[Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 的行为与新版 SDK 并无二致，而 [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 则只会报告一个节点，不管实际应用程序主机的数目是多少。 
  * 如果应用程序通过 SDK 将 roleInstance 设置为自定义值，则会默认使用该值确定节点计数。 
  * 如果对应用使用新版 SDK，而该应用在客户端计算机或移动设备中运行，则节点计数可能会返回很大的数目（在客户端计算机或移动设备数目很大的情况下）。 
