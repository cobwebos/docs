---
title: 'Application Insights: 语言、平台和集成 |Microsoft Docs'
description: 适用于 Application Insights 的语言、平台和集成
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/06/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: db79f2b1d3857aac2f71d2e18a3949f068b746eb
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67990061"
---
# <a name="languages---officially-supported-by-application-insights-team"></a>语言-Application Insights 团队正式支持

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript 网页](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="community-sdks"></a>社区 Sdk

有很多社区 Azure 应用程序见解 Sdk, 其中许多都是由 Microsoft 最初创作的。 社区 Sdk 并不由 Microsoft 正式维护。 我们无法为不在正式支持的列表中的任何 SDK 提供支持。 这些 Sdk 被视为实验性, 不建议用于生产。

## <a name="platforms-and-frameworks"></a>平台和框架
### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>已部署的应用程序 (无代码置备、基于代理) 的检测
* [Azure VM 和 Azure 虚拟机规模集](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure 应用服务](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET - 适用于已处于活动状态的应用](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure 云服务](../../azure-monitor/app/cloudservices.md)(包括 web 角色和辅助角色)
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>通过代码进行检测 (Sdk)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [通用 Windows 应用](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Windows 桌面应用程序、服务和辅助角色](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>记录框架
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net、NLog 或 System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java、Log4J 或 Logback](../../azure-monitor/app/java-trace-logs.md)
* [LogStash 插件](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>导出和数据分析
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [流分析](../../azure-monitor/app/export-power-bi.md)
