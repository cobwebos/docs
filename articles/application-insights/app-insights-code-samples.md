---
title: "Application Insights：代码示例和演练"
description: "可根据自己的应用改编的示例。"
services: application-insights
documentationcenter: windows
author: alancameronwills
manager: douge
ms.assetid: 586d0fb2-5286-449b-b327-5c9493632fc1
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 01/05/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: d84ab993b1d9489ca9d2edaa1cb9672d9bced899
ms.openlocfilehash: 2a9cb8062e1dc505706c285269bbbd1eeab72064


---
# <a name="application-insights-code-samples-and-walkthroughs"></a>Application Insights：代码示例和演练


本编译代码示例演示如何使用 [Azure Application Insights](app-insights-overview.md)。

## <a name="sdk-labs"></a>SDK 实验室
[SDK 实验室](https://www.myget.org/gallery/applicationinsights-sdk-labs)是实验性的 NuGet 包集合，可将其安装为 Application Insights SDK 的补充功能，也可以将其卸载。 欢迎试用并提供反馈！

## <a name="web-services"></a>Web 服务
* [将遥测添加到 Azure Web 角色和辅助角色](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService)。
* [Windows 8.1 Universal 应用](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/Windows%208.1%20Universal/)

## <a name="continuous-export"></a>连续导出
若要使用自己的工具分析遥测数据，请将遥测数据[导出](app-insights-export-telemetry.md)到存储，然后在其中进行分析和处理。

* [导出到 Power BI](app-insights-export-power-bi.md) 
* [使用辅助角色分析导出的数据](app-insights-code-sample-export-telemetry-sql-database.md)
* [使用流分析导出到 SQL](app-insights-code-sample-export-sql-stream-analytics.md)
* [为 Microsoft Dynamics CRM 启用遥测](app-insights-sample-mscrm.md)

## <a name="automate-tasks"></a>自动执行任务
* [使用 PowerShell 将 Azure 诊断数据发送到 Application Insights](app-insights-powershell.md)
* [用于创建新 Application Insights 资源的脚本](app-insights-powershell-script-create-resource.md)
* [使用 PowerShell 将 Azure 诊断数据发送到 Application Insights](app-insights-powershell-azure-diagnostics.md)




<!--HONumber=Nov16_HO3-->


