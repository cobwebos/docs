---
title: "SCOM 与 Application Insights 集成 | Microsoft 文档"
description: "如果你是 SCOM 用户，请监视性能并诊断 Application Insights 的问题。 全面的仪表板、智能警报、功能强大的诊断工具和分析查询。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 606e9d03-c0e6-4a77-80e8-61b75efacde0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/12/2016
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: dea21a59b189d1d3d474cbc5e67f64df485a1981
ms.openlocfilehash: 68ec072b972e38d8cd020adda4dcc85cdaccfb76
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016

---
# <a name="application-performance-monitoring-using-application-insights-for-scom"></a>为 SCOM 使用 Application Insights 的应用程序性能监视
如果使用 System Center Operations Manager (SCOM) 管理服务器，可在 [Azure Application Insights](app-insights-asp-net.md) 的帮助下监视性能并诊断性能问题。 Application Insights 监视 Web 应用程序的传入请求、传出 REST 和 SQL 调用、异常和日志跟踪。 它提供带有度量值图和智能警报的仪表板，以及针对此遥测的功能强大的搜索和分析查询。 

可使用 SCOM 管理包在 Application Insights 监视上切换。

## <a name="before-you-start"></a>开始之前
我们假设：

* 你熟悉 SCOM，并且使用 SCOM 2012 R2 或 2016 管理 IIS Web 服务器。
* 已在服务器上安装要使用 Application Insights 监视的 Web 应用程序。
* 应用框架版本为 .NET 4.5 或更高版本。
* 你有权访问 [Microsoft Azure](https://azure.com) 中的订阅，并且可登录 [Azure 门户](https://portal.azure.com)。 你的组织可能有订阅，并且可以将你的 Microsoft 帐户添加到它。

（开发团队可能在 Web 应用中生成 [Application Insights SDK](app-insights-asp-net.md)。 此生成时检测使他们在编写自定义遥测时具有更大的灵活性。 但是，没关系：无论是否内置 SDK，都可以按照此处所述的步骤操作。）

## <a name="one-time-install-application-insights-management-pack"></a>（一次性）安装 Application Insights 管理包
在可运行 Operations Manager 的计算机上：

1. 卸载管理包的任何较早版本：
   1. 在 Operations Manager 中，依次打开“管理”、“管理包”。 
   2. 删除旧版本。
2. 从目录中下载并安装管理包。
3. 重启 Operations Manager。

## <a name="create-a-management-pack"></a>创建管理包
1. 在 Operations Manager 中，依次打开“创作”、“使用 Application Insights 的 .NET...”、“添加监视向导”，然后再次选择“使用 Application Insights 的 .NET...”。
   
    ![](./media/app-insights-scom/020.png)
2. 根据应用为配置命名。 （必须一次检测一个应用程序。）
   
    ![](./media/app-insights-scom/030.png)
3. 在相同的向导页上，创建新管理包，或选择之前为 Application Insights 创建的管理包。
   
     （Application Insights [管理包](https://technet.microsoft.com/library/cc974491.aspx)是一个模板，可通过它创建实例。 以后可重复使用相同实例。）

    ![在“常规属性”选项卡中，键入应用名称。 单击“新建”并键入管理包的名称。 单击“确定”，然后单击“下一步”。](./media/app-insights-scom/040.png)

1. 选择要监视的一个应用。 搜索功能在服务器上安装的应用中搜索。
   
    ![在“监视对象”选项卡中，键入部分应用名称、单击“搜索”、选择该应用，然后依次单击“添加”、“确定”。](./media/app-insights-scom/050.png)
   
    如果不希望监视所有服务器中的应用，可使用选填的“监视范围”字段指定服务器的子集。
2. 在下一个向导页上，必须先提供凭据才能登录到 Microsoft Azure。
   
    在此页面上，选择要分析和显示遥测数据的 Application Insights 资源。 
   
   * 如果应用程序已在开发期间针对 Application Insights 配置，则选择其现有资源。
   * 否则，创建为该应用的新资源。 如果有其他应用是同一系统的组件，请将它们放入同一资源组中，以使对遥测的访问更易于管理。
     
     以后可更改这些设置。
     
     ![在“Application Insights 设置”选项卡上，单击“登录”并为 Azure 提供 Microsoft 帐户凭据。 然后选择订阅、资源组和资源。](./media/app-insights-scom/060.png)
3. 完成该向导。
   
    ![单击创建](./media/app-insights-scom/070.png)

为要监视的每个应用重复此过程。

如果以后需要更改设置，请从“创作”窗口重新打开监视器的属性。

![在“创作”中，选择“使用 Application Insights 的 .NET 应用程序性能监视”、选择监视器，然后单击“属性”。](./media/app-insights-scom/080.png)

## <a name="verify-monitoring"></a>验证监视
已安装的监视器将在每台服务器上搜索应用。 如果它找到应用，它会将 Application Insights 状态监视器配置为监视该应用。 如有必要，它首先在服务器上安装状态监视器。

可验证它找到了应用的哪个实例：

![在“监视”中，打开“Application Insights”](./media/app-insights-scom/100.png)

## <a name="view-telemetry-in-application-insights"></a>在 Application Insights 中查看遥测
在 [Azure 门户](https://portal.azure.com)中，浏览到应用的资源。 从应用[查看显示遥测的图表](app-insights-dashboards.md)。 （如果它尚未显示在主页上，请单击“实时指标流”。)

## <a name="next-steps"></a>后续步骤
* [设置仪表板](app-insights-dashboards.md)以将监视此应用和其他应用的重要图表汇总在一起。
* [了解指标](app-insights-metrics-explorer.md)
* [设置警报](app-insights-alerts.md)
* [诊断性能问题](app-insights-detect-triage-diagnose.md)
* [功能强大的分析查询](app-insights-analytics.md)
* [可用性 Web 测试](app-insights-monitor-web-app-availability.md)


