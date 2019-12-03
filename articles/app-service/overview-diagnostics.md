---
title: 诊断和解决工具
description: 了解如何通过 Azure 门户中的 "诊断和解决" 工具 Azure App Service 来解决应用程序的问题。
keywords: 应用服务, azure 应用服务, 诊断, 支持, web 应用, 故障排除, 自助服务
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: e06e71d4436ac6c64ff2edc876d7849d084482f8
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671636"
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure 应用服务诊断概述

运行 Web 应用程序时，我们希望能够对出现的各种问题做好准备，例如，出现 500 错误，或者用户反映站点已关闭。 应用服务诊断是一种智能和交互式体验，可帮助你对应用进行故障排除，无需进行任何配置。 当你在应用中遇到问题时，应用服务诊断会指出出现了什么问题，以帮助你更轻松快速地进行故障排除并解决问题。

尽管在过去24小时内遇到应用问题时这一体验最有用，但所有诊断图形始终都可供你进行分析。

应用服务诊断不但适用于 Windows 上的应用，也适用于 [Linux/容器](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)、[应用服务环境](https://docs.microsoft.com/azure/app-service/environment/intro)和 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 上的应用。

## <a name="open-app-service-diagnostics"></a>打开应用服务诊断

若要访问应用服务诊断，请导航到应用服务 web 应用或[Azure 门户](https://portal.azure.com)中的应用服务环境。 在左侧导航栏中，单击“诊断并解决问题”。

对于 "Azure Functions"，导航到 function app，然后在顶部导航栏中，单击 "**平台功能**"，然后从 "**资源管理**" 部分选择 "**诊断和解决问题**"。

在应用服务诊断主页中，可以通过使用每个主页磁贴中的关键字，选择最能描述应用问题的类别。 此外，还可以在此页中找到 Windows 应用的**诊断工具**。 请参阅[诊断工具（仅适用于 Windows 应用）](#diagnostic-tools-only-for-windows-app)。

![主页](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>交互式接口

选择最适合你的应用程序问题的 "主页" 类别后，应用服务诊断的交互式界面精灵可以指导你完成诊断和解决应用的问题。 可以使用精灵提供的磁贴快捷方式查看感兴趣的问题类别的完整诊断报告。 磁贴快捷方式为您提供了一种访问诊断指标的直接方式。

![磁贴快捷方式](./media/app-service-diagnostics/tile-shortcuts-2.png)

单击这些磁贴后，可以看到与磁贴中所述问题相关的主题列表。 这些主题提供完整报告的重要信息片段。 您可以单击这些主题中的任何一种，进一步调查问题。 此外，还可以单击 "**查看完整报表**" 来浏览单个页面上的所有主题。

![主题](./media/app-service-diagnostics/application-logs-insights-3.png)

![查看完整报表](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>诊断报告

通过单击某个主题选择进一步调查问题后，可以查看有关该主题的更多详细信息，这些详细信息通常使用图形和 markdown 来补充。 诊断报告是一种功能强大的工具，可用于查明应用的问题。

![诊断报告](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>运行状况检查

如果您不知道您的应用程序的问题，或者不知道从何处着手排查您的问题，则运行状况检查是一个不错的开端。 运行状况检查会分析你的应用程序，以便为你快速、交互式地概述如何确定运行状况和错误，并告诉你在何处查找问题。 使用其智能交互式界面可逐步完成故障排除的过程。 运行状况检查与适用于 Linux 应用的 Windows 应用和 web 应用的精灵体验集成。

### <a name="health-checkup-graphs"></a>运行状况检查图

运行状况检查有四个不同的关系图。

- **请求和错误：** 显示过去24小时内发出的请求数以及 HTTP 服务器错误的图表。
- **应用程序性能：** 显示过去24小时内各个百分位组的响应时间的图表。
- **CPU 使用率：** 显示过去24小时内每个实例的总 CPU 使用率的图表。  
- **内存使用率：** 显示过去24小时内每个实例的总物理内存使用百分比的图表。

![运行状况检查](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>调查应用程序代码问题（仅适用于 Windows 应用）

因为许多应用问题与应用程序代码中的问题相关，所以应用服务诊断集成了 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 来突出显示异常和依赖项问题，以便与所选故障时间进行关联。 必须单独启用 Application Insights。

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

若要查看 Application Insights 异常和依赖项，请选择 "**关闭 web 应用**" 或 " **web 应用慢速**磁贴快捷方式"。

### <a name="troubleshooting-steps-only-for-windows-app"></a>故障排除步骤（仅适用于 Windows 应用）

如果在过去24小时内检测到具有特定问题类别的问题，则可以查看完整的诊断报告，应用服务诊断可能会提示你查看更多的故障排除建议和后续步骤，以获得更多引导式体验。

![Application Insights 和疑难解答及后续步骤](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>诊断工具（仅适用于 Windows 应用）

诊断工具包括更高级的诊断工具，可帮助您调查应用程序代码问题、缓慢、连接字符串等。 和预防性工具，可帮助你缓解 CPU 使用情况、请求数和内存问题。

### <a name="proactive-cpu-monitoring"></a>主动 CPU 监视

主动 CPU 监视为你的应用程序或子进程消耗高 CPU 资源时，提供一种简单且主动的方式来执行操作。 你可以设置自己的 CPU 阈值规则，以暂时降低 CPU 严重问题，直到发现意外问题的真正原因。 有关详细信息，请参阅[降低 CPU 问题](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html)。

![主动 CPU 监视](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>自动修复和主动自动修复

自动修复是应用遇到意外行为时可以采取的缓解措施。 您可以基于请求计数、慢速请求、内存限制和 HTTP 状态代码设置自己的规则，以触发缓解操作。 使用此工具可暂时降低意外行为，直到找到根本原因。 有关详细信息，请参阅[在应用服务诊断中发布新的自动修复体验](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html)。

![自动修复](./media/app-service-diagnostics/auto-healing-10.png)

与主动 CPU 监视一样，主动自动修复是一种用于缓解应用程序意外行为的关键解决方案。 当应用服务确定应用处于不可恢复的状态时，主动自动修复会重启应用。 有关详细信息，请参阅[引入前瞻性自动修复](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html)。

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>导航器和更改分析（仅适用于 Windows 应用）

在具有持续集成并且应用具有许多依赖项的大型团队中，可能很难确定导致行为不正常的特定更改。 导航器通过自动渲染应用的依赖项映射和同一订阅中的所有资源，帮助查看应用的拓扑。 导航器使你可以查看应用及其依赖项所做的更改的合并列表，并缩小导致不正常行为的更改范围。 它可通过主页磁贴**导航器**进行访问，在首次使用前需要启用。 有关详细信息，请参阅[利用导航器了解应用的依赖项](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html)。

![导航器默认页](./media/app-service-diagnostics/navigator-default-page-11.png)

![差异视图](./media/app-service-diagnostics/diff-view-12.png)

可以通过磁贴快捷方式、**应用程序更改**和**应用程序**在**可用性和性能**方面的崩溃来访问应用更改的更改分析，使你可以同时与其他指标一起使用。 在使用此功能之前，必须首先启用它。 有关详细信息，请参阅发布[应用服务诊断中的新更改分析体验](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html)。

通过在标题中添加 "[诊断]"，在[UserVoice](https://feedback.azure.com/forums/169385-web-apps)上发布你的问题或反馈。
