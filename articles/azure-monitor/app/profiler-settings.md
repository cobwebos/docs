---
title: 使用 Azure Application Insights Profiler 设置窗格 | Microsoft Docs
description: 查看 Profiler 状态并启动分析会话
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9d9cc377ead0c297e8334d34255bd2c7c7cd39fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86499403"
---
# <a name="configure-application-insights-profiler"></a>配置 Application Insights Profiler

## <a name="updated-profiler-agent"></a>更新的 Profiler 代理
触发器功能仅适用于版本 2.6 或更高版本的 Profiler 代理。 如果运行的是 Azure 应用服务，代理将自动更新。 如果转到网站的 Kudu URL 并将 \DiagnosticServices 追加到其末尾（类似于 `https://yourwebsite.scm.azurewebsites.net/diagnosticservices`），则可以查看正在运行的代理版本。 Application Insights Profiler Webjob 应为版本 2.6 或更高版本。 可以通过重启 Web 应用来强制升级。 

如果在 VM 或云服务上运行 Profiler，则需要安装 Windows Azure 诊断 (WAD) 扩展版本 16.0.4 或更高版本。 可以通过登录到 VM 并查看以下目录来检查 WAD 的版本：C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4。 目录名称即为安装的 WAD 版本。 有新版本可用时，Azure VM 代理会自动更新 WAD。

## <a name="profiler-settings-page"></a>Profiler 设置页

若要打开 Azure Application Insights Profiler 设置窗格，请转到“Application Insights 性能”窗格，然后选择“配置 Profiler”按钮。

![用于打开 Profiler 设置页的链接][configure-profiler-entry]

此时会打开如下所示的页面：

![Profiler 设置页][configure-profiler-page]

“配置 Application Insights Profiler”页包含以下功能：

| Feature | 说明 |
|-|-|
立即探查 | 启动与此 Application Insights 实例链接的所有应用的分析会话。
触发器 | 用于配置使 Profiler 运行的触发器。 
最近的探查会话 | 显示有关最近分析会话的信息。

## <a name="profile-now"></a>立即探查
使用此选项可以按需启动探查会话。 单击此链接时，向此 Application Insights 实例发送数据的所有 Profiler 代理将开始捕获探查数据。 5 到 10 分钟后，探查会话将显示在下面的列表中。

用户若要手动触发 Profiler 会话，他们至少需要对其 Application Insights 组件角色具有“写入”访问权限。 在大多数情况下，可以自动获得此访问权限，不需要额外的工作。 如果遇到问题，则要添加的订阅范围角色将是“Application Insights 组件参与者”角色。 [详细了解如何使用 Azure 监视进行角色访问控制](./resources-roles-access-control.md)。

## <a name="trigger-settings"></a>触发器设置
![触发器设置浮出控件][trigger-settings-flyout]

单击菜单栏上的“触发器”按钮会打开“触发器设置”框。 可将触发器设置为在 CPU 或内存使用百分比达到设置的级别时启动探查。

| 设置 | 描述 |
|-|-|
开/关按钮 | 开：Profiler 可由此触发器启动；关：探查器不会由此触发器启动。
内存阈值 | 内存使用率达到此百分比时，将启动 Profiler。
持续时间 | 设置 Profiler 在触发后要运行的时间长短。
冷却 | 设置 Profiler 在触发后，再次检查内存或 CPU 使用率之前所要等待的时间长短。

## <a name="recent-profiling-sessions"></a>最近的探查会话
此页面部分显示有关最近建立的探查会话的信息。 探查会话代表 Profiler 代理探查托管应用程序的某台计算机的时间段。 单击其中一个行可从会话打开探查数据。 对于每个会话，此部分会显示：

| 设置 | 描述 |
|-|-|
触发者 | 会话的启动方式：由触发器启动、由“立即探查”操作启动，或由默认采样启动。 
应用程序名称 | 已探查的应用程序的名称。
计算机实例 | 运行 Profiler 代理的计算机的名称。
Timestamp | 捕获探查数据的时间。
受跟踪者 | 附加到各个请求的跟踪数。
CPU % | Profiler 运行时使用的 CPU 百分比。
内存百分比 | Profiler 运行时使用的内存百分比。

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a> 使用 Web 性能测试生成发往应用程序的流量

只需单击一下，即可手动触发 Profiler。 假设要运行 Web 性能测试。 需要借助跟踪来了解 Web 应用在承受负载情况下的运行性能。 控制何时捕获跟踪至关重要，因为知道负载测试何时将运行。 但是随机采样间隔可能会错过捕获。

下一部分将说明此方案的工作原理：

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>步骤 1：通过启动 Web 性能测试生成发往 Web 应用的流量

如果 Web 应用已有传入流量或者你就是希望手动生成流量，请跳过本部分并转到步骤 2。

1. 在 Application Insights 门户中，选择“配置” > “性能测试” 。 

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
