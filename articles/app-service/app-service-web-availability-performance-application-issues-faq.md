---
title: "Azure Web 应用的应用程序性能常见问题解答 | Microsoft Docs"
description: "获取有关 Azure 应用服务的 Web 应用功能中的可用性、性能和应用程序问题的常见问题解答。"
services: app-service\web
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 926005eace0acde27e26619a2787368ba0954351
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2018
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Azure 中的 Web 应用的应用程序性能常见问题解答

本文包含有关针对 [Azure 应用服务的 Web 应用功能](https://azure.microsoft.com/services/app-service/web/)的应用程序性能问题的常见问题 (FAQ) 解答。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>为何我的应用速度缓慢？

有多个因素可能会导致应用性能减慢。 有关详细故障排除步骤，请参阅 [Web 应用性能缓慢故障排除](app-service-web-troubleshoot-performance-degradation.md)。

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>如何对高 CPU 使用率情况进行故障排除？

在某些高 CPU 使用率情况下，应用可能的确需要更多计算资源。 在这种情况下，请考虑扩展到较高服务层，以便应用程序可获取所需的所有资源。 在其他时候，错误循环或编码做法可能会导致高 CPU 使用率。 深入了解触发 CPU 使用率提高的因素是包含两个部分的过程。 首先，创建一个进程转储，然后分析该进程转储。 有关详细信息，请参阅[针对 Web 应用的高 CPU 使用率捕获和分析转储文件](https://blogs.msdn.microsoft.com/asiatech/2016/01/20/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app/)。

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>如何对高内存使用率情况进行故障排除？

在某些高内存使用率情况下，应用可能的确需要更多计算资源。 在这种情况下，请考虑扩展到较高服务层，以便应用程序可获取所需的所有资源。 在其他时候，代码中的 bug 可能会导致内存泄漏。 编码做法也可能会提高内存使用率。 深入了解触发高内存使用率的因素是包含两个部分的过程。 首先，创建一个进程转储，然后分析该进程转储。 Azure 站点扩展库中的故障诊断程序可以高效地执行这两个步骤。 有关详细信息，请参阅[针对 Web 应用的间歇性高内存使用率捕获和分析转储文件](https://blogs.msdn.microsoft.com/asiatech/2016/02/02/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app/)。

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>如何使用 PowerShell 自动执行应用服务 Web 应用？

可以使用 PowerShell cmdlet 管理和维护应用服务 Web 应用。 在我们的博客文章[使用 PowerShell 自动执行在 Azure 应用服务中承载的 Web 应用](https://blogs.msdn.microsoft.com/puneetgupta/2016/03/21/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way/)中，我们介绍了如何使用基于 Azure 资源管理器的 PowerShell cmdlet 自动执行常见任务。 该博客文章还包含针对各种 Web 应用管理任务的示例代码。 有关所有应用服务 Web 应用 cmdlet 的说明和语法，请参阅 [AzureRM.Websites](https://docs.microsoft.com/powershell/module/azurerm.websites/?view=azurermps-4.0.0)。

## <a name="how-do-i-view-my-web-apps-event-logs"></a>如何查看 Web 应用的事件日志？

查看 Web 应用的事件日志：

1. 登录到 [Kudu 网站](https://*yourwebsitename*.scm.azurewebsites.net)。
2. 在菜单中，选择“调试控制台” > “CMD”。
3. 选择 **LogFiles** 文件夹。
4. 若要查看事件日志，请选择 **eventlog.xml** 旁的铅笔图标。
5. 若要下载日志，请运行 PowerShell cmdlet `Save-AzureWebSiteLog -Name webappname`。

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>如何捕获 Web 应用的用户模式内存转储？

捕获 Web 应用的用户模式内存转储:

1. 登录到 [Kudu 网站](https://*yourwebsitename*.scm.azurewebsites.net)。
2. 选择“进程资源管理器”菜单。
3. 右键单击 **w3wp.exe** 进程或 Web 作业进程。
4. 选择“下载内存转储” > “完全转储”。

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>如何查看 Web 应用的进程级信息？

有两个选项可用于查看 Web 应用的进程级信息：

*   在 Azure 门户中：
    1. 打开 Web 应用的“进程资源管理器”。
    2. 若要查看详细信息，请选择 **w3wp.exe** 进程。
*   在 Kudu 控制台中：
    1. 登录到 [Kudu 网站](https://*yourwebsitename*.scm.azurewebsites.net)。
    2. 选择“进程资源管理器”菜单。
    3. 对于 **w3wp.exe** 进程，选择“属性”。

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>当我浏览到我的应用时，看到“错误 403 - 此 Web 应用已停止”。 如何解决此问题？

有三种状况可能会导致此错误：

* Web 应用已达到计费限制，站点已禁用。
* 在门户中停止了 Web 应用。
* Web 应用已达到可能应用于免费或共享缩放服务计划的资源配额限制。

若要了解导致错误的原因并解决问题，请按照 [Web 应用：“错误 403 – 此 Web 应用已停止”](https://blogs.msdn.microsoft.com/waws/2016/01/05/azure-web-apps-error-403-this-web-app-is-stopped/)中的步骤执行。

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>我可以在何处详细了解各种应用服务计划的配额和限制？

有关配额和限制的信息，请参阅[应用服务限制](../azure-subscription-service-limits.md#app-service-limits)。 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>如何减少空闲时间之后第一个请求的响应时间？

默认情况下，Web 应用如果已处于空闲状态达到一定时间，则会卸载。 这样可以让系统节省资源。 其缺点是 Web 应用卸载之后的第一个请求的响应时间会较长，以使 Web 应用可以加载并启动服务响应。 在基本和标准服务计划中，可以打开“始终可用”设置以使应用始终加载。 这可消除应用处于空闲状态之后的较长加载时间。 更改“始终可用”设置：

1. 在 Azure 门户中，转到你的 Web 应用。
2. 选择“应用程序设置”。
3. 对于“始终可用”，选择“打开”。

## <a name="how-do-i-turn-on-failed-request-tracing"></a>如何打开失败请求跟踪？

打开失败请求跟踪：

1. 在 Azure 门户中，转到你的 Web 应用。
3. 选择“所有设置” > “诊断日志”。
4. 对于“失败请求跟踪”，选择“打开”。
5. 选择“保存”。
6. 在 Web 应用边栏选项卡，选择“工具”。
7. 选择“Visual Studio Online”。
8. 如果设置不是“打开”，则选择“打开”。
9. 选择“转到”。
10. 选择 **Web.config**。
11. 在 system.webServer 中，添加此配置（用于捕获特定 URL）：

    ```
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. 若要对性能缓慢问题进行故障排除，请添加此配置（如果捕获请求时间超过 30 秒）：
    ```
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. 若要下载失败请求跟踪，请在[门户](https://portal.azure.com)中，转到你的网站。
15. 选择“工具” > “Kudu” > “转到”。
18. 在菜单中，选择“调试控制台” > “CMD”。
19. 选择 **LogFiles** 文件夹，然后选择名称以 **W3SVC** 开头的文件夹。
20. 若要查看 XML 文件，请选择铅笔图标。

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>我看到消息“请求的工作进程由于‘内存百分比’限制而回收”。 如何解决此问题？

32 位进程的最大可用内存量（即使是在 64 位操作系统上）是 2 GB。 默认情况下，工作进程在应用服务中设置为 32 位（以便与旧 Web 应用程序兼容）。

可考虑切换为 64 位进程，以便可以利用 Web 辅助角色中可用的额外内存。 这会触发 Web 应用重新启动，因此请相应地进行计划。

另请注意，64 位环境需要基本或标准服务计划。 免费和共享计划始终在 32 位环境中运行。

有关详细信息，请参阅[在应用服务中配置 Web 应用](web-sites-configure.md)。

## <a name="why-does-my-request-time-out-after-240-seconds"></a>为何我的请求在 240 秒后超时？

Azure 负载均衡器的默认空闲超时设置为四分钟。 这通常是针对 Web 请求的合理响应时间限制。 如果你的 Web 应用需要后台处理，则建议使用 Azure Web 作业。 Azure Web 应用可以调用 Web 作业，并在后台处理完成时收到通知。 可以在多种 Web 作业使用方法（包括队列和触发器）中进行选择。

Web 作业旨在用于后台处理。 可以在 Web 作业中根据需要执行后台处理。 有关 Web 作业的详细信息，请参阅[使用 Web 作业运行后台任务](web-sites-create-web-jobs.md)。

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>在应用服务中承载的 ASP.NET Core 应用程序有时会停止响应。 如何修复此问题？

与早期 [Kestrel 版本](https://github.com/aspnet/KestrelHttpServer/issues/1182)有关的一个已知问题可能会在应用服务中承载的 ASP.NET Core 1.0 应用间歇性停止响应。 还可能会看到以下消息：“指定的 CGI 应用程序遇到错误，服务器终止了该进程”。

在 Kestrel 版本 1.0.2 中已修复了此问题。 此版本包含在 ASP.NET Core 1.0.3 更新中。 若要解决此问题，请确保将应用依赖关系更新为使用 Kestrel 1.0.2。 或者，可以使用博客文章[应用服务 Web 应用中的 ASP.NET Core 1.0 性能缓慢问题](https://blogs.msdn.microsoft.com/waws/2016/12/11/asp-net-core-slow-perf-issues-on-azure-websites)中介绍的两个解决方法之一。


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>我在我的 Web 应用文件结构中找不到日志文件。 如何才能找到它们？

如果使用应用服务的本地缓存功能，则应用服务实例的日志文件和数据文件夹的文件夹结构会受影响。 使用本地缓存时，会在存储日志文件和数据文件夹中创建子文件夹。 子文件夹使用命名模式“唯一标识符”+ 时间戳。 每个子文件夹都对应于一个 VM 实例，其中的 Web 应用正在运行或已运行。

若要确定是否在使用本地缓存，请检查应用服务“应用程序设置”选项卡。如果使用本地缓存，则应用设置 `WEBSITE_LOCAL_CACHE_OPTION` 设置为 `Always`。

如果未使用本地缓存并且遇到此问题，请提交支持请求。

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>我看到消息“以一种访问权限不允许的方式做了一个访问套接字的尝试”。 如何解决此问题？

如果 VM 实例上的出站 TCP 连接耗尽，通常会发生此错误。 在应用服务中，会对可以为每个 VM 实例进行的最大出站连接数实施限制。 有关详细信息，请参阅[跨 VM 数值限制](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)。

如果尝试从应用程序访问本地地址，也可能会发生此错误。 有关详细信息，请参阅[本地地址请求](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests)。

有关 Web 应用中的出站连接的详细信息，请参阅有关[到 Azure 网站的传出连接](http://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/)的博客文章。

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>如何使用 Visual Studio 远程调试应用服务Web 应用？

有关演示如何使用 Visual Studio 调试 Web 应用的详细演练，请参阅[远程调试应用服务 Web 应用](https://blogs.msdn.microsoft.com/benjaminperkins/2016/09/22/remote-debug-your-azure-app-service-web-app/)。
