---
title: 启用诊断日志记录
description: 了解如何启用诊断日志记录并将检测添加到应用程序，以及如何访问由 Azure 记录的信息。
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.topic: article
ms.date: 09/17/2019
ms.custom: seodec18
ms.openlocfilehash: e945fd77c2615e6f5213a9aa4fc996f0c4d2f3dd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81769985"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>为 Azure 应用服务中的应用启用诊断日志记录
## <a name="overview"></a>概述
Azure 提供内置诊断功能，可帮助调试[应用服务应用](overview.md)。 在本文中，将了解如何启用诊断日志记录并将检测添加到应用程序，以及如何访问由 Azure 记录的信息。

本文使用 [Azure 门户](https://portal.azure.com)和 Azure CLI 来处理诊断日志。 有关通过 Visual Studio 使用诊断日志的信息，请参阅[在 Visual Studio 中对 Azure 进行故障排除](troubleshoot-dotnet-visual-studio.md)。

> [!NOTE]
> 除了本文中所述的日志记录功能以外，Azure 监视还提供新的集成式日志记录功能。 可以在[将日志发送到 Azure Monitor（预览版）](#send-logs-to-azure-monitor-preview)部分找到有关此功能的详细信息。 
>
>

|类型|平台|位置|说明|
|-|-|-|-|
| 应用程序日志记录 | Windows、Linux | 应用服务文件系统和/或 Azure 存储 Blob | 记录应用程序代码生成的消息。 这些消息可能由所选的 Web 框架生成，或者由应用程序代码使用你的语言的标准日志记录模式直接生成。 为每条消息分配以下类别之一：“严重”、“错误”、“警告”、“信息”、“调试”和“跟踪”。       启用应用程序日志记录时，可以通过设置严重性级别来选择日志记录的详细程度。|
| Web 服务器日志记录| Windows | 应用服务文件系统或 Azure 存储 Blob| 采用 [W3C 扩展日志文件格式](/windows/desktop/Http/w3c-logging)的原始 HTTP 请求数据。 每条日志消息包含 HTTP 方法、资源 URI、客户端 IP、客户端端口、用户代理、响应代码等数据。 |
| 详细错误消息| Windows | 应用服务文件系统 | 已发送到客户端浏览器的 *.htm* 错误页副本。 出于安全原因，不应将详细错误页发送到生产环境中的客户端，但每当出现 HTTP 代码为 400 或更高的应用程序错误时，应用服务都可以保存错误页。 该页可能包含有助于确定服务器返回错误代码的原因的信息。 |
| 失败请求跟踪 | Windows | 应用服务文件系统 | 有关失败请求的详细跟踪信息，包括对用于处理请求的 IIS 组件和每个组件所用的时间的跟踪。 如果要提高站点性能或隔离特定的 HTTP 错误，这将非常有用。 为每个失败的请求生成一个文件夹，其中包含 XML 日志文件，以及用于查看日志文件的 XSL 样式表。 |
| 部署日志记录 | Windows、Linux | 应用服务文件系统 | 有关何时将内容发布到应用的日志。 部署日志记录会自动发生，它没有可配置的设置。 它可以帮助确定部署失败的原因。 例如，如果使用[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)，你可能会使用部署日志记录确定该脚本失败的原因。 |

> [!NOTE]
> 应用服务提供了一个专用的交互式诊断工具来帮助你排查应用程序问题。 有关详细信息，请参阅[Azure App Service 诊断概述](overview-diagnostics.md)。
>
> 此外，可以使用其他 Azure 服务（例如 [Azure Monitor](../azure-monitor/app/azure-web-apps.md)）来改善应用的日志记录和监视功能。
>

## <a name="enable-application-logging-windows"></a>启用应用程序日志记录 (Windows)

> [!NOTE]
> Blob 存储的应用程序日志记录只能使用与应用服务位于同一区域中的存储帐户

若要在 [Azure 门户](https://portal.azure.com)中为 Windows 应用启用应用程序日志记录，请导航到你的应用，然后选择“应用服务日志”。****

对“应用程序日志记录(文件系统)”和/或“应用程序日志记录(Blob)”选择“打开”。************ 

“文件系统”选项用于临时调试，在 12 小时后会自行关闭。**** “Blob”选项用于长期日志记录，需要提供一个要将日志写入到的 Blob 存储容器。****  “Blob”选项还会在日志消息中包含其他信息，例如日志消息的来源 VM 实例 ID (`InstanceId`)、线程 ID (`Tid`) 和更详细的时间戳 ([`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks))。****

> [!NOTE]
> 目前，只有 .NET 应用程序日志可以写入到 blob 存储。 Java、PHP、Node.js、Python 应用程序日志只能存储在应用服务文件系统上（无需修改代码即可将日志写入外部存储）。
>
> 此外，如果[重新生成存储帐户的访问密钥](../storage/common/storage-create-storage-account.md)，则必须重置相应的日志记录配置才能使用更新的访问密钥。 要执行此操作：
>
> 1. 在“配置”**** 选项卡上，将相应的日志记录功能设置为“关闭”****。 保存设置。
> 2. 再次启用将日志记录到存储帐户 Blob。 保存设置。
>
>

选择“级别”，即要记录的详细级别。**** 下表显示了每个级别包含的日志类别：

| 级别 | 包含的类别 |
|-|-|
|**已禁用** | 无 |
|**错误** | “错误”、“严重” |
|**警告** | “警告”、“错误”、“严重”|
|**信息** | “信息”、“警告”、“错误”、“严重”|
|**Verbose** | “跟踪”、“调试”、“信息”、“警告”、“错误”、“严重”（所有类别） |

完成后，选择“保存”。****

## <a name="enable-application-logging-linuxcontainer"></a>启用应用程序日志记录（Linux/容器）

若要在[Azure 门户](https://portal.azure.com)中启用适用于 Linux 应用或自定义容器应用的应用程序日志记录，请导航到应用并选择 "**应用服务日志**"。

在 "**应用程序日志记录**" 中，选择 "**文件系统**"。

在 "**配额（MB）**" 中，指定应用程序日志的磁盘配额。 在“保留期(天)”中，设置日志要保留的天数。****

完成后，选择“保存”。****

## <a name="enable-web-server-logging"></a>启用 Web 服务器日志记录

若要在 [Azure 门户](https://portal.azure.com)中为 Windows 应用启用 Web 服务器日志记录，请导航到你的应用，然后选择“应用服务日志”。****

对于“Web 服务器日志记录”，请选择“存储”以将日志存储在 Blob 存储上，或选择“文件系统”以将日志存储在应用服务文件系统上。************ 

在“保留期(天)”中，设置日志要保留的天数。****

> [!NOTE]
> 如果[重新生成存储帐户的访问密钥](../storage/common/storage-create-storage-account.md)，则必须重置相应的日志记录配置才能使用更新的密钥。 要执行此操作：
>
> 1. 在“配置”**** 选项卡上，将相应的日志记录功能设置为“关闭”****。 保存设置。
> 2. 再次启用将日志记录到存储帐户 Blob。 保存设置。
>
>

完成后，选择“保存”。****

## <a name="log-detailed-errors"></a>记录详细错误

若要在 [Azure 门户](https://portal.azure.com)中保存 Windows 应用的错误页或失败请求跟踪，请导航到你的应用，然后选择“应用服务日志”。****

在“详细错误日志记录”或“失败请求跟踪”下，选择“打开”，然后选择“保存”。****************

这两种类型的日志都将存储在应用服务文件系统中。 最多可保留 50 个错误（文件/文件夹）。 当 HTML 文件的数目超过 50 个时，会自动删除最早的 26 个错误。

## <a name="add-log-messages-in-code"></a>在代码中添加日志消息

在应用程序代码中，可以使用普通的日志记录功能将日志消息发送到应用程序日志。 例如：

- ASP.NET 应用程序可使用 [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) 类将信息记录到应用程序诊断日志。 例如：

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- 默认情况下，ASP.NET Core 使用 [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 日志记录提供程序。 有关详细信息，请参阅 [Azure 中的 ASP.NET Core 日志记录](https://docs.microsoft.com/aspnet/core/fundamentals/logging/)。

## <a name="stream-logs"></a>流式传输日志

在实时流式传输日志之前，请启用所需的日志类型。 应用服务将会流式传输写入到存储在 */LogFiles* 目录 (d:/home/logfiles) 中的、以 .txt、.log 或 .htm 结尾文件的所有信息。

> [!NOTE]
> 某些类型的日志记录缓冲区会对日志文件执行写入操作，这可能会导致流中的事件变成混乱。 例如，用户访问页面时出现的应用程序日志项可能显示在该页面请求所对应的 HTTP 日志项的前面。
>

### <a name="in-azure-portal"></a>在 Azure 门户中配置

若要在 [Azure 门户](https://portal.azure.com)中流式传输日志，请导航到你的应用并选择“日志流”。**** 

### <a name="in-cloud-shell"></a>在 Cloud Shell

若要在[Cloud Shell](../cloud-shell/overview.md)中实时流式传输日志，请使用以下命令：

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup
```

若要筛选特定事件（如错误），请使用 **--Filter** 参数。 例如：

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --filter Error
```
若要筛选特定日志类型（如 HTTP），请使用 **-Path** 参数。 例如：

```azurecli-interactive
az webapp log tail --name appname --resource-group myResourceGroup --path http
```

### <a name="in-local-terminal"></a>在本地终端中

若要在本地控制台中流式传输日志，请[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 并[登录帐户](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)。 登录后，请按照[Cloud Shell 的说明进行操作](#in-cloud-shell)

## <a name="access-log-files"></a>访问日志文件

如果为日志类型配置 Azure 存储 Blob 选项，需要使用适用于 Azure 存储的客户端工具。 有关详细信息，请参阅 [Azure 存储客户端工具](../storage/common/storage-explorers.md)。

对于存储在应用服务文件系统中的日志，最简单的方法是在浏览器中通过以下链接下载 ZIP 文件：

- Linux/容器应用：`https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows 应用：`https://<app-name>.scm.azurewebsites.net/api/dump`

对于 Linux/容器应用，ZIP 文件包含 docker 主机和 docker 容器的控制台输出日志。 对于向外扩展的应用程序，ZIP 文件包含每个实例的一组日志。 在应用服务文件系统中，这些日志文件是 */home/LogFiles*目录的内容。

对于 Windows 应用，该 ZIP 文件包含应用服务文件系统中 *D:\Home\LogFiles* 目录的内容。 其结构如下：

| 日志类型 | 目录 | 说明 |
|-|-|-|
| **应用程序日志** |*/LogFiles/Application/* | 包含一个或多个文本文件。 日志消息的格式取决于所用的日志记录提供程序。 |
| **失败请求跟踪** | */LogFiles/W3SVC#########/* | 包含 XML 文件和一个 XSL 文件。 可以在浏览器中查看带格式的 XML 文件。 |
| **详细的错误日志** | */LogFiles/DetailedErrors/* | 包含 HTM 错误文件。 可以在浏览器中查看 HTM 文件。<br/>查看失败请求跟踪的另一种方法是在门户中导航到应用页。 在左侧菜单中选择“诊断和解决问题”，搜索“失败请求跟踪日志”，然后单击相应的图标来浏览和查看所需的跟踪。******** |
| **Web 服务器日志** | */LogFiles/http/RawLogs/* | 包含使用 [W3C 扩展日志文件格式](/windows/desktop/Http/w3c-logging)的文本文件。 可以使用文本编辑器或诸如[日志分析程序](https://go.microsoft.com/fwlink/?LinkId=246619)之类实用工具来阅读此信息。<br/>应用服务不支持 `s-computername`、`s-ip` 或 `cs-version` 字段。 |
| **部署日志** | */LogFiles/Git/* 和 */deployments/* | 包含内部部署进程生成的日志，以及 Git 部署的日志。 |

## <a name="send-logs-to-azure-monitor-preview"></a>将日志发送到 Azure Monitor（预览版）

使用新的 [Azure Monitor 集成](https://aka.ms/appsvcblog-azmon)，可以[创建诊断设置（预览版）](https://azure.github.io/AppService/2019/11/01/App-Service-Integration-with-Azure-Monitor.html#create-a-diagnostic-setting)将日志发送到存储帐户、事件中心和 Log Analytics。 

> [!div class="mx-imgBorder"]
> ![诊断设置（预览版）](media/troubleshoot-diagnostic-logs/diagnostic-settings-page.png)

### <a name="supported-log-types"></a>支持的日志类型

下表显示了支持的日志类型和说明： 

| 日志类型 | Windows 支持 | Linux （Docker）支持 | 说明 |
|-|-|-|
| AppServiceConsoleLogs | TBA | 是 | 标准输出和标准错误 |
| AppServiceHTTPLogs | 是 | 是 | Web 服务器日志 |
| AppServiceEnvironmentPlatformLogs | 是 | 是 | 应用服务环境：缩放、配置更改和状态日志|
| AppServiceAuditLogs | 是 | 是 | 通过 FTP 和 Kudu 进行的登录活动 |
| AppServiceFileAuditLogs | 是 | TBD | 通过 FTP 和 Kudu 进行的文件更改 |
| AppServiceAppLogs | TBA | Java SE & Tomcat | 应用程序日志 |

## <a name="next-steps"></a><a name="nextsteps"></a> 后续步骤
* [使用 Azure Monitor 查询日志](../azure-monitor/log-query/log-query-overview.md)
* [如何监视 Azure 应用服务](web-sites-monitor.md)
* [在 Visual Studio 中对 Azure 应用服务进行故障排除](troubleshoot-dotnet-visual-studio.md)
* [在 HDInsight 中分析应用日志](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
