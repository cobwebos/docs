---
title: 使用 Azure Application Insights Profiler 设置窗格 | Microsoft Docs
description: 查看 Profiler 状态并启动分析会话
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: f66a23c0562ec9f1987bd119a45b7b767f2dfe46
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671624"
---
# <a name="configure-application-insights-profiler"></a>配置 Application Insights Profiler

## <a name="updated-profiler-agent"></a>已更新探查器代理
触发器功能仅适用于版本2.6 或更高版本的探查器代理。 如果运行的是 Azure App Service，则代理将自动更新。 如果你访问网站的 Kudu URL 并将 \DiagnosticServices 附加到其末尾，则可以查看正在运行的代理的版本，如下所示： https://yourwebsite.scm.azurewebsites.net/diagnosticservices。 Application Insights Profiler Webjob 应为2.6 版或更高版本。 可以通过重新启动 web 应用来强制升级。 

如果在 VM 或云服务上运行探查器，则需要安装 Windows Azure 诊断（WAD）扩展版本16.0.4 或更高版本。 可以通过登录到 VM 并查看以下目录来检查 WAD 的版本： C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4。 目录名称是安装的 WAD 的版本。 当新版本可用时，Azure VM 代理会自动更新 WAD。

## <a name="profiler-settings-page"></a>探查器设置页

若要打开 "Azure 应用程序 Insights 探查器设置" 窗格，请参阅 "Application Insights 性能" 窗格，然后选择 "**配置探查器**" 按钮。

!["打开探查器设置" 页的链接][configure-profiler-entry]

这会打开如下所示的页面：

![探查器设置页][configure-profiler-page]

"**配置 Application Insights Profiler** " 页具有以下功能：

| | |
|-|-|
立即配置文件 | 启动与此 Application Insights 实例链接的所有应用的分析会话。
触发器 | 允许您配置导致探查器运行的触发器。 
最近分析会话 | 显示有关最近分析会话的信息。

## <a name="profile-now"></a>立即配置文件
使用此选项可以按需启动分析会话。 单击此链接时，将数据发送到此 Application Insights 实例的所有探查器代理都将开始捕获配置文件。 5到10分钟后，配置文件会话将显示在下面的列表中。

为了使用户能够手动触发探查器会话，它们至少需要对 Application Insights 组件的角色具有 "写入" 访问权限。 在大多数情况下，你可以自动获取此访问权限，而无需执行其他操作。 如果遇到问题，要添加的订阅作用域角色为 "Application Insights 组件参与者" 角色。 [查看有关使用 Azure 监视的角色访问控制的详细信息](https://docs.microsoft.com/azure/azure-monitor/app/resources-roles-access-control)。

## <a name="trigger-settings"></a>触发器设置
![触发器设置浮出控件][trigger-settings-flyout]

单击菜单栏上的 "触发器" 按钮将打开 "触发器设置" 框。 当 CPU 或内存使用率达到所设置的级别时，可以设置触发器来开始分析。

| | |
|-|-|
"开/关" 按钮 | 开启：探查器可以通过此触发器启动;Off：探查器不会由此触发器启动。
内存阈值 | 如果正在使用此内存百分比，则探查器将启动。
Duration | 设置探查器在触发时将运行的时间长度。
Cooldown | 设置探查器在其触发之后再次检查内存或 CPU 使用情况之前将等待的时间长度。

## <a name="recent-profiling-sessions"></a>最近分析会话
页面的此部分显示有关最近分析会话的信息。 分析会话表示探查器代理在承载应用程序的某个计算机上使用配置文件的时间段。 单击其中一个行即可从会话中打开配置文件。 对于每个会话，我们将显示：

| | |
|-|-|
触发者 | 会话的启动方式，可以是触发器、立即配置文件或默认采样。 
应用程序名称 | 已分析的应用程序的名称。
计算机实例 | 探查器代理运行所在的计算机的名称。
时间戳 | 捕获配置文件的时间。
Tracee | 附加到各个请求的跟踪数。
CPU % | 探查器运行时所使用的 CPU 的百分比。
记忆 | 探查器运行时所使用的内存的百分比。

## <a id="profileondemand"></a>使用 web 性能测试生成到应用程序的流量

只需单击一下，即可手动触发 Profiler。 假设要运行 Web 性能测试。 你需要跟踪来帮助你了解 web 应用在负载下的运行情况。 控制何时捕获跟踪至关重要，因为知道负载测试何时将运行。 但是随机采样间隔可能会错过捕获。

下一部分将说明此方案的工作原理：

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>步骤1：通过启动 web 性能测试生成到 web 应用的流量

如果 Web 应用已有传入流量或者你就是希望手动生成流量，请跳过本部分并转到步骤 2。

1. 在 Application Insights 门户中，选择“配置” **“性能测试”**  > 。 

1. 若要启动新的性能测试，请选择“新建”按钮。

   ![创建新的性能测试][create-performance-test]

1. 在“新建性能测试”窗格中，配置测试目标 URL。 接受所有默认设置，然后选择“运行测试”开始运行负载测试。

    ![配置负载测试][configure-performance-test]

    新测试的状态最先为“已排队”，随后状态变为“正在进行”。

    ![负载测试已提交并排队][load-test-queued]

    ![负载测试正在运行中][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>步骤2：启动探查器点播会话

1. 负载测试运行时，启动 Profiler 以在 Web 应用接收负载期间捕获该应用上的跟踪。

1. 转到“配置 Profiler”窗格。


### <a name="step-3-view-traces"></a>步骤 3：查看跟踪

在 Profiler 完成运行后，按照通知中的说明转到“性能”窗格并查看跟踪。

## <a name="next-steps"></a>后续步骤
[启用 Profiler 并查看跟踪](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[configure-profiler-page]: ./media/profiler-settings/configureBlade.png
[trigger-settings-flyout]: ./media/profiler-settings/CPUTrigger.png
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
