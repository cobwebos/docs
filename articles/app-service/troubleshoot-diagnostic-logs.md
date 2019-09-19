---
title: 为应用启用诊断日志记录 - Azure 应用服务
description: 了解如何启用诊断日志记录并将检测添加到应用程序，以及如何访问由 Azure 记录的信息。
services: app-service
author: cephalin
manager: gwallace
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/17/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b0fab51e002ecb431bf68f58984290889296b2a9
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097541"
---
# <a name="enable-diagnostics-logging-for-apps-in-azure-app-service"></a>为 Azure 应用服务中的应用启用诊断日志记录
## <a name="overview"></a>概述
Azure 提供内置诊断功能，可帮助调试[应用服务应用](overview.md)。 在本文中，将了解如何启用诊断日志记录并将检测添加到应用程序，以及如何访问由 Azure 记录的信息。

本文使用 [Azure 门户](https://portal.azure.com)和 Azure CLI 来处理诊断日志。 有关通过 Visual Studio 使用诊断日志的信息，请参阅[在 Visual Studio 中对 Azure 进行故障排除](troubleshoot-dotnet-visual-studio.md)。

|类型|平台|Location|描述|
|-|-|-|-|
| 应用程序日志记录 | Windows、Linux | 应用服务文件系统和/或 Azure 存储 blob | 记录由应用程序代码生成的消息。 这些消息可以由你选择的 web 框架或你的应用程序代码使用你的语言的标准日志模式直接生成。 为每条消息分配以下类别之一：**严重**、**错误**、**警告**、**信息**、**调试**和**跟踪**。 启用应用程序日志记录时，可以通过设置严重性级别来选择要进行日志记录的详细程度。|
| Web 服务器日志记录| Windows | 应用服务文件系统或 Azure 存储 blob| 采用[W3C 扩展日志文件格式](/windows/desktop/Http/w3c-logging)的原始 HTTP 请求数据。 每条日志消息都包括 HTTP 方法、资源 URI、客户端 IP、客户端端口、用户代理、响应代码等数据。 |
| 详细错误日志记录 | Windows | 应用服务文件系统 | 已发送到客户端浏览器的 *.htm*错误页的副本。 出于安全原因，不应将详细的错误页发送到生产中的客户端，但应用服务可以在每次出现包含 HTTP 代码400或更高版本的应用程序错误时保存错误页。 页面可能包含可帮助确定服务器返回错误代码的原因的信息。 |
| 请求跟踪失败 | Windows | 应用服务文件系统 | 有关失败请求的详细跟踪信息，包括用于处理请求的 IIS 组件和每个组件所用的时间的跟踪。 如果要提高站点性能或隔离特定的 HTTP 错误，这将非常有用。 为每个失败的请求生成一个文件夹，其中包含 XML 日志文件，以及用于查看日志文件的 XSL 样式表。 |
| 部署日志记录 | Windows、Linux | 应用服务文件系统 | 将内容发布到应用时的日志。 部署日志记录自动发生，并且没有可配置的部署日志记录设置。 它可帮助你确定部署失败的原因。 例如，如果使用[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)，则可以使用部署日志记录来确定该脚本失败的原因。 |

> [!NOTE]
> 应用服务提供了一个专用的交互式诊断工具，可帮助你对应用程序进行故障排除。 有关详细信息，请参阅[Azure App Service 诊断概述](overview-diagnostics.md)。
>
> 此外，还可以使用其他 Azure 服务来改善应用的日志记录和监视功能，如[Azure Monitor](../azure-monitor/app/azure-web-apps.md)。
>

## <a name="enable-application-logging-windows"></a>启用应用程序日志记录（Windows）

若要在[Azure 门户](https://portal.azure.com)中为 Windows 应用启用应用程序日志记录，请导航到应用并选择 "**应用服务日志**"。

对于**应用程序日志记录（文件系统）** 或**应用程序日志记录（Blob）** ，请选择 **"打开**"。 

**Filesystem**选项用于临时调试，并在12小时后关闭。 **Blob**选项用于长期日志记录，需要 blob 存储容器来写入日志。  **Blob**选项还在日志消息中包含其他信息，如日志消息的源 VM 实例的 ID （`InstanceId`）、线程 ID （`Tid`），以及更细化的时间戳（[`EventTickCount`](https://docs.microsoft.com/dotnet/api/system.datetime.ticks)）。

> [!NOTE]
> 目前，只有 .NET 应用程序日志可以写入到 blob 存储。 Java、PHP、node.js、Python 应用程序日志只能存储在应用服务文件系统上（无需修改代码即可将日志写入外部存储）。
>
> 此外，如果[重新生成存储帐户的访问密钥](../storage/common/storage-create-storage-account.md)，则必须重置相应的日志记录配置才能使用更新的访问密钥。 为此，请执行以下操作:
>
> 1. 在“配置”选项卡上，将相应的日志记录功能设置为“关闭”。 保存设置。
> 2. 再次启用将日志记录到存储帐户 Blob。 保存设置。
>
>

选择**级别**，或要记录的详细信息的级别。 下表显示了每个级别中包含的日志类别：

| Level | 包含的类别 |
|-|-|
|**已禁用** | None |
|**错误** | “错误”、“严重” |
|**警告** | “警告”、“错误”、“严重”|
|**信息** | “信息”、“警告”、“错误”、“严重”|
|**详细** | “跟踪”、“调试”、“信息”、“警告”、“错误”、“严重”（所有类别） |

完成后，选择 "**保存**"。

## <a name="enable-application-logging-linuxcontainer"></a>启用应用程序日志记录（Linux/容器）

若要在[Azure 门户](https://portal.azure.com)中启用适用于 Linux 应用或自定义容器应用的应用程序日志记录，请导航到应用并选择 "**应用服务日志**"。

在 "**应用程序日志记录**" 中，选择 "**文件系统**"。

在 "**配额（MB）** " 中，指定应用程序日志的磁盘配额。 在 "**保持期（天）** " 中，设置日志应保留的天数。

完成后，选择 "**保存**"。

## <a name="enable-web-server-logging"></a>启用 Web 服务器日志记录

若要在[Azure 门户](https://portal.azure.com)中为 Windows 应用启用 web 服务器日志记录，请导航到应用并选择 "**应用服务日志**"。

对于 " **Web 服务器日志记录**"，请选择 "**存储**"，将日志存储在 Blob 存储或**文件系统**上，以将日志存储在应用服务文件系统上。 

在 "**保持期（天）** " 中，设置日志应保留的天数。

> [!NOTE]
> 如果[重新生成存储帐户的访问密钥](../storage/common/storage-create-storage-account.md)，则必须重置相应的日志记录配置才能使用更新的密钥。 为此，请执行以下操作:
>
> 1. 在“配置”选项卡上，将相应的日志记录功能设置为“关闭”。 保存设置。
> 2. 再次启用将日志记录到存储帐户 Blob。 保存设置。
>
>

完成后，选择 "**保存**"。

## <a name="log-detailed-errors"></a>记录详细错误

若要为[Azure 门户](https://portal.azure.com)中的 Windows 应用保存错误页面或失败请求跟踪，请导航到应用并选择 "**应用服务日志**"。

在 "**详细错误日志记录**" 或 "**失败请求跟踪**" 下选择 **"打开**"，然后选择 "**保存**"。

这两种类型的日志都存储在应用服务文件系统中。 最多可保留50个错误（文件/文件夹）。 如果 HTML 文件的数量超过50，则将自动删除最早的26个错误。

## <a name="add-log-messages-in-code"></a>在代码中添加日志消息

在应用程序代码中，使用常用日志记录功能将日志消息发送到应用程序日志。 例如：

- ASP.NET 应用程序可使用 [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) 类将信息记录到应用程序诊断日志。 例如：

    ```csharp
    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");
    ```

- 默认情况下，ASP.NET Core 使用[AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices)日志记录提供程序。 有关详细信息，请参阅 [Azure 中的 ASP.NET Core 日志记录](https://docs.microsoft.com/aspnet/core/fundamentals/logging/)。

## <a name="stream-logs"></a>流式传输日志

在实时流式传输日志之前，请启用所需的日志类型。 写入以 .txt、.log 或 .htm 结尾并存储在 */LogFiles*目录（d：/home/日志文件）中的文件的任何信息都由应用服务进行流式处理。

> [!NOTE]
> 某些类型的日志记录缓冲区会对日志文件执行写入操作，这可能会导致流中的事件变成混乱。 例如，用户访问页面时出现的应用程序日志项可能显示在该页面请求所对应的 HTTP 日志项的前面。
>

### <a name="in-azure-portal"></a>在 Azure 门户中配置

若要在[Azure 门户](https://portal.azure.com)中流式传输日志，请导航到应用并选择 "**日志流**"。 

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

若要在本地控制台中流式传输日志，请[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)并[登录到你的帐户](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)。 登录后，请按照[Cloud Shell 的说明进行操作](#in-cloud-shell)

## <a name="access-log-files"></a>访问日志文件

如果为日志类型配置 Azure 存储 blob 选项，则需要使用适用于 Azure 存储的客户端工具。 有关详细信息，请参阅[Azure 存储客户端工具](../storage/common/storage-explorers.md)。

对于存储在应用服务文件系统中的日志，最简单的方法是在浏览器中下载 ZIP 文件，网址为：

- Linux/容器应用：`https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip`
- Windows 应用：`https://<app-name>.scm.azurewebsites.net/api/dump`

对于 Linux/容器应用，ZIP 文件包含 docker 主机和 docker 容器的控制台输出日志。 对于向外扩展的应用程序，ZIP 文件包含每个实例的一组日志。 在应用服务文件系统中，这些日志文件是 */home/LogFiles*目录的内容。

对于 Windows 应用，ZIP 文件包含应用服务文件系统中*D:\Home\LogFiles*目录的内容。 其结构如下：

| 日志类型 | 目录 | 描述 |
|-|-|-|
| **应用程序日志** |*/LogFiles/Application/* | 包含一个或多个文本文件。 日志消息的格式取决于所使用的日志提供程序。 |
| **失败请求跟踪** | */LogFiles/W3SVC # # # # # # # # #/* | 包含 XML 文件和一个 XSL 文件。 您可以在浏览器中查看格式化的 XML 文件。 |
| **详细的错误日志** | */LogFiles/DetailedErrors/* | 包含 HTM 错误文件。 可以在浏览器中查看 HTM 文件。<br/>查看失败请求跟踪的另一种方法是导航到门户中的应用页面。 在左侧菜单中，选择 "**诊断和解决问题**"，然后搜索 "**失败请求跟踪日志**"，然后单击图标以浏览并查看所需跟踪。 |
| **Web 服务器日志** | */LogFiles/http/RawLogs/* | 包含使用[W3C 扩展日志文件格式](/windows/desktop/Http/w3c-logging)进行格式化的文本文件。 可以使用文本编辑器或实用程序（如[Log Parser](https://go.microsoft.com/fwlink/?LinkId=246619)）读取此信息。<br/>应用服务不支持`s-computername`、 `s-ip`或`cs-version`字段。 |
| **部署日志** | */LogFiles/Git/* 和 */deployments/* | 包含内部部署过程生成的日志以及 Git 部署的日志。 |

## <a name="nextsteps"></a>后续步骤
* [如何监视 Azure 应用服务](web-sites-monitor.md)
* [在 Visual Studio 中对 Azure 应用服务进行故障排除](troubleshoot-dotnet-visual-studio.md)
* [在 HDInsight 中分析应用日志](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)
