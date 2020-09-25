---
title: 配置自定义容器
description: 了解如何在 Azure App Service 中配置自定义容器。 本文介绍最常见的配置任务。
ms.topic: article
ms.date: 09/22/2020
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 5b1bf9b205fc1eb90c6eeae3a101def764381213
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91264569"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>为 Azure App Service 配置自定义容器

本文介绍如何配置在 Azure App Service 上运行的自定义容器。

::: zone pivot="container-windows"

本指南提供有关应用服务中的 Windows 应用的容器化的重要概念和说明。 如果你从未使用过 Azure App Service，请先按照 [自定义容器快速入门](quickstart-custom-container.md) 和 [教程](tutorial-custom-container.md) 操作。

::: zone-end

::: zone pivot="container-linux"

本指南提供有关应用服务中 Linux 应用的容器化的重要概念和说明。 如果你从未使用过 Azure App Service，请先按照 [自定义容器快速入门](quickstart-custom-container.md) 和 [教程](tutorial-custom-container.md) 操作。 还有 [多容器应用快速入门](quickstart-multi-container.md) 和 [教程](tutorial-multi-container-app.md)。

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>支持的父映像

对于自定义 Windows 映像，必须为所需的框架 [ (基本映像) ](https://docs.docker.com/develop/develop-images/baseimages/) 选择正确的父映像：

- 若要部署 .NET Framework 应用，请使用基于 Windows Server Core [长期服务通道 (LTSC) ](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) 版本的父映像。 
- 若要部署 .NET Core 应用，请使用基于 Windows Server Nano [半年服务通道的父映像 (SAC) ](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) 版本。 

在应用启动期间，下载父映像需要一些时间。 不过，可以使用已缓存在 Azure 应用服务中的下述父映像之一，缩短启动时间：

- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore)：2004
- [mcr.microsoft.com/windows/servercore](https://hub.docker.com/_/microsoft-windows-servercore)： ltsc2019
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/)： 4.8-windowsservercore-2004
- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/)： 4.8-windowsservercore-ltsc2019
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/)： 3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/)： 3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/)： 3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/)： 3.1-nanoserver-1809
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/)： 3.1-nanoserver-2004
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/)： 3.1-nanoserver-1909
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/)： 3.1-nanoserver-1903
- [mcr.microsoft.com/dotnet/core/aspnet](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/)： 3.1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>更改自定义容器的 Docker 映像

若要将现有的自定义容器应用从当前 Docker 映像更改为新映像，请使用以下命令：

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>使用专用注册表中的映像

若要从专用注册表（如 Azure 容器注册表）使用映像，请运行以下命令：

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

对于 *\<username>* 和 *\<password>* ，请提供专用注册表帐户的登录凭据。

## <a name="i-dont-see-the-updated-container"></a>我看不到更新的容器

如果将 Docker 容器的设置更改为指向新的容器，则可能需要几分钟，应用才能从新容器处理 HTTP 请求。 在拉取和启动新容器时，应用服务会继续处理来自旧容器的请求。 只有启动新容器并准备好接收请求后，应用服务才会开始向其发送请求。

## <a name="how-container-images-are-stored"></a>容器映像的存储方式

首次在应用服务中运行自定义 Docker 映像时，应用服务会执行 `docker pull` 并拉取所有映像层。 这些层存储在磁盘上，就像使用 Docker 本地一样。 应用服务每次重新启动时，都将执行 `docker pull` ，但只提取已更改的层。 如果没有更改，应用服务将使用本地磁盘上的现有层。

如果应用由于某种原因（例如向上和向下缩放定价层）更改了计算实例，应用服务必须再次拉出所有层。 如果横向扩展以添加更多实例，则情况也是如此。 在少数情况下，应用程序实例可能会在不进行缩放操作的情况下更改。

## <a name="configure-port-number"></a>配置端口号

默认情况下，应用服务假定自定义容器在端口80上进行侦听。 如果容器侦听其他端口，请 `WEBSITES_PORT` 在应用服务应用中设置应用设置。 可以通过 [Cloud Shell](https://shell.azure.com)设置。 在 Bash 中：

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

在 PowerShell 中运行：

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

应用服务目前允许容器只为 HTTP 请求提供一个端口。 

## <a name="configure-environment-variables"></a>配置环境变量

自定义容器可能会使用需要在外部提供的环境变量。 可以通过 [Cloud Shell](https://shell.azure.com)传递它们。 在 Bash 中：

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

在 PowerShell 中运行：

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

当应用运行时，应用服务应用设置会自动注入到进程中。 

::: zone pivot="container-windows"
对于 IIS 或 .NET Framework (4.0 或更高版本) 的容器，它们 `System.ConfigurationManager` 被应用服务自动注入到 .net 应用设置和连接字符串。 对于所有其他语言或框架，它们作为该进程的环境变量提供，其中包含以下相应的前缀之一：

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

此方法适用于单容器应用或多容器应用，其中环境变量是在 *docker-compose.yml. docker-compose.override.yml* 文件中指定的。

::: zone-end

## <a name="use-persistent-shared-storage"></a>使用永久共享存储

::: zone pivot="container-windows"

你可以使用应用文件系统中的 *C:\home* 目录跨重启保存文件并在实例之间共享这些文件。 `C:\home`提供应用中的，使容器应用能够访问永久性存储。

禁用永久性存储后，不会保留写入 `C:\home` 目录。 [Docker 主机日志和容器日志](#access-diagnostic-logs) 保存在未附加到容器的默认永久性共享存储中。 启用持久性存储时，将保留对该目录的所有写入 `C:\home` 并可由扩展的应用程序的所有实例访问该目录，并且可以从访问日志 `C:\home\LogFiles` 。

默认情况下，永久存储处于 *禁用状态* ，并且该设置不会在应用程序设置中公开。 若要启用它，请 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 通过 [Cloud Shell](https://shell.azure.com)设置应用程序设置。 在 Bash 中：

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

在 PowerShell 中运行：

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

::: zone-end

::: zone pivot="container-linux"

你可以使用应用文件系统中的 */home* 目录跨重启保存文件并在实例之间共享这些文件。 `/home`提供应用中的，使容器应用能够访问永久性存储。

禁用永久性存储后，对该目录的写入 `/home` 不会在应用重新启动或多个实例之间保持不变。 唯一的例外是 `/home/LogFiles` 用于存储 Docker 和容器日志的目录。 启用持久性存储时，将保留对该目录的所有写入， `/home` 并可由扩展的应用程序的所有实例访问。

默认情况下，将 *启用* 持久性存储，并且不会在应用程序设置中公开设置。 若要禁用它，请 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 通过 [Cloud Shell](https://shell.azure.com)设置应用程序设置。 在 Bash 中：

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

在 PowerShell 中运行：

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=false}
```

::: zone-end

> [!NOTE]
> 你还可以 [配置自己的持久存储](configure-connect-to-azure-storage.md)。

## <a name="detect-https-session"></a>检测 HTTPS 会话

应用服务在前端终止 TLS/SSL。 也就是说，TLS/SSL 请求永远不会进入你的应用程序。 不需要，也不应在应用程序中实现对 TLS/SSL 的任何支持。 

前端位于 Azure 数据中心内。 如果对应用程序使用 TLS/SSL，则始终会安全加密 Internet 上的流量。

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>自定义 ASP.NET 计算机密钥注入

 在容器启动过程中，会将自动生成的密钥作为 ASP.NET 加密例程的计算机密钥注入到容器中。 可以通过查找以下环境变量 [在容器中查找这些密钥](#connect-to-the-container) ： `MACHINEKEY_Decryption` 、 `MACHINEKEY_DecryptionKey` 、 `MACHINEKEY_ValidationKey` 和 `MACHINEKEY_Validation` 。 

如果你的应用程序依赖于，则每次重新启动时的新密钥可能会重置 ASP.NET forms 身份验证和视图状态。 若要阻止自动重新生成密钥，请 [手动将其设置为应用服务应用设置](#configure-environment-variables)。 

## <a name="connect-to-the-container"></a>连接到容器

您可以通过导航到，直接连接到您的 Windows 容器来诊断任务 `https://<app-name>.scm.azurewebsites.net/DebugConsole` 。 工作原理如下：

- 调试控制台允许执行交互式命令，如启动 PowerShell 会话、检查注册表项，以及导航整个容器文件系统。
- 它独立于其上方的图形浏览器运行，它只显示 [共享存储](#use-persistent-shared-storage)中的文件。
- 在向外扩展的应用程序中，调试控制台连接到一个容器实例。 可以从顶部菜单中的 " **实例** " 下拉列表中选择一个不同的实例。
- 当应用重新启动时，对容器所做的任何更改都 *不* 会保留 (共享存储) 中的更改除外，因为它不是 Docker 映像的一部分。 若要保存更改，如注册表设置和软件安装，请将其设为 Dockerfile 的一部分。

## <a name="access-diagnostic-logs"></a>访问诊断日志

应用服务通过 Docker 主机以及容器中的活动记录操作。 默认情况下，会附带 (平台日志) 的 Docker 主机中的日志，但需要手动启用容器中的应用程序日志或 web 服务器日志。 有关详细信息，请参阅 [启用应用程序日志记录](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) 和 [启用 web 服务器日志记录](troubleshoot-diagnostic-logs.md#enable-web-server-logging)。 

可以通过多种方式访问 Docker 日志：

- [在 Azure 门户中配置](#in-azure-portal)
- [从 Kudu 控制台](#from-the-kudu-console)
- [具有 Kudu API](#with-the-kudu-api)
- [向 Azure monitor 发送日志](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>在 Azure 门户中配置

Docker 日志显示在门户中应用程序的 " **容器设置** " 页上。 日志将被截断，但你可以下载所有日志单击 " **下载**"。 

### <a name="from-the-kudu-console"></a>从 Kudu 控制台

导航到 `https://<app-name>.scm.azurewebsites.net/DebugConsole` 并单击 " **日志** 文件" 文件夹以查看单个日志文件。 若要下载整个 **日志** 的目录，请单击目录名称左侧的 " **下载** " 图标。 你还可以使用 FTP 客户端访问此文件夹。

默认情况下，控制台终端不能访问该 `C:\home\LogFiles` 文件夹，因为未启用永久共享存储。 若要在控制台终端中启用此行为，请 [启用永久共享存储](#use-persistent-shared-storage)。

如果尝试使用 FTP 客户端下载当前正在使用的 Docker 日志，可能会由于文件锁定而出现错误。

### <a name="with-the-kudu-api"></a>具有 Kudu API

直接导航到 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 以查看 Docker 日志的元数据。 你可能会看到列出了多个日志文件， `href` 属性允许你直接下载日志文件。 

若要将所有日志一起下载到一个 ZIP 文件中，请访问 `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` 。

## <a name="customize-container-memory"></a>自定义容器内存

默认情况下，部署在 Azure App Service 中的所有 Windows 容器均限制为 1 GB RAM。 可以通过 Cloud Shell 提供应用设置来更改此值 `WEBSITE_MEMORY_LIMIT_MB` 。 [Cloud Shell](https://shell.azure.com) 在 Bash 中：

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

在 PowerShell 中运行：

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

该值以 MB 为单位定义，并且必须小于或等于主机的总物理内存。 例如，在具有 8 GB RAM 的应用服务计划中，所有应用的累积总数 `WEBSITE_MEMORY_LIMIT_MB` 不得超过 8 gb。 有关可用于每个定价层的内存量的信息，请参阅**高级容器 (Windows) 计划**部分的[应用服务定价](https://azure.microsoft.com/pricing/details/app-service/windows/)。

## <a name="customize-the-number-of-compute-cores"></a>自定义计算核心数

默认情况下，Windows 容器将运行，其中包含所选定价层的所有可用内核。 例如，你可能想要减少过渡槽使用的内核数。 若要减少容器使用的内核数，请将 `WEBSITE_CPU_CORES_LIMIT` 应用设置设置为首选的内核数。 可以通过 [Cloud Shell](https://shell.azure.com)设置。 在 Bash 中：

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

在 PowerShell 中运行：

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> 更新应用设置会触发自动重新启动，从而使停机时间最短。 对于生产应用，请考虑将其交换到过渡槽，在过渡槽中更改应用设置，然后将其交换回生产。

转到 Kudu 控制台 (`https://<app-name>.scm.azurewebsites.net`) 并使用 PowerShell 键入以下命令，以验证调整后的数字。 每个命令输出一个数字。

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

处理器可能是多核处理器或超线程处理器。 有关可用于每个定价层的内核数的信息，请参阅 "**高级容器 (Windows) 计划**" 部分中的 "[应用服务定价](https://azure.microsoft.com/pricing/details/app-service/windows/)"。

## <a name="customize-health-ping-behavior"></a>自定义运行状况 ping 行为

应用服务认为容器在容器启动时成功启动并响应 HTTP ping。 运行状况 ping 请求会容器标头 `User-Agent= "App Service Hyper-V Container Availability Check"` 。 如果容器在一段时间后启动但不响应 ping，应用服务会在 Docker 日志中记录一个事件，指出容器未启动。 

如果你的应用程序占用大量资源，则容器可能无法及时响应 HTTP ping。 若要控制 HTTP ping 失败时的操作，请设置 `CONTAINER_AVAILABILITY_CHECK_MODE` 应用设置。 可以通过 [Cloud Shell](https://shell.azure.com)设置。 在 Bash 中：

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

在 PowerShell 中运行：

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

下表显示了可能的值：

| Value | 说明 |
| - | - |
| **修复** | 三次连续可用性检查后重启容器 |
| **ReportOnly** | 默认值。 请不要重启容器，但在三次连续的可用性检查后，会在 Docker 日志中报告容器。 |
| 关闭 | 不检查可用性。 |

## <a name="support-for-group-managed-service-accounts"></a>支持组托管服务帐户

应用服务中的 Windows 容器当前不支持组托管服务帐户 (Gmsa) 。

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>启用 SSH

SSH 实现容器和客户端之间的安全通信。 为了使自定义容器支持 SSH，你必须将其添加到 Dockerfile 本身。

> [!TIP]
> 所有内置 Linux 容器都已将 SSH 说明添加到了其映像存储库中。 你可以使用 [Node.js 10.14 存储库](https://github.com/Azure-App-Service/node/blob/master/10.14) 完成以下说明，以查看其在此中的启用方式。

- 使用 [运行](https://docs.docker.com/engine/reference/builder/#run) 指令安装 SSH 服务器，并将根帐户的密码设置为 `"Docker!"` 。 例如，对于基于 [Alpine Linux](https://hub.docker.com/_/alpine)的映像，需要以下命令：

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    此配置不允许从外部建立到容器的连接。 SSH 仅通过 `https://<app-name>.scm.azurewebsites.net` 发布凭据提供并经过身份验证。

- 将 [此 sshd_config 文件](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) 添加到映像存储库，并使用 [copy](https://docs.docker.com/engine/reference/builder/#copy) 指令将文件复制到 */etc/ssh/* 目录中。 有关 *sshd_config* 文件的详细信息，请参阅 [OpenBSD 文档](https://man.openbsd.org/sshd_config)。

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > sshd_config 文件必须包括以下项**：
    > - `Ciphers` 必须至少包含此列表中的一项：`aes128-cbc,3des-cbc,aes256-cbc`。
    > - `MACs` 必须至少包含此列表中的一项：`hmac-sha1,hmac-sha1-96`。

- 使用 [公开](https://docs.docker.com/engine/reference/builder/#expose) 说明在容器中打开端口2222。 尽管根密码已知，但无法从 internet 访问端口2222。 它只能由专用虚拟网络的桥网络中的容器访问。

    ```Dockerfile
    EXPOSE 80 2222
    ```

- 在容器的启动脚本中启动 SSH 服务器。

    ```bash
    /usr/sbin/sshd
    ```

    有关示例，请参阅默认 [Node.js 10.14 容器](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) 如何启动 SSH 服务器。

## <a name="access-diagnostic-logs"></a>访问诊断日志

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>配置多容器应用

- [在 Docker Compose 中使用持久性存储](#use-persistent-storage-in-docker-compose)
- [预览版限制](#preview-limitations)
- [Docker Compose 选项](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>在 Docker Compose 中使用持久性存储

多容器应用（如 WordPress）需要持久存储才能正常工作。 若要启用它，你的 Docker Compose 配置必须指向容器 *外部* 的存储位置。 在应用重新启动后，容器中的存储位置不会保存更改。

`WEBSITES_ENABLE_APP_SERVICE_STORAGE`使用[Cloud Shell](https://shell.azure.com)中的[az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)命令设置应用设置，启用持久存储。

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

在 *docker-compose.yml* 文件中，将 `volumes` 选项映射到 `${WEBAPP_STORAGE_HOME}` 。 

`WEBAPP_STORAGE_HOME` 是应用服务中已映射到应用持续性存储的环境变量。 例如：

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>预览版限制

多容器目前处于预览阶段。 不支持以下应用服务平台功能：

- 身份验证/授权
- 托管标识
- CORS

### <a name="docker-compose-options"></a>Docker Compose 选项

以下列表显示了支持的和不支持的 Docker Compose 配置选项：

#### <a name="supported-options"></a>支持的选项

- command
- entrypoint
- environment
- 图像
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>不支持的选项

- build（不允许）
- depends_on（忽略）
- networks（忽略）
- secrets（忽略）
- 除了 80 和 8080 之外的端口（忽略）

> [!NOTE]
> 公共预览版中将忽略未显式调用的任何其他选项。

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用自定义容器将自定义软件迁移到 Azure App Service](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [教程：多容器 WordPress 应用](tutorial-multi-container-app.md)

::: zone-end

或者，请参阅其他资源：

[在 Windows/Linux 容器中加载证书](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)