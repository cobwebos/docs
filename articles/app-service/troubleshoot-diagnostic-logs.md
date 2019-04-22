---
title: 为应用启用诊断日志记录 - Azure 应用服务
description: 了解如何启用诊断日志记录并将检测添加到应用程序，以及如何访问由 Azure 记录的信息。
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 37455c278d665d05636ec120ca91b76153e53d16
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58894912"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>为 Azure 应用服务中的应用启用诊断日志记录
## <a name="overview"></a>概述
Azure 提供内置诊断功能，可帮助调试[应用服务应用](https://go.microsoft.com/fwlink/?LinkId=529714)。 在本文中，将了解如何启用诊断日志记录并将检测添加到应用程序，以及如何访问由 Azure 记录的信息。

本文使用 [Azure 门户](https://portal.azure.com)和 Azure CLI 来处理诊断日志。 有关通过 Visual Studio 使用诊断日志的信息，请参阅[在 Visual Studio 中对 Azure 进行故障排除](troubleshoot-dotnet-visual-studio.md)。

## <a name="whatisdiag"></a>Web 服务器诊断和应用程序诊断
应用服务为 Web 服务器和 Web 应用程序中的日志记录信息提供诊断功能。 这些诊断功能按逻辑分为 **Web 服务器诊断**和**应用程序诊断**。

### <a name="web-server-diagnostics"></a>Web 服务器诊断
可以启用或禁用以下种类的日志：

* **详细错误日志记录** - 任何会生成 HTTP 状态代码 400（或更大数字）的请求的详细信息。 其中可能包含有助于确定服务器返回错误代码的原因的信息。 会为应用的文件系统中的每个错误生成一个 HTML 文件，并可保留最多 50 个错误（文件）。 当 HTML 文件的数目超出 50 时，最旧的 26 个文件会自动删除。
* **失败请求跟踪** - 有关失败请求的详细信息，包括对用于处理请求的 IIS 组件和每个组件所用的时间的跟踪。 如果要提高站点性能或隔离特定的 HTTP 错误，这将非常有用。 会在应用的文件系统中为每个错误生成一个文件夹。 文件保留策略与上述详细错误日志记录的相同。
* **Web 服务器日志记录** - 使用 [W3C 扩展日志文件格式](/windows/desktop/Http/w3c-logging)的 HTTP 事务信息。 这在确定整体站点指标（如处理的请求数量或来自特定 IP 地址的请求数）时非常有用。

### <a name="application-diagnostics"></a>应用程序诊断
应用程序诊断可以捕获由 Web 应用程序产生的信息。 ASP.NET 应用程序可使用 [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) 类将信息记录到应用程序诊断日志。 例如：

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

在运行时，可以检索这些日志帮助进行故障排除。 有关详细信息，请参阅[在 Visual Studio 中对 Azure 应用服务进行故障排除](troubleshoot-dotnet-visual-studio.md)。

将内容发布到某个应用时，应用服务还记录部署信息。 此操作自动执行，不会对部署日志记录进行配置设置。 部署日志记录允许确定部署失败的原因。 例如，如果使用自定义部署脚本，可能会使用部署日志记录确定该脚本失败的原因。

## <a name="enablediag"></a>如何启用诊断
若要在 [Azure 门户](https://portal.azure.com)中启用诊断，请转到应用的相应页面，并依次单击“设置”>“诊断日志”。

<!-- todo:cleanup dogfood addresses in screenshot -->
![日志部分](./media/web-sites-enable-diagnostic-log/logspart.png)

启用“应用程序诊断”时，还需选择“级别”。 下表显示了每个级别包含的日志类别：

| 级别| 包含的日志类别 |
|-|-|
|**已禁用** | 无 |
|**错误** | “错误”、“严重” |
|**警告** | “警告”、“错误”、“严重”|
|**信息** | “信息”、“警告”、“错误”、“严重”|
|**详细** | “跟踪”、“调试”、“信息”、“警告”、“错误”、“严重”（所有类别） |
|-|-|

对于“应用程序日志记录”，可以临时打开文件系统选项以便调试。 此选项将在 12 小时后自动关闭。 还可打开 blob 存储选项，选择要将日志写入到的 blob 容器。

> [!NOTE]
> 目前，只有 .NET 应用程序日志可以写入到 blob 存储。 Java、PHP、Node.js、Python 应用程序日志只能存储在文件系统上（无需修改代码即可将日志写入外部存储）。
>
>

对于“Web 服务器日志记录”，可选择“存储”或“文件系统”。 选择“存储”可选择存储帐户，然后选择日志写入到的 Blob 容器。 

如果将日志存储在文件系统中，可通过 FTP 访问这些文件，或使用 Azure CLI 将这些文件作为 Zip 存档下载。

默认情况下，日志不会自动删除（除非是“应用程序日志记录(文件系统)”）。 要自动删除日志，请设置“保留期(天)”字段。

> [!NOTE]
> 如果[重新生成存储帐户的访问密钥](../storage/common/storage-create-storage-account.md)，则必须重置相应的日志记录配置才能使用更新的密钥。 为此，请按以下步骤操作：
>
> 1. 在“配置”选项卡上，将相应的日志记录功能设置为“关闭”。 保存设置。
> 2. 再次启用将日志记录到存储帐户 Blob。 保存设置。
>
>

可同时启用文件系统或 Blob 存储的任意组合，并采用单独的日志级别配置。 例如，你可能希望将错误和警告记录到 Blob 存储中作为长期的日志记录解决方案，同时将文件系统日志记录级别设置为“详细”。

尽管这两个存储位置都可为记录事件提供相同的基本信息，但与记录到**文件系统**相比，**Blob 存储**还会记录其他信息，例如实例 ID、线程 ID 以及更详细的时间戳（刻度格式）。

> [!NOTE]
> 只能使用存储客户端访问或由直接使用这些存储系统的应用程序访问存储在 **Blob 存储**中的信息。 例如，Visual Studio 2013 包含的存储资源管理器可用于浏览 Blob 存储，而 HDInsight 可以访问存储在 Blob 存储中的数据。 还可编写通过使用 [Azure SDK](https://azure.microsoft.com/downloads/) 之一访问 Azure 存储的应用程序。
>

## <a name="download"></a> 如何：下载日志
可使用 FTP 直接访问存储到应用文件系统的诊断信息。 还可以使用 Azure CLI 将其作为 Zip 存档下载。

存储日志采用的目录结构如下：

* **应用程序日志** - /LogFiles/Application/。 此文件夹包含一个或多个包含应用程序日志记录生成的信息的文本文件。
* **失败请求跟踪** - /LogFiles/W3SVC#########/。 此文件夹包含一个 XSL 文件和一个或多个 XML 文件。 请确保将 XSL 文件下载到 XML 文件所在的目录，因为 XSL 文件可提供格式化和筛选这些文件的内容的功能（在 Internet Explorer 中查看 XML 文件时）。
* **详细错误日志** - /LogFiles/DetailedErrors/。 此文件夹包含一个或多个 .htm 文件，这些文件可提供大量有关所有已出现 HTTP 错误的信息。
* **Web 服务器日志** - /LogFiles/http/RawLogs。 此文件夹包含使用 [W3C 扩展日志文件格式](/windows/desktop/Http/w3c-logging)进行格式化的一个或多个文本文件。
* **部署日志** - /LogFiles/Git。 此文件夹包含由 Azure 应用服务所用的内部部署过程生成的日志和 Git 部署的日志。 此外，还可在 D:\home\site\deployments 下找到部署日志。

### <a name="ftp"></a>FTP

要开启到应用的 FTP 服务器的 FTP 连接，请参阅[使用 FTP/S 将应用部署到 Azure App Service](deploy-ftp.md)。

一旦连接到应用的 FTP/S 服务器，请打开 **LogFiles** 文件夹，所有日志文件都存储于此。

### <a name="download-with-azure-cli"></a>使用 Azure CLI 下载
若要使用 Azure 命令行接口下载日志文件，请打开新的命令提示符、PowerShell、Bash 或终端会话，并输入以下命令：

    az webapp log download --resource-group resourcegroupname --name appname

此命令将名为“appname”的应用的日志保存到当前目录中名为 **webapp_logs.zip** 的文件。

> [!NOTE]
> 如果尚未安装 Azure CLI，或尚未将其配置为使用你的 Azure 订阅，请参阅[如何使用 Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)。
>
>

## <a name="how-to-view-logs-in-application-insights"></a>如何：在 Application Insights 中查看日志
Visual Studio Application Insights 可提供用于筛选和搜索日志的工具，以及用于将日志与请求和其他事件关联的工具。

1. 在 Visual Studio 中，将 Application Insights SDK 添加到项目。
   * 在解决方案资源管理器中，右键单击项目并选择“添加 Application Insights”。 界面会引导你完成所有步骤，包括创建 Application Insights 资源。 [了解详细信息](../azure-monitor/app/asp-net.md)
2. 将跟踪侦听器包添加到项目。
   * 右键单击项目，并选择“管理 NuGet 包”。 选择 `Microsoft.ApplicationInsights.TraceListener` [了解详细信息](../azure-monitor/app/asp-net-trace-logs.md)
3. 上传项目并运行，以生成日志数据。
4. 在 [Azure 门户](https://portal.azure.com/)中，浏览到新的 Application Insights 资源，并打开“搜索”。 随后应会显示日志数据，以及请求、使用情况和其他遥测。 某些遥测可能需要几分钟才能到达：请单击“刷新”。 [了解详细信息](../azure-monitor/app/diagnostic-search.md)

[了解有关使用 Application Insights 跟踪性能的详细信息](../azure-monitor/app/azure-web-apps.md)

## <a name="streamlogs"></a> 如何：流式传输日志
开发应用程序时，以近乎实时的方式查看日志记录信息通常很有用。 可以使用 Azure CLI 将日志记录信息流式传输到开发环境。

> [!NOTE]
> 某些类型的日志记录缓冲区会对日志文件执行写入操作，这可能会导致流中的事件变成混乱。 例如，用户访问页面时出现的应用程序日志项可能显示在该页面请求所对应的 HTTP 日志项的前面。
>
> [!NOTE]
> 日志流式传输还将对写入存储在 **D:\\home\\LogFiles\\** 文件夹中的任何文本文件的信息进行流式传输。
>
>

### <a name="streaming-with-azure-cli"></a>使用 Azure CLI 流式传输
若要流式传输日志记录信息，请打开新的命令行提示、PowerShell、Bash 或终端会话并输入以下命令：

    az webapp log tail --name appname --resource-group myResourceGroup

此命令连接到名为“appname”的应用，并在该应用上出现日志事件时开始将信息流式传输到窗口。 写入以 .txt、.log 或 .htm 结尾并存储在 /LogFiles 目录 (d:/home/logfiles) 中文件的所有信息将流式传输至本地控制台。

若要筛选特定事件（如错误），请使用 **--Filter** 参数。 例如：

    az webapp log tail --name appname --resource-group myResourceGroup --filter Error

若要筛选特定日志类型（如 HTTP），请使用 **-Path** 参数。 例如：

    az webapp log tail --name appname --resource-group myResourceGroup --path http

> [!NOTE]
> 如果尚未安装 Azure CLI，或尚未将其配置为使用你的 Azure 订阅，请参阅[如何使用 Azure CLI](../cli-install-nodejs.md)。
>
>

## <a name="understandlogs"></a> 如何：了解诊断日志
### <a name="application-diagnostics-logs"></a>应用程序诊断日志
应用程序诊断将信息以特定格式存储在 .NET 应用程序中，具体取决于是将日志存储到文件系统还是 Blob 存储。 

这两种存储类型存储的基本数据信息相同 — 事件发生的日期和时间，生成事件的进程 ID，事件类型（信息、警告、错误）以及事件消息。 如果需要立即访问日志以排查某个问题，则使用文件系统作为日志存储会很有帮助，因为日志文件几乎是即时更新的。 Blob 存储用于存档目的，因为这些文件将会缓存，并按计划刷新到存储容器。

**文件系统**

记录到文件系统或使用流式传输收到的每行都会采用以下格式：

    {Date}  PID[{process ID}] {event type/level} {message}

例如，错误事件可能类似如下所示：

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

记录到文件系统可提供三种可用方法的最基本信息，仅提供时间、进程 ID、事件级别以及消息。

**Blob 存储**

如果记录到 Blob 存储，数据以逗号分隔值 (CSV) 格式存储。 将记录其他字段以提供有关事件的更详尽信息。 以下属性适用于每一行（CSV 格式）：

| 属性名称 | 值/格式 |
| --- | --- |
| 日期 |事件发生的日期和时间 |
| 级别 |事件级别（例如“错误”、“警告”或“信息”） |
| ApplicationName |应用名称 |
| InstanceId |发生事件的应用实例 |
| EventTickCount |事件发生的日期和时间，刻度格式（精度更高） |
| EventId |此事件的事件 ID<p><p>如果未指定，默认为 0 |
| Pid |进程 ID |
| Tid |生成事件的线程的线程 ID |
| 消息 |事件详细消息 |

存储在 blob 中的数据如下方示例所示：

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> 对于 ASP.NET Core，可以使用 [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 提供程序实现日志记录。此提供程序会将其他日志文件保存到 Blob 容器。 有关详细信息，请参阅 [Azure 中的 ASP.NET Core 日志记录](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#logging-in-azure)。
>
>

### <a name="failed-request-traces"></a>失败请求跟踪
失败请求跟踪存储在名为 **fr######.xml** 的 XML 文件中。 为了便于查看记录信息，在 XML 文件所在目录中提供了一个名为 **freb.xsl** 的 XSL 样式表。 如果在 Internet Explorer 中打开其中一个 XML 文件，Internet Explorer 会使用 XSL 样式表提供易于阅读的跟踪信息，如下方示例所示：

![在浏览器中查看失败请求](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

> [!NOTE]
> 查看带格式的失败的请求跟踪的简单办法是导航到在门户中的应用程序页面。 从左侧菜单中，选择**诊断并解决问题**，然后搜索**失败请求跟踪日志**，然后单击图标来浏览和查看所需的跟踪。
>

### <a name="detailed-error-logs"></a>详细的错误日志
详细的错误日志是 HTML 文档，可提供有关发生的 HTTP 错误的详细信息。 由于它们只是 HTML 文档，所以可以使用 Web 浏览器查看。

### <a name="web-server-logs"></a>Web 服务器日志
可使用 [W3C 扩展日志文件格式](/windows/desktop/Http/w3c-logging)格式化 Web 服务器日志。 可使用文本编辑器读取此信息，或使用诸如[日志分析程序](https://go.microsoft.com/fwlink/?LinkId=246619)等实用工具进行解析。

> [!NOTE]
> Azure 应用服务生成的日志不支持 **s-computername**、**s-ip** 或 **cs-version** 字段。
>
>

## <a name="nextsteps"></a>后续步骤
* [如何监视 Azure 应用服务](web-sites-monitor.md)
* [在 Visual Studio 中对 Azure 应用服务进行故障排除](troubleshoot-dotnet-visual-studio.md)
* [在 HDInsight 中分析应用日志](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
