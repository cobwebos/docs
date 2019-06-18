---
title: Azure 应用服务诊断概述 | Microsoft Docs
description: 了解如何使用你的应用使用应用服务诊断排查问题。
keywords: 应用服务, azure 应用服务, 诊断, 支持, web 应用, 故障排除, 自助服务
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: 3e304df51133d53adad50e672249bde6c9960712
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539783"
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure 应用服务诊断概述

运行 Web 应用程序时，我们希望能够对出现的各种问题做好准备，例如，出现 500 错误，或者用户反映站点已关闭。 应用服务诊断是智能的交互式体验，可帮助你排查应用的问题，且无需配置。 如果应用确实出现问题，应用服务诊断会指出问题所在，并引导你获取适当的信息，以便更轻松快速地排查和解决问题。

尽管此体验在应用过去 24 小时内出现问题时可发挥最大的作用，但是，你始终可以使用所有诊断图形进行分析。

应用服务诊断不但适用于 Windows 上的应用，也适用于 [Linux/容器](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)、[应用服务环境](https://docs.microsoft.com/azure/app-service/environment/intro)和 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 上的应用。

## <a name="open-app-service-diagnostics"></a>打开应用服务诊断

若要访问应用服务诊断，请导航到你的应用服务 web 应用或应用服务环境[Azure 门户](https://portal.azure.com)。 在左侧导航栏中，单击“诊断并解决问题”。 

对于 Azure Functions 中，导航到 function app 中，然后在顶部导航栏中，单击**平台功能**，然后选择**诊断并解决问题**从**资源管理**部分。

在应用服务诊断主页中，可以选择在每个主页磁贴中使用关键字适合您的应用程序的问题的类别。 此外，此页是在哪里可以找到**诊断工具**用于 Windows 的应用程序。 请参阅[诊断工具 （仅适用于 Windows 应用）](#diagnostic-tools-only-for-windows-app)。

![主页](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>交互式接口

一旦选择主页类别最适合于您的应用程序的问题，应用服务诊断交互式界面，精灵，可以指导您完成诊断和解决您的应用程序问题。 提供的精灵磁贴快捷方式可用于查看完整诊断报告的问题类别感兴趣。 磁贴快捷方式提供了直接访问诊断指标的方式。

![磁贴快捷方式](./media/app-service-diagnostics/tile-shortcuts-2.png)

单击这些磁贴上后，可以看到与磁贴中所述的问题相关的主题列表。 这些主题提供完整报表中值得注意信息的片段。 您可以单击任意这些主题以调查进一步的问题。 此外，你可以单击**查看完整报告**浏览单个页面上的所有主题。

![主题](./media/app-service-diagnostics/application-logs-insights-3.png)

![查看完整报告](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>诊断报告

选择要进一步调查此问题通过单击一个主题后，可以查看关于该主题通常补充，使用图表和 markdown 的更多详细信息。 诊断报表可用于查明您的应用程序的问题的强大工具。

![诊断报告](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>运行状况检查

如果不知道什么是与您的应用程序错误或不知道从何处着手排查问题，运行状况检查是一个良好起点。 运行状况检查分析您的应用程序为您提供快速的交互式概述，指出内容处于正常状态，什么是错，告诉您查找要调查此问题的位置。 使用其智能交互式界面可逐步完成故障排除的过程。 运行状况检查的 Windows 应用和 web 应用程序向下诊断精灵体验集成的 Linux 应用的报表。

### <a name="health-checkup-graphs"></a>运行状况检查关系图

运行状况检查中有四个不同的图形。

- **请求和错误：** 显示过去 24 小时内 HTTP 服务器错误以及发出的请求数的关系图。
- **应用程序性能：** 为不同的百分位组最近 24 小时之内显示响应时间的关系图。
- **CPU 使用情况：** 最近 24 小时之内显示每个实例的 CPU 使用率总体百分比的关系图。  
- **内存使用情况：** 最近 24 小时之内显示每个实例的整体百分比的物理内存使用量的关系图。

![运行状况检查](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>调查应用程序代码问题 （仅适用于 Windows 应用程序）

因为许多应用问题与应用程序代码中的问题相关，所以应用服务诊断集成了 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 来突出显示异常和依赖项问题，以便与所选故障时间进行关联。 Application Insights 提供单独启用。

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

若要查看 Application Insights 异常和依赖项，请选择**下的 web 应用**或**web 应用运行速度缓慢**磁贴快捷方式。

### <a name="troubleshooting-steps-only-for-windows-app"></a>（仅适用于 Windows 应用） 的故障排除步骤

如果检测到问题的特定问题类别与过去 24 小时内，你可以查看完整诊断报告，并应用服务诊断可能会提示你查看更多故障排除建议和针对多引导式体验下的一步。

![Application Insights 和故障排除和后续步骤](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>诊断工具 （仅适用于 Windows 应用程序）

诊断工具包括更高级的诊断工具，帮助调查应用程序代码问题，以便运行缓慢、 连接字符串和的详细信息。 并主动工具，可帮助您缓解与 CPU 使用情况、 请求和内存的问题。

### <a name="proactive-cpu-monitoring"></a>主动 CPU 监视

主动 CPU 监视提供了一种简单主动的方法来执行操作时为你的应用在应用程序或子进程正在占用高的 CPU 资源。 可以设置自己的 CPU 阈值规则，以暂时缓解高 CPU 问题，直到找到意外的问题的真正原因。

![主动 CPU 监视](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="proactive-auto-healing"></a>主动预防性自动修复

类似于主动监视 CPU，主动预防性自动修复提供了缓解您的应用程序的意外的行为的简单、 主动方法。 您可以设置规则根据请求计数、 缓慢的请求、 内存限制和触发缓解操作的 HTTP 状态代码。 此工具可以用于暂时缓解意外的行为，直至找到问题的真正原因。 主动预防性自动修复的详细信息，请访问[宣布推出新的自动修复应用服务诊断中的体验](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html)。

![主动预防性自动修复](./media/app-service-diagnostics/proactive-auto-healing-10.png)

## <a name="change-analysis"></a>更改分析

在快节奏的开发环境中，有时可能会很难跟踪的对您的应用程序所做的所有更改，并允许单独 pinpoint 上导致不正常的行为的更改。 更改分析可帮助你缩小以便排除体验对你的应用所做的更改。 更改分析如诊断的报表中嵌入**应用程序崩溃**以便可以与其他指标同时使用它。

![更改分析默认页](./media/app-service-diagnostics/change-analysis-default-page-11.png)

![差异视图](./media/app-service-diagnostics/diff-view-12.png)

更改分析必须使用功能之前启用。 更改分析的详细信息，请访问[宣布推出应用服务诊断中的新更改分析体验](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html)。