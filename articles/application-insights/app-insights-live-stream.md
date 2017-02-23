---
title: "Azure Application Insights 中的实时指标流 | Microsoft Docs"
description: "实时监控 Web 应用的性能，以观察释放或其他更改的影响。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: c453774b04c7005ce5948617beea8770b499b88d
ms.openlocfilehash: 2da6d02055616db5f9854481054ff12c69dc4801


---

# <a name="live-metrics-stream-instant-metrics-for-close-monitoring"></a>实时指标流：适用于关闭监视的实时指标
实时指标流说明 [Application Insights](app-insights-overview.md) 指标正处于此非常时刻，接近实时的延迟为&1; 秒。 发布新版本并想要确保一切按预期运行，或者要实时调查某个事件时，此实时监视非常有用。

![在“概览”边栏选项卡中单击“实时流”](./media/app-insights-live-stream/live-stream.png)

不同于[指标资源管理器](app-insights-metrics-explorer.md)，实时指标流显示一组固定指标。 数据只会在图表上显示时才会得到保留，不显示时将被丢弃。

实时指标流数据是免费的：不会计入帐单中。

## <a name="live-failures"></a>实时失败

如果记录了任何失败或异常，实时流将选出这些信息的一个样本。 单击“暂停”可以暂停特定的样本，选择一个事件可显示其详细信息。

![采样的实时失败](./media/app-insights-live-stream/live-stream-failures.png)


实时指标流适用于最新版本的 [Application Insights SDK for Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)。



## <a name="troubleshooting"></a>故障排除

没有数据？ 实时指标流使用与其他 Application Insights 遥测不同的端口。 确保[这些端口](app-insights-ip-addresses.md)已在防火墙中打开。



## <a name="next-steps"></a>后续步骤
* [使用 Application Insights 监视使用情况](app-insights-overview-usage.md)
* [使用诊断搜索](app-insights-diagnostic-search.md)




<!--HONumber=Feb17_HO2-->


