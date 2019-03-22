---
title: 使用 Azure Application Insights Profiler 设置窗格 | Microsoft Docs
description: 查看 Profiler 状态并启动分析会话
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 8c9fba14bd3f7d3b55a245f8e647f0eae1f8ef83
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118419"
---
# <a name="configure-application-insights-profiler"></a>配置 Application Insights Profiler

## <a name="profiler-settings-pane"></a>Profiler 设置窗格

打开 Azure Application Insights Profiler 设置窗格中，请转到“Application Insights 性能”窗格中，然后选择“Profiler”按钮。

![配置 Profiler 窗格][configure-profiler-entry]

“配置 Application Insights Profiler”窗格包含四项功能： 
* **立即探查**：启动与此 Application Insights 实例链接的所有应用的分析会话。
* **链接的应用**：列出将分析数据发送到此 Application Insights 资源的应用程序。
* **正在进行的会话**：显示选中“立即探查”时的会话状态。 
* **最新的分析会话**：显示有关最近分析会话的信息。

![按需 Profiler][profiler-on-demand]

## <a name="app-service-environment"></a>应用服务环境
根据 Azure 应用服务环境的配置方式，可能会阻止检查代理状态的调用。 即使代理正在运行，窗格可能会显示消息称代理没有运行。 为了确保确实如此，请检查应用程序上的 Web 作业。 如果所有应用设置值都正确并且应用程序上安装了 Application Insights 站点扩展，则 Profiler 正在运行。 如果应用程序正在接收足够的流量，则最近的分析会话应显示在列表中。

## <a id="profileondemand"></a> 手动触发 Profiler

只需单击一下，即可手动触发 Profiler。 假设要运行 Web 性能测试。 你将需要跟踪来帮助了解 Web 应用在负载下的性能如何。 控制何时捕获跟踪至关重要，因为知道负载测试何时将运行。 但是随机采样间隔可能会错过捕获。

下一部分将说明此方案的工作原理：

### <a name="step-1-optional-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>步骤 1：（可选）通过启动 Web 性能测试生成发往 Web 应用的流量

如果 Web 应用已有传入流量或者你就是希望手动生成流量，请跳过本部分并转到步骤 2。

1. 在 Application Insights 门户中，选择“配置” > “性能测试”。 

1. 若要启动新的性能测试，请选择“新建”按钮。

   ![创建新的性能测试][create-performance-test]

1. 在“新建性能测试”窗格中，配置测试目标 URL。 接受所有默认设置，然后选择“运行测试”开始运行负载测试。

    ![配置负载测试][configure-performance-test]

    新测试的状态最先为“已排队”，随后状态变为“正在进行”。

    ![负载测试已提交并排队][load-test-queued]

    ![负载测试正在运行中][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>步骤 2：启动 Profiler 按需会话

1. 负载测试运行时，启动 Profiler 以在 Web 应用接收负载期间捕获该应用上的跟踪。

1. 转到“配置 Profiler”窗格。


### <a name="step-3-view-traces"></a>步骤 3：查看跟踪

在 Profiler 完成运行后，按照通知中的说明转到“性能”窗格并查看跟踪。

## <a name="troubleshoot-the-profiler-on-demand-session"></a>对 Profiler 按需会话进行故障排除

在按需会话后，可能会收到 Profiler 超时错误消息：

![Profiler 超时错误][profiler-timeout]

出于以下某项原因，可能会收到此错误消息：

* 按需 Profiler 会话已成功，但 Application Insights 花费了比预期更长的时间来处理所收集的数据。  

  如果在 15 分钟内未处理数据，则门户会显示一条超时消息。 但是，片刻之后，将显示 Profiler 跟踪。 如果收到一条错误消息，请暂时忽略它。 我们正在努力找出修复方法。

* Web 应用有一个不具备按需功能的较旧版本的 Profiler 代理。  

  如果之前启用了 Application Insights Profiler，则可能需要更新 Profiler 代理以开始使用按需功能。
  
转到应用服务的“应用设置”窗格并检查以下设置：
* **APPINSIGHTS_INSTRUMENTATIONKEY**：替换为 Application Insights 的正确检测密钥。
* **APPINSIGHTS_PORTALINFO**：ASP.NET
* **APPINSIGHTS_PROFILERFEATURE_VERSION**：1.0.0

如果未设置任何前述值，请执行以下操作安装最新的站点扩展：

1. 转到应用服务门户中的“Application Insights”窗格。

    ![从应用服务门户中启用 Application Insights][enable-app-insights]

1. 如果“Application Insights”窗格显示“更新”按钮，请选择该按钮以更新 Application Insights 站点扩展，这将安装最新的 Profiler 代理。

    ![更新站点扩展][update-site-extension]

1. 若要确保已启用 Profiler，请选择“更改”，然后选择“确定”保存所做的更改。

    ![更改并保存 App Insights][change-and-save-appinsights]

1. 返回到应用服务的“应用设置”窗格，确保设置了以下值：
   * **APPINSIGHTS_INSTRUMENTATIONKEY**：替换为 Application Insights 的正确检测密钥。
   * **APPINSIGHTS_PORTALINFO**：ASP.NET 
   * **APPINSIGHTS_PROFILERFEATURE_VERSION**：1.0.0

     ![Profiler 的应用设置][app-settings-for-profiler]

1. （可选）选择“扩展”，然后检查扩展版本并确定更新是否可用。

    ![检查是否有扩展更新][check-for-extension-update]

## <a name="next-steps"></a>后续步骤
[启用 Profiler 并查看跟踪](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
