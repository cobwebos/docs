---
title: 将 Azurite 开源模拟器用于 blob 存储开发和测试 (预览)
description: Azurite 开源模拟器 (预览版) 提供了一个免费的本地环境, 用于测试 Azure Blob 存储应用程序。
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: article
ms.date: 06/12/2019
ms.author: mhopkins
ms.subservice: common
ms.openlocfilehash: 2ccb19253c762bad69875a7b7bba7cd11d46e132
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869789"
---
# <a name="use-the-azurite-open-source-emulator-for-blob-storage-development-and-testing-preview"></a>将 Azurite 开源模拟器用于 blob 存储开发和测试 (预览)

Azurite 版本3开源模拟器 (预览版) 提供了一个用于测试 Azure Blob 存储应用程序的免费本地环境。 如果对应用程序在本地的工作方式感到满意, 则可以切换到在云中使用 Azure 存储帐户。 模拟器在 Windows、Linux 和 MacOS 上提供跨平台支持。 Azurite v3 支持 Azure Blob 服务实现的 Api。

Azurite 是将来的存储模拟器平台。 Azurite 取代了[Azure 存储模拟器](storage-use-emulator.md)。 Azurite 将继续更新以支持最新版本的 Azure 存储 Api。

在本地系统上安装和运行 Azurite 有几种不同的方法:

  1. [安装并运行 Azurite Visual Studio Code 扩展](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [使用 NPM 安装并运行 Azurite](#install-and-run-azurite-by-using-npm)
  1. [安装并运行 Azurite Docker 映像](#install-and-run-the-azurite-docker-image)
  1. [从 GitHub 存储库克隆、构建和运行 Azurite](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>安装并运行 Azurite Visual Studio Code 扩展

在 Visual Studio Code 中, 选择 "**扩展**" 窗格并在 "**扩展: MARKETPLACE**" 中搜索 " *Azurite* "。

![Visual Studio Code 扩展应用商店](media/storage-use-azurite/azurite-vs-code-extension.png)

或者, 在浏览器中导航到[VS Code 扩展市场](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite)。 选择 "**安装**" 按钮以打开 Visual Studio Code 并直接跳到 Azurite 扩展页。

可以通过单击 VS Code 状态栏中的**Azurite Blob 服务**或在 "VS Code 命令" 面板中发出以下命令, 快速启动或关闭 Azurite。 若要打开命令面板, 请在 VS Code 中按**F1** 。

此扩展插件支持以下 Visual Studio Code 命令:

   * **Azurite启动** -启动所有 Azurite 服务
   * **Azurite关闭**所有 Azurite 服务
   * **Azurite清除** -重置所有 Azurite services 持久性数据
   * **Azurite启动** -blob 启动 blob 服务
   * **Azurite关闭** blob 关闭 blob 服务
   * **Azurite清理** blob 干净 blob 服务

若要在 Visual Studio Code 中配置 Azurite, 请选择 "扩展" 窗格, 然后右键单击 " **Azurite**"。 选择 "**配置扩展设置**"。

![Azurite 配置扩展设置](media/storage-use-azurite/azurite-configure-extension-settings.png)

支持下列设置:

   * **AzuriteBlob 主机** -blob 服务侦听终结点。 默认设置为127.0.0.1。
   * **AzuriteBlob 端口** -blob 服务侦听端口。 默认端口为10000。
   * **Azurite调试** -将调试日志输出到 Azurite 通道。 默认值为 **false**。
   * **Azurite位置** -工作区位置路径。 默认值为 Visual Studio Code 工作文件夹。
   * **Azurite无**提示模式禁用访问日志。 默认值为 **false**。

## <a name="install-and-run-azurite-by-using-npm"></a>使用 NPM 安装并运行 Azurite

此安装方法需要安装 node.js [8.0 版或更高版本](https://nodejs.org)。 **npm**是每个 node.js 安装随附的包管理工具。 安装 node.js 后, 执行以下**npm**命令来安装 Azurite。

```console
npm install -g azurite
```

安装 Azurite 后, 请参阅[从命令行运行 Azurite](#run-azurite-from-a-command-line)。

## <a name="install-and-run-the-azurite-docker-image"></a>安装并运行 Azurite Docker 映像

使用[DockerHub](https://hub.docker.com/)通过以下命令拉取[最新的 Azurite 映像](https://hub.docker.com/_/microsoft-azure-storage-azurite):

```console
docker pull mcr.microsoft.com/azure-storage/azurite
```

**运行 Azurite Docker 映像**:

以下命令运行 Azurite Docker 映像。 参数`-p 10000:10000`将请求从主机的端口10000重定向到 Docker 实例。

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite
```

**指定工作区位置**:

在下面的示例中, `-v c:/azurite:/data`参数指定`c:/azurite`为 Azurite 持久化数据位置。

```console
docker run -p 10000:10000 -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**设置所有 Azurite 参数**:

此示例演示如何设置所有命令行参数。 下面的所有参数都应放置在单个命令行上。

```console
docker run -p 8888:8888
           -v c:/azurite:/workspace mcr.microsoft.com/azure-storage/azurite azurite
           -l /workspace
           -d /workspace/debug.log
           --blobPort 8888
           --blobHost 0.0.0.0
```

有关在启动时配置 Azurite 的详细信息, 请参阅[命令行选项](#command-line-options)。

## <a name="clone-build-and-run-azurite-from-the-github-repository"></a>从 GitHub 存储库克隆、构建和运行 Azurite

此安装方法需要安装[Git](https://git-scm.com/) 。 使用以下控制台命令克隆 Azurite 项目的[GitHub 存储库](https://github.com/azure/azurite)。

```console
git clone https://github.com/Azure/Azurite.git
```

克隆源代码后, 从克隆的存储库的根目录中执行以下命令以生成并安装 Azurite。

```console
npm install
npm run build
npm install -g
```

安装和生成 Azurite 后, 请参阅[从命令行运行 Azurite](#run-azurite-from-a-command-line)。

## <a name="run-azurite-from-a-command-line"></a>从命令行运行 Azurite

> [!NOTE]
> 如果只安装了 Visual Studio Code 扩展, 则不能从命令行运行 Azurite。 请改用 VS Code 命令面板。 有关详细信息, 请参阅[安装并运行 Azurite Visual Studio Code 扩展](#install-and-run-the-azurite-visual-studio-code-extension)。

若要立即开始使用命令行, 请创建名为**c:\azurite**的目录, 并通过发出以下命令启动 azurite:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

此命令通知 Azurite 将所有数据存储在特定目录**c:\azurite**中。 如果省略 **--location**选项, 它将使用当前工作目录。

## <a name="command-line-options"></a>命令行选项

本部分详细介绍了启动 Azurite 时可用的命令行开关。 所有命令行开关都是可选的。

```console
C:\Azurite> azurite [--blobHost <IP address>] [--blobPort <port address>]
    [-l | --location <workspace path>] [-s | --silent] [-d | --debug <log file path>]
```

**-L**开关是 **--location**的快捷方式,- **s**是 **--缄默**的快捷方式, 而 **-d**是 **--debug**的快捷方式。

### <a name="listening-host"></a>侦听主机

**可选**默认情况下, Azurite 将以本地服务器的形式侦听127.0.0.1。 使用 **--blobHost**开关将地址设置为你的要求。

仅在本地计算机上接受请求:

```console
azurite --blobHost 127.0.0.1
```

允许远程请求:

```console
azurite --blobHost 0.0.0.0
```

> [!CAUTION]
> 允许远程请求可能会使系统容易受到外部攻击。

### <a name="listening-port-configuration"></a>侦听端口配置

**可选**默认情况下, Azurite 会侦听端口10000上的 Blob 服务。 使用 **--blobPort**开关指定所需的侦听端口。

> [!NOTE]
> 使用自定义端口后, 需要在 Azure 存储工具或 Sdk 中更新连接字符串或相应的配置。

自定义 Blob 服务侦听端口:

```console
azurite --blobPort 8888
```

让系统自动选择可用端口:

```console
azurite --blobPort 0
```

在 Azurite 启动过程中, 将显示正在使用的端口。

### <a name="workspace-path"></a>工作区路径

**可选**Azurite 在执行过程中将数据存储到本地磁盘。 使用 **--location**开关指定路径作为工作区位置。 默认情况下, 将使用当前进程工作目录。

```console
azurite --location c:\azurite
```

```console
azurite -l c:\azurite
```

### <a name="access-log"></a>访问日志

**可选**默认情况下, 访问日志显示在控制台窗口中。 使用 **--无声**开关禁用访问日志的显示。

```console
azurite --silent
```

```console
azurite -s
```

### <a name="debug-log"></a>调试日志

**可选**调试日志包含每个请求和异常堆栈跟踪的详细信息。 通过为 **--debug**开关提供有效的本地文件路径来启用调试日志。

```console
azurite --debug path/debug.log
```

```console
azurite -d path/debug.log
```

## <a name="authorization-for-tools-and-sdks"></a>工具和 Sdk 的授权

使用任何身份验证策略, 从 Azure 存储 Sdk 或工具 (如[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)) 连接到 Azurite。 需要身份验证。 Azurite 支持使用共享密钥和共享访问签名 (SAS) 的授权。 Azurite 还支持匿名访问公共容器。

### <a name="well-known-storage-account-and-key"></a>众所周知的存储帐户和密钥

可以在 Azurite 中使用以下帐户名和密钥。 这是旧 Azure 存储模拟器使用的众所周知的帐户和密钥。

* 帐户名:`devstoreaccount1`
* 帐户密钥:`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> 除了 SharedKey authentication 外, Azurite 还支持帐户和服务 SAS 身份验证。 如果将容器设置为允许公共访问, 则也可以使用匿名访问。

### <a name="connection-string"></a>连接字符串

从应用程序连接到 Azurite 的最简单方法是在应用程序的配置文件中配置引用快捷方式*UseDevelopmentStorage = true*的连接字符串。 下面是*app.config*文件中的连接字符串的示例:

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

有关详细信息，请参阅[配置 Azure 存储连接字符串](storage-configure-connection-string.md)。

### <a name="storage-explorer"></a>存储资源管理器

在 Azure 存储资源管理器中, 通过单击 "**添加帐户**" 图标连接到 Azurite, 然后选择 "**附加到本地模拟器**" 并单击 "**连接**"。

## <a name="differences-between-azurite-and-azure-storage"></a>Azurite 与 Azure 存储之间的差异

Azurite 的本地实例与云中的 Azure 存储帐户之间存在功能差异。

### <a name="endpoint-and-connection-url"></a>终结点和连接 URL

Azurite 的服务终结点不同于 Azure 存储帐户的终结点。 本地计算机不执行域名解析, 需要将 Azurite 终结点作为本地地址。

当你对 Azure 存储帐户中的资源进行寻址时, 帐户名称是 URI 主机名的一部分。 要寻址的资源是 URI 路径的一部分:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

下面的 URI 是 Azure 存储帐户中 blob 的有效地址:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

因为本地计算机不执行域名解析, 帐户名称是 URI 路径的一部分, 而不是主机名。 对 Azurite 中的资源使用以下 URI 格式:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

以下地址可用于访问 Azurite 中的 blob:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

### <a name="scaling-and-performance"></a>缩放和性能

Azurite 不是可扩展的存储服务, 并且不支持大量并发客户端。 没有性能保证。 Azurite 旨在用于开发和测试目的。

### <a name="error-handling"></a>错误处理。

Azurite 与 Azure 存储错误处理逻辑一致, 但有一些差异。 例如, 错误消息可能会不同, 而错误状态代码会对齐。

### <a name="ra-grs"></a>RA-GRS

Azurite 支持读取访问异地冗余复制 (GRS)。 对于存储资源, 通过在帐户名称后面追加 **-辅助**位置来访问辅助位置。 例如, 以下地址可用于在 Azurite 中使用只读辅助访问 blob:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite 是开源

欢迎使用 Azurite 的贡献和建议。 请参阅 Azurite [GitHub 项目](https://github.com/Azure/Azurite/projects)页或[github 问题](https://github.com/Azure/Azurite/issues), 了解我们为即将推出的功能和 bug 修复跟踪的里程碑和工作项。 还会在 GitHub 中跟踪详细的工作项。

## <a name="next-steps"></a>后续步骤

* [使用 azure 存储模拟器进行开发和测试](storage-use-emulator.md), 使旧的 Azure 存储模拟器被 Azurite 取代。
* [配置 Azure 存储连接字符串](storage-configure-connection-string.md)说明了如何汇编有效的 Azure 存储连接字符串。
