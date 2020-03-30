---
title: 诊断和求解工具
description: 了解如何使用 Azure 门户中的诊断和解决工具解决 Azure 应用服务中的应用问题。
keywords: 应用服务, azure 应用服务, 诊断, 支持, web 应用, 故障排除, 自助服务
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: e06e71d4436ac6c64ff2edc876d7849d084482f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671636"
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure 应用服务诊断概述

运行 Web 应用程序时，我们希望能够对出现的各种问题做好准备，例如，出现 500 错误，或者用户反映站点已关闭。 应用服务诊断是智能的交互式体验，可帮助你排查应用的问题，且无需配置。 如果应用确实出现问题，应用服务诊断会指出问题所在，并引导你获取适当的信息，以便更轻松快速地排查和解决问题。

尽管此体验在应用过去 24 小时内出现问题时可发挥最大的作用，但是，你始终可以使用所有诊断图形进行分析。

应用服务诊断不但适用于 Windows 上的应用，也适用于 [Linux/容器](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)、[应用服务环境](https://docs.microsoft.com/azure/app-service/environment/intro)和 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 上的应用。

## <a name="open-app-service-diagnostics"></a>打开应用服务诊断

要访问应用服务诊断，请导航到[Azure 门户](https://portal.azure.com)中的应用服务 Web 应用或应用服务环境。 在左侧导航栏中，单击“诊断并解决问题”。****

对于 Azure 函数，导航到函数应用，并在顶部导航中单击**平台功能**，然后从 **"资源管理**"部分选择 **"诊断并解决问题**"。

在"应用服务诊断主页"中，您可以使用每个主页磁贴中的关键字选择最能描述应用问题的类别。 此外，此页面是您可以找到 Windows 应用**的诊断工具**的位置。 请参阅[诊断工具（仅适用于 Windows 应用）。](#diagnostic-tools-only-for-windows-app)

![主页](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>交互式界面

选择最适合应用问题的主页类别后，应用服务诊断的交互式界面 Genie 可以指导您完成应用的诊断和解决问题。 您可以使用 Genie 提供的磁贴快捷方式查看您感兴趣的问题类别的完整诊断报告。 磁贴快捷方式提供了访问诊断指标的直接方法。

![磁贴快捷方式](./media/app-service-diagnostics/tile-shortcuts-2.png)

单击这些磁贴后，您可以看到与磁贴中描述的问题相关的主题列表。 这些主题提供了完整报告中的显著信息片段。 您可以单击这些主题中的任何一个以进一步调查问题。 此外，您还可以单击"**查看完整报告**"以浏览单个页面上的所有主题。

![主题](./media/app-service-diagnostics/application-logs-insights-3.png)

![查看完整报告](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>诊断报告

选择通过单击主题进一步调查问题后，您可以查看有关该主题的更多详细信息，这些详细信息通常辅以图形和标记。 诊断报告可以是一个强大的工具，可以查明应用的问题。

![诊断报告](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>运行状况检查

如果您不知道应用出了什么问题，或者不知道从哪里开始解决问题，则运行状况检查是一个很好的开始。 运行状况检查会分析您的应用程序，为您提供快速的交互式概述，指出什么是健康的和出了什么问题，并告诉您在哪里调查问题。 使用其智能交互式界面可逐步完成故障排除的过程。 运行状况检查与适用于 Windows 应用的精灵体验和 Linux 应用的 Web 应用向下诊断报告集成。

### <a name="health-checkup-graphs"></a>运行状况检查图表

运行状况检查中有四个不同的图形。

- **请求和错误：** 显示过去 24 小时内发出的请求数以及 HTTP 服务器错误的图表。
- **应用性能：** 显示各种百分位数组的过去 24 小时的响应时间的图形。
- **CPU 使用率：** 显示过去 24 小时内每个实例的总 CPU 使用率的图形。  
- **内存使用情况：** 显示过去 24 小时内每个实例的物理内存使用率的总体百分比的图形。

![运行状况检查](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>调查应用程序代码问题（仅适用于 Windows 应用）

因为许多应用问题与应用程序代码中的问题相关，所以应用服务诊断集成了 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 来突出显示异常和依赖项问题，以便与所选故障时间进行关联。 必须单独启用应用程序见解。

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

要查看应用程序见解的例外项和依赖项，**请选择 Web 应用或** **Web 应用慢磁贴**快捷方式。

### <a name="troubleshooting-steps-only-for-windows-app"></a>故障排除步骤（仅适用于 Windows 应用）

如果在过去 24 小时内检测到特定问题类别的问题，您可以查看完整的诊断报告，应用服务诊断可能会提示您查看更多故障排除建议和后续步骤，以便获得更引导的体验。

![应用程序见解和故障排除以及后续步骤](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>诊断工具（仅适用于 Windows 应用）

诊断工具包括更高级的诊断工具，可帮助您调查应用程序代码问题、速度慢、连接字符串等。 和主动工具，帮助您缓解 CPU 使用率、请求和内存问题。

### <a name="proactive-cpu-monitoring"></a>主动 CPU 监控

主动 CPU 监视为您提供了在应用的应用或子进程消耗高 CPU 资源时采取操作的简便、主动的方法。 您可以设置自己的 CPU 阈值规则，以暂时缓解高 CPU 问题，直到找到意外问题的真正原因。 有关详细信息，请参阅在[CPU 问题发生之前缓解这些问题](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html)。

![主动 CPU 监控](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>自动修复和主动自动修复

自动修复是应用发生意外行为时可以执行的缓解操作。 您可以根据请求计数、慢速请求、内存限制和 HTTP 状态代码设置自己的规则，以触发缓解操作。 使用该工具暂时缓解意外行为，直到找到根本原因。 有关详细信息，请参阅[在应用服务诊断中宣布新的自动修复体验](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html)。

![自动修复](./media/app-service-diagnostics/auto-healing-10.png)

与主动 CPU 监视一样，主动自动修复是缓解应用意外行为的交钥匙解决方案。 当应用服务确定你的应用处于不可恢复状态时，主动自动修复将重新启动应用。 有关详细信息，请参阅[引入主动自动修复](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html)。

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>导航器和更改分析（仅适用于 Windows 应用）

在具有持续集成的大型团队中，以及你的应用具有许多依赖关系的地方，很难确定导致不正常行为的特定更改。 导航器通过自动呈现应用的依赖项映射以及同一订阅中的所有资源，帮助获取应用拓扑的可见性。 导航器允许您查看应用及其依赖项所做的更改的整合列表，并缩小导致不正常行为的更改的范围。 它可以通过主页磁贴**导航器**进行访问，并且需要在第一次使用之前启用它。 有关详细信息，请参阅使用[Navigator 查看应用的依赖项](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html)。

![导航器默认页](./media/app-service-diagnostics/navigator-default-page-11.png)

![差异视图](./media/app-service-diagnostics/diff-view-12.png)

可以通过磁贴快捷方式、**应用程序更改**和**应用程序崩溃**访问应用更改分析 **，** 以便与其他指标同时使用。 在使用该功能之前，必须首先启用它。 有关详细信息，请参阅[在应用服务诊断中宣布新的更改分析体验](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html)。

通过在标题中添加"[Diag]"，在[UserVoice](https://feedback.azure.com/forums/169385-web-apps)上发布您的问题或反馈。
