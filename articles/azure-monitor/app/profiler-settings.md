---
title: Azure Application Insights Profiler 设置边栏选项卡 | Microsoft Docs
description: 查看 Profiler 状态并启动探查会话
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: b656fb521ad72256e91de63e96aa261f1e94bd13
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54081666"
---
# <a name="configure-application-insights-profiler"></a>配置 Application Insights Profiler

## <a name="profiler-settings-page"></a>Profiler 设置页

可以在“Application Insights 性能”页中按下“Profiler”按钮打开 Profiler 设置页。

![“配置 Profiler”窗格条目][configure-profiler-entry]

“配置 Application Insights Profiler”页包含四项功能： 
1. **立即探查** - 单击此按钮会针对链接到此 Application Insights 实例的所有应用启动探查会话
1. **链接的应用** - 向此 Application Insights 资源发送 Profiler 数据的应用程序列表
1. **正在进行的会话** - 按下“立即探查”时，此处会显示会话状态
1. **最近的探查会话** - 显示有关以往探查会话的信息。

![按需 Profiler][profiler-on-demand]

## <a name="app-service-environments-ase"></a>应用服务环境 (ASE)
根据 ASE 的配置方式，可能会阻止检查代理状态的调用。 此页将会指出代理未运行，但实际上它已运行。 可以检查应用程序中的 Webjob 来确认这一点。 但是，如果已正确设置所有应用设置，并且应用程序中已安装 App Insights 站点扩展，则 Profiler 将会运行；如果有足够的流量发送到应用程序，则该列表中应会显示最近的探查会话。

## <a id="profileondemand"></a> 手动触发 Profiler

可以通过单击一个按钮手动触发 Profiler。 假设你在运行 Web 性能测试。 你将需要使用跟踪来帮助你了解 Web 应用在负载下的性能。 控制何时捕获跟踪至关重要，因为虽然你知道负载测试将在何时运行，但随机采样时间间隔可能会错过它。
以下步骤展示了此方案如何工作：

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>（可选）步骤 1：通过启动 Web 性能测试生成发往 Web 应用的流量

如果 Web 应用已有传入流量或者你就是希望手动生成流量，请跳过本部分并转到步骤 2。

导航到 Application Insights 门户中的“配置”>“性能测试”。 单击“新建”按钮以启动新的性能测试。

![创建新的性能测试][create-performance-test]

在“新建性能测试”窗格中，配置测试目标 URL。 接受所有默认设置并开始运行负载测试。

![配置负载测试][configure-performance-test]

你将看到新测试的状态最先为“已排队”，随后状态变为“正在进行”。

![负载测试已提交并排队][load-test-queued]

![负载测试正在运行中][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>步骤 2：按需启动 Profiler

在负载测试开始运行后，可以启动 Profiler 以在 Web 应用接收负载期间捕获该应用上的跟踪。
导航到“配置 Profiler”窗格：


### <a name="step-3-view-traces"></a>步骤 3：查看跟踪

在 Profiler 完成运行后，按照通知中的说明转到“性能”页并查看跟踪。

## <a name="troubleshooting-on-demand-profiler"></a>对按需 Profiler 进行故障排除

有时，在按需会话后可能会看到 Profiler 超时错误消息：

![Profiler 超时错误][profiler-timeout]

有两个原因可能会导致你看到此错误：

1. 按需 Profiler 会话已成功，但 Application Insights 花费了较长的时间来处理所收集的数据。 如果数据在 15 分钟内未完成处理，则门户将显示一条超时消息。 但是，片刻之后将显示 Profiler 跟踪。 如果发生此情况，请暂时忽略错误消息。 我们正在努力找出修复方法。

1. Web 应用有一个不具备按需功能的较旧版本的 Profiler 代理。 如果之前启用了 Application Insights Profiler，则可能需要更新 Profiler 代理以开始使用按需功能。
  
请执行以下步骤来检查并安装最新的 Profiler：

1. 转到应用服务的“应用设置”并检查是否设置了以下设置：
    * **APPINSIGHTS_INSTRUMENTATIONKEY**：替换为 Application Insights 的正确检测密钥。
    * **APPINSIGHTS_PORTALINFO**：ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**：1.0.0 如果这些设置有任何一个未设置，请转到 Application Insights 启用窗格来安装最新的站点扩展。

1. 转到应用服务门户中的 Application Insights 窗格。

    ![从应用服务门户中启用 Application Insights][enable-app-insights]

1. 如果在下面的页面中看到了“更新”按钮，请单击它以更新 Application Insights 站点扩展，这将安装最新的 Profiler 代理。

    ![更新站点扩展][update-site-extension]

1. 然后单击“更改”来确保启用 Profiler，并选择“确定”来保存更改。

    ![更改并保存 App Insights][change-and-save-appinsights]

1. 返回到应用服务的“应用设置”选项卡来复查是否已设置了以下应用设置项：
    * **APPINSIGHTS_INSTRUMENTATIONKEY**：替换为 Application Insights 的正确检测密钥。
    * **APPINSIGHTS_PORTALINFO**：ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**：1.0.0

    ![Profiler 的应用设置][app-settings-for-profiler]

1. （可选）检查扩展版本，并确保没有任何可用的更新。

    ![检查是否有扩展更新][check-for-extension-update]

## <a name="next-steps"></a>后续步骤
[启用 Profiler 和查看跟踪](profiler-overview.md ?toc=/azure/azure-monitor/toc.json)

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