---
title: 应用程序性能常见问题解答
description: 获取有关 Azure 应用服务中的可用性、性能和应用程序问题的常见问题解答。
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: dfaeee6a6e2a9728d7e63fb5681c487fbbd6139e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88958989"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Azure 中 Web 应用的应用程序性能常见问题解答

> [!NOTE]
> 下面的某些准则可能仅适用于 Windows 或 Linux 应用服务。 例如，默认情况下，Linux 应用服务在64位模式下运行。
>

本文对 [Azure 应用服务 Web 应用功能](https://azure.microsoft.com/services/app-service/web/)的应用程序性能常见问题 (FAQ) 进行了解答。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>为何我的应用运行速度缓慢？

有多种因素可能导致应用性能降低。 有关问题排查的详细步骤，请参阅 [Troubleshoot slow web app performance](troubleshoot-performance-degradation.md)（排查导致 Web 应用性能降低的问题）。

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>如何解决 CPU 占用高的问题？

在某些 CPU 占用高的情况下，应用可能真的需要更多计算资源。 在这种情况下，请考虑扩展到较高服务层级，以便应用程序可获取所需的所有资源。 其他情况下，高 CPU 占用可能是由错误循环或编码实践导致的。 深入了解 CPU 占用升高的触发因素这一过程分为两部分。 首先，创建进程转储，然后分析此进程转储。 有关详细信息，请参阅[捕获和分析 Web 应用高 CPU 占用的转储文件](/archive/blogs/asiatech/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app)。

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>如何解决内存占用高的问题？

在某些内存占用高的情况下，应用可能真的需要更多计算资源。 在这种情况下，请考虑扩展到较高服务层级，以便应用程序可获取所需的所有资源。 在其他情况下，代码中存在的 bug 可能会导致内存泄漏。 此外，编码实践也可能会增大内存占用。 深入了解内存占用高的触发因素这一过程分为两部分。 首先，创建进程转储，然后分析此进程转储。 Azure 站点扩展库中的故障诊断程序可高效执行这两个步骤。 有关详细信息，请参阅[捕获和分析 Web 应用间歇性高内存的转储文件](/archive/blogs/asiatech/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app)。

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>如何使用 PowerShell 实现应用服务 Web 应用的自动化？

可以使用 PowerShell cmdlet 管理和维护应用服务 Web 应用。 在我们的博客文章 [Automate web apps hosted in Azure App Service by using PowerShell](/archive/blogs/puneetgupta/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way)（使用 PowerShell 实现 Azure 应用服务中托管的 Web 应用的自动化）中，我们将说明如何使用基于 Azure Resource Manager 的 PowerShell cmdlet 自动执行常见任务。 此博客文章中还包含适用于各种 Web 应用管理任务的示例代码。 有关所有应用服务 Web 应用 cmdlet 的说明和语法，请参阅 [Az.Websites](/powershell/module/az.websites)。

## <a name="how-do-i-view-my-web-apps-event-logs"></a>如何查看 Web 应用的事件日志？

查看 Web 应用的事件日志：

1. 登录到 Kudu 网站 (`https://*yourwebsitename*.scm.azurewebsites.net`)。
2. 在菜单中，选择“调试控制台” > “CMD”。
3. 选择“LogFiles”文件夹。
4. 若要查看事件日志，请选择 **eventlog.xml** 旁的铅笔图标。
5. 若要下载日志，请运行 PowerShell cmdlet `Save-AzureWebSiteLog -Name webappname`。

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>如何捕获 Web 应用的用户模式内存转储？

若要捕获 Web 应用的用户模式内存转储，请执行以下操作：

1. 登录到 Kudu 网站 (`https://*yourwebsitename*.scm.azurewebsites.net`)。
2. 选择“进程资源管理器”菜单。
3. 右键单击“w3wp.exe”进程或 WebJob 进程。
4. 选择“下载内存转储” > “完全转储”。

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>如何查看 Web 应用的进程级信息？

可通过两种方法查看 Web 应用的进程级信息：

*   在 Azure 门户中：
    1. 打开 Web 应用的“进程资源管理器”。
    2. 若要查看详细信息，请选择“w3wp.exe”进程。
*   在 Kudu 控制台中：
    1. 登录到 Kudu 网站 (`https://*yourwebsitename*.scm.azurewebsites.net`)。
    2. 选择“进程资源管理器”菜单。
    3. 对于“w3wp.exe”进程，选择“属性”。

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>浏览到应用时，看到“错误 403 - 此 web 应用已停止。” 如何解决此问题？

有三种情况可能导致此错误：

* Web 应用已达到计费限制，站点已被禁用。
* 已在门户中停止 Web 应用。
* Web 应用已达到资源配额限制，该限制可能适用于免费或共享缩放服务计划。

若要了解导致错误的原因并解决问题，请按照 [Web 应用：“错误 403 - 此 Web 应用已停止”](/archive/blogs/waws/azure-web-apps-error-403-this-web-app-is-stopped)中的步骤执行。

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>可从何处了解有关各种应用服务计划的配额和限制的详细信息？

有关配额和限制的信息，请参阅[应用服务限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>如何缩短空闲时间后第一个请求的响应时间？

默认情况下，如果 Web 应用已处于空闲状态相当一段时间，则其处于未加载的状态。 这样，系统可以节省资源。 其缺点是：Web 应用处于未加载的状态后，对第一个请求的响应时间较长，需要等待 Web 应用加载和启动处理响应。 在基本和标准服务计划中，可启用“始终打开”设置，使应用保持加载状态。 这样就无需在应用处于空闲状态后重新加载应用。 若要更改“始终打开”设置，请执行以下操作：

1. 在 Azure 门户中，转到自己的 Web 应用。
2. 选择“配置”
3. 选择“常规设置”。
4. 对于“始终打开”，选择“打开”。

## <a name="how-do-i-turn-on-failed-request-tracing"></a>如何打开失败请求跟踪？

若要打开失败的请求跟踪，请执行以下操作：

1. 在 Azure 门户中，转到自己的 Web 应用。
3. 选择“所有设置” > “诊断日志”。
4. 对于“失败的请求跟踪”，选择“打开”。
5. 选择“保存” 。
6. 在 Web 应用边栏选项卡，选择“工具”。
7. 选择“Visual Studio Online”。
8. 如果设置不是“打开”，则选择“打开”。
9. 选择“转到”。
10. 选择“Web.config”。
11. 在 system.webServer 中，添加此配置（以捕获特定的 URL）：

    ```xml
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
12. 若要解决性能较低的问题，请添加此配置（如果捕获请求话费的时间超过 30 秒）：
    ```xml
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
13. 若要下载失败的请求跟踪，请在[门户](https://portal.azure.com)中转到你的网站。
15. 选择“工具” > “Kudu” > “转到”。
18. 在菜单中，选择“调试控制台” > “CMD”。
19. 选择“LogFiles”文件夹，然后选择名称以“W3SVC”开头的文件夹。
20. 若要查看 XML 文件，请选择铅笔图标。

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>看到消息“由于‘内存百分比’限制工作进程请求回收。” 如何解决此问题？

对于 32 位的进程（即使是在 64 位操作系统中），最大可用内存量是 2 GB。 默认情况下，应用服务中的工作进程设置为 32 位（以便与旧版 Web 应用程序兼容）。

请考虑切换为 64 位进程，以便利用 Web 辅助角色中的其他可用内存。 这将触发 Web 应用重启，因此请进行相应安排。

另请注意，64 位环境需要“基本”或“标准”服务计划。 “免费”和“共享”计划始终在 32 位环境中运行。

有关详细信息，请参阅[在应用服务中配置 web 应用](configure-common.md)。

## <a name="why-does-my-request-time-out-after-230-seconds"></a>为何我的请求在 230 秒后超时？

Azure 负载均衡器的默认空闲超时设置为四分钟。 这通常是 Web 请求合理的响应时间限制。 如果 Web 应用需要后台处理，建议使用 Azure WebJobs。 Azure Web 应用可以调用 WebJobs，并在后台处理完成时收到通知。 有多种方法可用于使用 WebJobs，包括队列和触发器。

WebJobs 专用于后台处理。 可在 WebJobs 中执行任意数量的后台处理。 有关 WebJobs 的详细信息，请参阅[使用 WebJobs 运行后台任务](webjobs-create.md)。

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>应用服务中托管的 ASP.NET Core 应用程序有时会停止响应。 如何解决此问题？

早期 [Kestrel 版本](https://github.com/aspnet/KestrelHttpServer/issues/1182)的已知问题可能会导致托管于应用服务中的 ASP.NET Core 1.0 应用间歇性地停止响应。 还可能会看到以下消息：“指定的 CGI 应用程序遇到错误，服务器终止了该进程”。

已在 Kestrel 版本 1.0.2 中修复了此问题。 此版本包含在 ASP.NET Core 1.0.3 更新中。 若要解决此问题，请确保将你的应用依赖项更新为使用 Kestrel 1.0.2。 或者，可以使用博客文章 [ASP.NET Core 1.0 slow perf issues in App Service web apps](/archive/blogs/waws/asp-net-core-slow-perf-issues-on-azure-websites)（应用服务 Web 应用中 ASP.NET Core 1.0 低性能问题）中介绍的两种解决方法之一。


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>在 Web 应用的文件结构中找不到日志文件。 如何找到它们？

如果使用应用服务的本地缓存功能，应用服务实例“LogFiles 和数据”文件夹的文件夹结构会受到影响。 使用本地缓存时，将在存储 LogFiles 和数据文件夹中创建子文件夹。 子文件夹使用“唯一标识符”+ 时间戳的命名模式。 每个子文件夹对应于一个 VM 实例，其中的 Web 应用正在运行或已运行。

若要确定是否是在本地缓存，请检查应用服务的“应用程序设置”选项卡。如果在使用本地缓存，应用设置 `WEBSITE_LOCAL_CACHE_OPTION` 设置为 `Always`。

如果未使用本地缓存，并且遇到此问题，请提交支持请求。

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>我看到消息“以一种访问权限不允许的方式做了一个访问套接字的尝试。” 如何解决此问题？

将发生此错误通常是因为 VM 实例上的出站 TCP 连接耗尽。 在应用服务中，将强制限制每个 VM 实例的最大出站连接数。 有关详细信息，请参阅[跨 VM 数字限制](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)。

如果尝试从应用程序访问本地地址，也可能发生此错误。 有关详细信息，请参阅[本地地址请求](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests)。

有关 Web 应用中的出站连接的详细信息，请参阅有关[到 Azure 网站的传出连接](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/)的博客文章。

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>如何使用 Visual Studio 远程调试应用服务 Web 应用？

有关如何使用 Visual Studio 调试 Web 应用的详细演练，请参阅 [Remote debug your App Service web app](/archive/blogs/benjaminperkins/remote-debug-your-azure-app-service-web-app)（远程调试应用服务 Web 应用）。