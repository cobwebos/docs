---
title: "应用服务中 Web 应用性能缓慢 | Microsoft Docs"
description: "本文帮助排查 Azure App Service 中 Web 应用性能缓慢的问题。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: top-support-issue
keywords: "Web 应用性能，缓慢应用，应用缓慢"
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: cephalin
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 1cfe7ec37ad8b24a8bd9ab2bf67e95675a57b675
ms.contentlocale: zh-cn
ms.lasthandoff: 08/04/2017

---
# <a name="troubleshoot-slow-web-app-performance-issues-in-azure-app-service"></a>排查 Azure 应用服务中 Web 应用性能缓慢的问题
本文将帮助排查 [Azure 应用服务](http://go.microsoft.com/fwlink/?LinkId=529714)中 Web 应用性能缓慢的问题。

如果对本文中的任何内容需要更多帮助，可以联系 [MSDN Azure 和堆栈溢出论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者，你也可以提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)，并单击“**获取支持**”。

## <a name="symptom"></a>症状
浏览 Web 应用时，网页加载缓慢，并且有时还会超时。

## <a name="cause"></a>原因
此问题通常是应用程序级别的问题造成的，例如：

* 网络请求耗时过长
* 应用程序代码或数据库查询效率低下
* 应用程序的内存/CPU 使用率过高
* 应用程序因异常而崩溃

## <a name="troubleshooting-steps"></a>疑难解答步骤
故障排除可划分为三种不同的任务，依次为：

1. [观察和监视应用程序行为](#observe)
2. [收集数据](#collect)
3. [缓解问题](#mitigate)

[应用服务 Web 应用](/services/app-service/web/)为每个步骤提供了多种选项。

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1.观察和监视应用程序行为
#### <a name="track-service-health"></a>跟踪服务运行状况
每次发生服务中断或性能下降时 Microsoft Azure 会发出通告。 可以在 [Azure 门户](https://portal.azure.com/)中跟踪服务的运行状况。 有关详细信息，请参阅[跟踪服务的运行状况](../monitoring-and-diagnostics/insights-service-health.md)。

#### <a name="monitor-your-web-app"></a>监视 Web 应用
此选项可用于查明应用程序是否存在任何问题。 在 Web 应用的边栏选项卡中，单击“请求和错误”磁贴。 “指标”边栏选项卡显示所有可以添加的指标。

可能想要在 Web 应用中监视的一些指标包括

* 平均内存工作集
* 平均响应时间
* CPU 时间
* 内存工作集
* 请求

![监视 Web 应用性能](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

有关详细信息，请参阅：

* [监视 Azure 应用服务中的 Web 应用](web-sites-monitor.md)
* [接收警报通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>监视 Web 终结点状态
如果在“标准”定价层中运行 Web 应用，Web 应用允许从三个地理位置监视两个终结点。

终结点监视可从测试 Web URL 的响应时间和运行时间的分布式地理位置配置 Web 测试。 该测试可对 Web URL 执行 HTTP GET 操作，以从每个位置确定响应时间和运行时间。 每个已配置位置每 5 分钟运行一次测试。

将使用 HTTP 响应代码监视运行时间，并且以毫秒为单位计算响应时间。 如果 HTTP 响应代码大于或等于 400 或响应时间超过 30 秒，则监视测试失败。 如果从所有指定的位置监视测试均成功，则终结点被视为可用。

有关详细信息，请参阅[在 Azure 应用服务中监视应用](web-sites-monitor.md)

另外，有关终结点监视的视频，请参阅[保持 Azure 网站运行以及终结点监视 - Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow)。

#### <a name="application-performance-monitoring-using-extensions"></a>使用扩展的应用程序性能监视
还可以使用站点扩展监视应用程序的性能。

每个应用服务 Web 应用都提供了一个可扩展的管理终结点，通过此终结点可使用一组作为站点扩展部署的功能强大的工具。 扩展包括： 

- 源代码编辑器，如 [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx)。 
- 连接的资源（如连接到 Web 应用的 MySQL 数据库）的管理工具。

[Azure Application Insights](/services/application-insights/) 和 [New Relic](/marketplace/partners/newrelic/newrelic/) 是两个可用的性能监视站点扩展工具。 若要使用 New Relic，请在运行时安装代理。 若要使用 Azure Application Insights，请使用 SDK 重新生成代码，还可以安装可访问其他数据的扩展。 SDK 可用于编写代码，以便更详细地监视应用的使用情况和性能。

若要使用 Application Insights，请参阅[在 Web 应用中监视性能](../application-insights/app-insights-web-monitor-performance.md)。

若要使用 New Relic，请参阅 [Azure 上的 New Relic 应用程序性能管理](../store-new-relic-cloud-services-dotnet-application-performance-management.md)。

<a name="collect" />

### <a name="2-collect-data"></a>2.收集数据
Web 应用环境为 Web 服务器和 Web 应用中的日志记录信息提供了诊断功能。 此信息分为 Web 服务器诊断和应用程序诊断。

#### <a name="enable-web-server-diagnostics"></a>启用 Web 服务器诊断
可以启用或禁用以下种类的日志：

* **详细错误日志记录** - 指示故障的 HTTP 状态代码（状态代码 400 或更大数字）的详细错误消息。 其中可能包含有助于确定服务器返回错误代码的原因的信息。
* **失败请求跟踪** - 有关失败请求的详细信息，包括对用于处理请求的 IIS 组件和每个组件所用的时间的跟踪。 在尝试提高 Web 应用性能或查找导致特定 HTTP 错误的问题时，此信息很有用。
* **Web 服务器日志记录** - 使用 W3C 扩展日志文件格式的 HTTP 事务信息。 这在确定总体 Web 应用指标（如处理的请求数量或来自特定 IP 地址的请求数）时非常有用。

#### <a name="enable-application-diagnostics"></a>启用应用程序诊断
可通过多种选项收集 Web 应用中的应用程序性能数据、从 Visual Studio 中实时分析应用程序，或修改应用程序代码以记录更多信息和跟踪。 可以基于针对应用程序的访问权限和通过监视工具观察到的内容选择相关选项。

##### <a name="use-application-insights-profiler"></a>使用 Application Insights Profiler
可以启用 Application Insights Profiler 开始捕获详细的性能跟踪。 需要调查过去发生的问题时，可以访问最多五日天前捕获的跟踪。 只要有权访问 Azure 门户中 Web 应用的 Application Insights 资源，就可以选择此选项。

Application Insights Profiler 提供每个 Web 调用响应时间的统计信息，以及指示哪一行代码导致响应缓慢的跟踪信息。 有时应用服务应用运行速度慢，因为某些代码不是以高性能方式编写的。 示例包括可并行运行的顺序代码和不需要的数据库锁争用。 删除代码中的这些瓶颈会提供应用性能，但是如果不设置详细的跟踪和日志，则很难检测到这些瓶颈。 Application Insights Profiler 收集的跟踪信息有助于识别减慢应用程序速度的代码行，还有助于为应用服务应用解决这一挑战。

 有关详细信息，请参阅[使用 Application Insights 分析实时 Azure Web 应用](../application-insights/app-insights-profiler.md)。

##### <a name="use-remote-profiling"></a>使用远程分析
在 Azure 应用服务中，可以远程分析 Web 应用、API 应用和 WebJob。 如果有权访问 Web 应用资源并且知道如何再现问题，或者知道出现性能问题的确切时间间隔，请选择此选项。

远程分析非常有用，如果进程的 CPU 使用率偏高并且进程运行速度比预期缓慢，或者 HTTP 请求的延迟高于平时，可以远程分析进程并获取 CPU 采样调用堆栈，以分析进程活动和代码繁忙的路径。

有关详细信息，请参阅 [Azure App Service 中的远程分析支持](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service)。

##### <a name="set-up-diagnostic-traces-manually"></a>手动设置诊断跟踪
如果有权访问 Web 应用程序源代码，使用应用程序诊断可以捕获 Web 应用程序生成的信息。 ASP.NET 应用程序可使用 `System.Diagnostics.Trace` 类将信息记录到应用程序诊断日志。 但是需要更改代码并重新部署应用程序。 如果应用在测试环境中运行，则推荐使用此方法。

有关如何在应用程序中配置日志记录的详细说明，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](web-sites-enable-diagnostic-log.md)。

#### <a name="use-the-azure-app-service-support-portal"></a>使用 Azure App Service 支持门户
在 Web 应用中，可通过查看 HTTP 日志、事件日志、进程转储等信息来排查与 Web 应用相关的问题。 可以使用支持门户 (**http://&lt;your app name>.scm.azurewebsites.net/Support**) 访问所有这些信息。

Azure App Service 支持门户具有三个不同的选项卡，用于支持常见故障排除方案的三个步骤：

1. 观察当前行为
2. 通过收集诊断信息和运行内置分析器进行分析
3. 缓解

如果目前正有问题，请单击“分析” > “诊断” > “立即诊断”创建诊断会话，该会话收集 HTTP 日志、事件查看器日志、内存转储、PHP 错误日志和 PHP 进程报告。

完成数据收集后，支持门户对数据运行分析并提供一份 HTML 报告。

如果想要下载数据，数据默认情况下会存储在 D:\home\data\DaaS 文件夹中。

有关 Azure App Service 支持门户的详细信息，请参阅 [New Updates to Support Site Extension for Azure Websites](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites)（用于支持 Azure 网站的站点扩展的最新更新）。

#### <a name="use-the-kudu-debug-console"></a>使用 Kudu 调试控制台
Web 应用随附可用于调试、浏览和上传文件的调试控制台，以及用于获取环境相关信息的 JSON 终结点。 此控制台称为 Web 应用的 Kudu 控制台或 SCM 仪表板。

可以通过转到链接 **https://&lt;Your app name>.scm.azurewebsites.net/** 来访问此仪表板。

Kudu 提供的一些信息和功能包括：

* 应用程序的环境设置
* 日志流
* 诊断转储
* 调试控制台，可以在其中运行 Powershell cmdlet 和基本 DOS 命令。

Kudu 的另一项有用功能是，如果应用程序引发第一次异常，可以使用 Kudu 和 SysInternals 工具 Procdump 创建内存转储。 这些内存转储是进程的快照，通常可帮助排查较复杂的 Web 应用问题。

有关 Kudu 提供的功能的详细信息，请参阅 [Azure Websites Team Services tools you should know about](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)（应该了解的 Azure Websites Team Services 工具）。

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3.缓解问题
#### <a name="scale-the-web-app"></a>缩放 Web 应用
在 Azure 应用服务中，为了提高性能和吞吐量，可以调整运行应用程序的规模。 向上缩放 Web 应用涉及到两个相关操作：将应用服务计划更改为较高的定价层，以及在切换到较高的定价层后配置特定的设置。

有关缩放的详细信息，请参阅[缩放 Azure 应用服务中的 Web 应用](web-sites-scale.md)。

此外，可以选择在多个实例上运行应用程序。 扩展不仅能提供更强大的处理功能，而且还能提供一定程度的容错。 如果进程在某个实例上中断，其他实例会继续处理请求。

可以将缩放设置为手动或自动。

#### <a name="use-autoheal"></a>使用 AutoHeal
AutoHeal 会根据所选设置（例如配置更改、请求、基于内存的限制或执行请求所需的时间）回收应用的工作进程。 在大多数情况下，回收进程是在出现问题后进行恢复的最快方式。 尽管始终都可从 Azure 门户中直接重启 Web 应用，但 AutoHeal 可以自动执行此操作。 只需在 Web 应用的根 web.config 中添加一些触发器即可。 即使应用程序并非 .Net 应用程序，这些设置的工作方式也仍然相同。

有关详细信息，请参阅[自动修复 Azure 网站](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/)。

#### <a name="restart-the-web-app"></a>重新启动 Web 应用
重启通常是在发生一次性问题后进行恢复的最简单方式。 [Azure 门户](https://portal.azure.com/)上的 Web 应用边栏选项卡中提供了用于停止或重启应用的选项。

 ![重新启动 Web 应用以解决性能问题](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

还可以使用 Azure Powershell 管理 Web 应用。 有关详细信息，请参阅[将 Azure PowerShell 与 Azure Resource Manager 配合使用](../powershell-azure-resource-manager.md)。

