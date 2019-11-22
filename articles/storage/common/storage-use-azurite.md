---
title: 将 Azurite 模拟器用于本地 Azure 存储开发
description: Azurite 开源模拟器（预览版）提供一个免费的本地环境用于测试 Azure 存储应用程序。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 0421f49b31eba688542adc0a5b62e1cf75028836
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74269458"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>将 Azurite 模拟器用于本地 Azure 存储开发和测试（预览）

Azurite 版本 3.2 开源模拟器（预览版）提供一个免费的本地环境用于测试 Azure blob 和队列存储应用程序。 如果你对应用程序在本地的工作状况感到满意，可以改用云中的 Azure 存储帐户。 该仿真器在 Windows、Linux 和 MacOS 上提供跨平台支持。 Azurite v3 支持 Azure Blob 服务实现的 API。

Azurite 是未来的存储仿真器平台。 Azurite 取代了 [Azure 存储仿真器](storage-use-emulator.md)。 Azurite 将持续更新，以支持最新版本的 Azure 存储 API。

可通过多种不同的方法在本地系统上安装并运行 Azurite：

  1. [安装并运行 Azurite Visual Studio Code 扩展](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [使用 NPM 安装并运行 Azurite](#install-and-run-azurite-by-using-npm)
  1. [安装并运行 Azurite Docker 映像](#install-and-run-the-azurite-docker-image)
  1. [从 GitHub 存储库克隆、生成并运行 Azurite](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>安装并运行 Azurite Visual Studio Code 扩展

在 Visual Studio Code 中选择“扩展”窗格，然后在“扩展: 市场”中搜索 **Azurite**。

![Visual Studio Code 扩展市场](media/storage-use-azurite/azurite-vs-code-extension.png)

或者，在浏览器中导航到 [VS Code 扩展市场](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite)。 选择“安装”按钮打开 Visual Studio Code，并直接转到 Azurite 扩展页。

可以通过单击 VS Code 状态栏中的“[Azurite Blob 服务]”或“[Azurite 队列服务]”，或者在 VS Code 命令面板中发出以下命令，快速启动或关闭 Azurite。 若要打开命令面板，请在 VS Code 中按 **F1**。

该扩展支持以下 Visual Studio Code 命令：

   * **Azurite：启动**所有 Azurite 服务
   * **Azurite：关闭**所有 Azurite 服务
   * **Azurite：清理**-重置所有 Azurite 服务持久性数据
   * **Azurite：启动 Blob 服务**-启动 blob 服务
   * **Azurite：关闭 Blob 服务**-关闭 blob 服务
   * **Azurite：清理 Blob 服务**-清理 blob 服务
   * **Azurite：启动队列服务**-启动队列服务
   * **Azurite：关闭队列服务**-关闭队列服务
   * **Azurite：清理队列**服务-清理队列服务

若要在 Visual Studio Code 中配置 Azurite，请选择“扩展”窗格。 选择 **Azurite** 所对应的“管理”(齿轮)图标。 选择“配置扩展设置”。

![Azurite - 配置扩展设置](media/storage-use-azurite/azurite-configure-extension-settings.png)

支持以下设置：

   * **Azurite： Blob 主机**-blob 服务侦听终结点。 默认设置为 127.0.0.1。
   * **Azurite： Blob 端口**-blob 服务侦听端口。 默认端口为 10000。
   * **Azurite：调试**-将调试日志输出到 Azurite 通道。 默认值为 **false**。
   * **Azurite： Location** -工作区位置路径。 默认值为 Visual Studio Code 的工作文件夹。
   * **Azurite： Queue Host** -队列服务侦听终结点。 默认设置为 127.0.0.1。
   * **Azurite：队列端口**-队列服务侦听端口。 默认端口为 10001。
   * **Azurite：无提示**模式禁用访问日志。 默认值为 **false**。

## <a name="install-and-run-azurite-by-using-npm"></a>使用 NPM 安装并运行 Azurite

此安装方法需要安装 [Node.js 8.0 或更高版本](https://nodejs.org)。 **npm** 是每个 Node.js 安装随附的包管理工具。 安装 Node.js 后，请执行以下 **npm** 命令安装 Azurite。

```console
npm install -g azurite
```

安装 Azurite 后，请参阅[从命令行运行 Azurite](#run-azurite-from-a-command-line)。

## <a name="install-and-run-the-azurite-docker-image"></a>安装并运行 Azurite Docker 映像

使用以下命令通过 [DockerHub](https://hub.docker.com/) 提取[最新的 Azurite 映像](https://hub.docker.com/_/microsoft-azure-storage-azurite)：

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**运行 Azurite Docker 映像**：

以下命令运行 Azurite Docker 映像。 `-p 10000:10000` 参数将来自主机端口 10000 的请求重定向到 Docker 实例。

```console
docker run -p 10000:10000 -p 10001:10001 mcr.microsoft.com/azure-storage/azurite
```

**指定工作区位置**：

在以下示例中，`-v c:/azurite:/data` 参数指定“c:/azurite”作为 Azurite 永久性数据的位置。 必须在运行 Docker 命令之前创建目录“c:/azurite”。

```console
docker run -p 10000:10000 -p 10001:10001 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**仅运行 blob 服务**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
```

**设置所有 Azurite 参数**：

此示例演示如何设置所有命令行参数。 以下所有参数应在一个命令行中指定。

```console
docker run -p 8888:8888
           -p 9999:9999
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
           --queuePort 9999
           --queueHost 0.0.0.0
```

有关在启动时配置 Azurite 的详细信息，请参阅[命令行选项](#command-line-options)。

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>从 GitHub 存储库克隆、生成并运行 Azurite

此安装方法需要安装 [Git](https://git-scm.com/)。 使用以下控制台命令克隆 Azurite 项目的 [GitHub 存储库](https://github.com/azure/azurite)。

```console
git clone https://github.com/Azure/Azurite.git
```

克隆源代码后，从克隆的存储库的根目录执行以下命令，以生成并安装 Azurite。

```console
npm install
npm run build
npm install -g
```

安装并生成 Azurite 后，请参阅[从命令行运行 Azurite](#run-azurite-from-a-command-line)。

## <a name="run-azurite-from-a-command-line"></a>从命令行运行 Azurite

> [!NOTE]
> 如果只是安装了 Visual Studio Code 扩展，则无法从命令行运行 Azurite。 请改用 VS Code 命令面板。 有关详细信息，请参阅[安装并运行 Azurite Visual Studio Code 扩展](#install-and-run-the-azurite-visual-studio-code-extension)。

若要立即开始使用命令行，请创建名为 **c:\azurite** 的目录，然后发出以下命令启动 Azurite：

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

此命令告知 Azurite 要将所有数据存储在特定目录 **c:\azurite** 中。 如果省略 **--location** 选项，此命令将使用当前工作目录。

## <a name="command-line-options"></a>命令行选项

本部分详细说明启动 Azurite 时可用的命令行开关。 所有命令行开关都是可选的。

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>] 
    [-d | --debug <log file path>] [-l | --location <workspace path>]
    [--queueHost <IP address>] [--queuePort <port address>]
    [-s | --silent] [-h | --help]
```

**-d** 是 **--debug** 的简写， **-l** 开关是 **--location** 的简写， **-s** 是 **--silent** 的简写， **-h** 是 **--help** 的简写。

### <a name="blob-listening-host"></a>Blob 侦听主机

**（可选）** 默认情况下，Azurite 将侦听本地服务器 127.0.0.1。 可根据要求使用 **--blobHost** 开关设置地址。

仅接受本地计算机上的请求：

```console
azurite --blobHost 127.0.0.1
```

允许远程请求：

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> 允许远程请求可能会导致系统容易受到外部攻击。

### <a name="blob-listening-port-configuration"></a>Blob 侦听端口配置

**（可选）** 默认情况下，Azurite 将在端口 10000 上侦听 Blob 服务。 使用 **--blobPort** 开关可指定所需的侦听端口。

> [!NOTE]
> 使用自定义端口后，需要在 Azure 存储工具或 SDK 中更新连接字符串或相应的配置。

自定义 Blob 服务侦听端口：

```console
azurite --blobPort 8888
```

让系统自动选择可用端口：

```console
azurite --blobPort 0
```

Azurite 启动期间，会显示正在使用的端口。

### <a name="queue-listening-host"></a>队列侦听主机

**（可选）** 默认情况下，Azurite 将侦听本地服务器 127.0.0.1。 可根据要求使用 **--queueHost** 开关设置地址。

仅接受本地计算机上的请求：

```console
azurite --queueHost 127.0.0.1
```

允许远程请求：

```console
azurite --queueHost 0.0.0.0
```

> [!CAUTION]
> 允许远程请求可能会导致系统容易受到外部攻击。

### <a name="queue-listening-port-configuration"></a>队列侦听端口配置

**（可选）** 默认情况下，Azurite 将在端口 10001 上侦听队列服务。 使用 **--queuePort** 开关可指定所需的侦听端口。

> [!NOTE]
> 使用自定义端口后，需要在 Azure 存储工具或 SDK 中更新连接字符串或相应的配置。

自定义队列服务侦听端口：

```console
azurite --queuePort 8888
```

让系统自动选择可用端口：

```console
azurite --queuePort 0
```

Azurite 启动期间，会显示正在使用的端口。

### <a name="workspace-path"></a>工作区路径

**（可选）** 在执行过程中，Azurite 会将数据存储到本地磁盘。 使用 **--location** 开关可将某个路径指定为工作区位置。 默认将使用当前进程的工作目录。

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>访问日志

**（可选）** 默认情况下，访问日志会显示在控制台窗口中。 使用 **--silent** 开关可以禁用显示访问日志。

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>调试日志

**（可选）** 调试日志包括有关每个请求和异常堆栈跟踪的详细信息。 在 **--debug** 开关中提供有效的本地文件路径可以启用调试日志。

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

## <a name="authorization-for-tools-and-sdks"></a>工具和 SDK 的授权

使用任何身份验证策略从 Azure 存储 SDK 或工具（例如 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)）连接到 Azurite。 需要身份验证。 Azurite 支持使用共享密钥和共享访问签名 (SAS) 进行授权。 Azurite 还支持匿名访问公共容器。

### <a name="well-known-storage-account-and-key"></a>已知的存储帐户和密钥

可以在 Azurite 中使用以下帐户名称和密钥。 旧式 Azure 存储仿真器也使用此已知帐户和密钥。

* 帐户名称：`devstoreaccount1`
* 帐户密钥：`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> 除了共享密钥身份验证外，Azurite 还支持帐户和服务 SAS 身份验证。 如果将容器设置为允许公共访问，则也可以使用匿名访问。

### <a name="connection-string"></a>连接字符串

从应用程序连接到 Azurite 的最简单方法是在应用程序的配置文件中配置一个引用快捷方式 *UseDevelopmentStorage=true* 的连接字符串。 下面是 *app.config* 文件中的连接字符串示例：

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

有关详细信息，请参阅[配置 Azure 存储连接字符串](storage-configure-connection-string.md)。

### <a name="storage-explorer"></a>存储资源管理器

在 Azure 存储资源管理器中，单击“添加帐户”图标连接到 Azurite，然后选择“附加到本地仿真器”并单击“连接”。

## <a name="differences-between-azurite-and-azure-storage"></a>Azurite 与 Azure 存储之间的差别

Azurite 本地实例与云中的 Azure 存储帐户在功能上有差别。

### <a name="endpoint-and-connection-url"></a>终结点和连接 URL

Azurite 的服务终结点不同于 Azure 存储帐户的终结点。 本地计算机不执行域名解析，要求 Azurite 终结点是本地地址。

对 Azure 存储帐户中的资源寻址时，帐户名称是 URI 主机名的一部分。 要寻址的资源是 URI 路径的一部分：

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

以下 URI 是 Azure 存储帐户中某个 Blob 的有效地址：

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

由于本地计算机不执行域名解析，帐户名称是 URI 路径的一部分，而不是主机名的一部分。 对 Azurite 中的资源使用以下 URI 格式：

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

可使用以下地址访问 Azurite 中的 Blob：

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>缩放和性能

Azurite 不是可缩放的存储服务，且不支持大量并发客户端。 它不提供性能方面的保证。 Azurite 用于开发和测试目的。

### <a name="error-handling"></a>错误处理。

Azurite 的错误处理逻辑与 Azure 存储相符，但存在一些差别。 例如，错误消息可能不同，而错误状态代码是一致的。

### <a name="ra-grs"></a>RA-GRS

Azurite 支持读取访问异地冗余复制 (RA-GRS)。 对于存储资源，可以通过在帐户名称后面追加 **-secondary** 来访问次要位置。 例如，以下地址可用于访问 Azurite 中使用只读辅助副本的 Blob：

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite 是开源的

欢迎为 Azurite 贡献作品和提出建议。 请访问 Azurite [GitHub 项目](https://github.com/Azure/Azurite/projects)页或 [GitHub 问题](https://github.com/Azure/Azurite/issues)了解产品里程碑，以及我们针对即将推出的功能和 bug 修复正在跟进的工作项。 GitHub 中也列出了详细的工作项。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 存储仿真器进行开发和测试](storage-use-emulator.md)介绍了旧式 Azure 存储仿真器（现已由 Azurite 取代）。
* [配置 Azure 存储连接字符串](storage-configure-connection-string.md)介绍了如何汇编有效的 Azure 存储连接字符串。
