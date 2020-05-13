---
title: 使用 Azurite 模拟器进行本地 Azure 存储开发
description: Azurite 开源模拟器（预览版）提供一个免费的本地环境用于测试 Azure 存储应用程序。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/01/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: f4e0bbd546b770b9e81bb9142cdd97e3927db7bd
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195943"
---
# <a name="use-the-azurite-emulator-for-local-azure-storage-development-and-testing-preview"></a>使用 Azurite 模拟器进行本地 Azure 存储开发和测试（预览）

Azurite 版本 3.2 开源模拟器（预览版）提供一个免费的本地环境用于测试 Azure blob 和队列存储应用程序。 如果你对应用程序在本地的工作状况感到满意，可以改用云中的 Azure 存储帐户。 模拟器在 Windows、Linux 和 macOS 上提供跨平台支持。 Azurite v3 支持 Azure Blob 服务实现的 API。

Azurite 是未来的存储仿真器平台。 Azurite 取代了 [Azure 存储仿真器](storage-use-emulator.md)。 Azurite 将持续更新，以支持最新版本的 Azure 存储 API。

可通过多种不同的方法在本地系统上安装并运行 Azurite：

  1. [安装并运行 Azurite Visual Studio Code 扩展](#install-and-run-the-azurite-visual-studio-code-extension)
  1. [使用 NPM 安装并运行 Azurite](#install-and-run-azurite-by-using-npm)
  1. [安装并运行 Azurite Docker 映像](#install-and-run-the-azurite-docker-image)
  1. [从 GitHub 存储库克隆、生成并运行 Azurite](#clone-build-and-run-azurite-from-the-github-repository)

## <a name="install-and-run-the-azurite-visual-studio-code-extension"></a>安装并运行 Azurite Visual Studio Code 扩展

在 Visual Studio Code 中选择“扩展”窗格，然后在“扩展: 市场”中搜索 *Azurite*。  

![Visual Studio Code 扩展市场](media/storage-use-azurite/azurite-vs-code-extension.png)

你还可以在浏览器中导航到[Visual Studio Code 扩展市场](https://marketplace.visualstudio.com/items?itemName=Azurite.azurite)。 选择“安装”按钮打开 Visual Studio Code，并直接转到 Azurite 扩展页。 

可以在 Visual Studio Code 的状态栏中快速启动或关闭 Azurite。 单击 **[Azurite Blob service]** 或 **[Azurite Queue service]**。

此扩展插件支持以下 Visual Studio Code 命令。 若要打开命令面板，请在 Visual Studio Code 中按 F1。 

   - **Azurite：清理**-重置所有 Azurite 服务持久性数据
   - **Azurite：清理 Blob 服务**-清理 blob 服务
   - **Azurite：清理队列**服务-清理队列服务
   - **Azurite：关闭**所有 Azurite 服务
   - **Azurite：关闭 Blob 服务**-关闭 blob 服务
   - **Azurite：关闭队列服务**-关闭队列服务
   - **Azurite：启动**所有 Azurite 服务
   - **Azurite：启动 Blob 服务**-启动 blob 服务
   - **Azurite：启动队列服务**-启动队列服务

若要在 Visual Studio Code 中配置 Azurite，请选择“扩展”窗格。 选择 **Azurite** 所对应的“管理”****(齿轮)图标。 选择 "**扩展设置**"。

![Azurites 配置扩展设置](media/storage-use-azurite/azurite-configure-extension-settings.png)

支持以下设置：

   - **Azurite： Blob 主机**-blob 服务侦听终结点。 默认设置为 127.0.0.1。
   - **Azurite： Blob 端口**-blob 服务侦听端口。 默认端口为 10000。
   - **Azurite： Cert** -用于启用 HTTPS 模式的本地受信任 PEM 或 PFX 证书文件路径的路径。
   - **Azurite：调试**-将调试日志输出到 Azurite 通道。 默认值是 **false**秒。
   - **Azurite：** 指向本地受信任 PEM 密钥文件的路径，当**Azurite： Cert**指向 PEM 文件时需要此文件。
   - **Azurite： Location** -工作区位置路径。 默认值为 Visual Studio Code 的工作文件夹。
   - **Azurite：松**-启用宽松模式，这会忽略不支持的标头和参数。
   - **Azurite： oauth** -可选 oauth 级别。
   - **Azurite：** 密码-PFX 文件的密码。 当**Azurite： Cert**指向 PFX 文件时是必需的。
   - **Azurite： Queue Host** -队列服务侦听终结点。 默认设置为 127.0.0.1。
   - **Azurite：队列端口**-队列服务侦听端口。 默认端口为 10001。
   - **Azurite：无提示**模式禁用访问日志。 默认值是 **false**秒。

## <a name="install-and-run-azurite-by-using-npm"></a>使用 NPM 安装并运行 Azurite

此安装方法需要安装 [Node.js 8.0 或更高版本](https://nodejs.org)。 Node 包管理器（npm）是每个 node.js 安装附带的包管理工具。 安装 node.js 后，执行以下 `npm` 命令以安装 Azurite。

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
docker run -p 10000:10000 -p 10001:10001 \
    mcr.microsoft.com/azure-storage/azurite
```

**指定工作区位置**：

在以下示例中，`-v c:/azurite:/data` 参数指定“c:/azurite”** 作为 Azurite 永久性数据的位置。 必须在运行 Docker 命令之前创建目录“c:/azurite”**。

```console
docker run -p 10000:10000 -p 10001:10001 \
    -v c:/azurite:/data mcr.microsoft.com/azure-storage/azurite
```

**仅运行 blob 服务**

```console
docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite \
    azurite-blob --blobHost 0.0.0.0 --blobPort 10000
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

安装和生成 Azurite 后，请参阅[从命令行运行 Azurite](#run-azurite-from-a-command-line)。

## <a name="run-azurite-from-a-command-line"></a>从命令行运行 Azurite

> [!NOTE]
> 如果只是安装了 Visual Studio Code 扩展，则无法从命令行运行 Azurite。 请改用 Visual Studio Code 命令面板。 有关详细信息，请参阅[安装并运行 Azurite Visual Studio Code 扩展](#install-and-run-the-azurite-visual-studio-code-extension)。

若要立即开始使用命令行，请创建名为*c:\azurite*的目录，并通过发出以下命令启动 azurite：

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

此命令告知 Azurite 要将所有数据存储在特定目录 *c:\azurite* 中。 如果 `--location` 省略该选项，它将使用当前工作目录。

## <a name="command-line-options"></a>命令行选项

本部分详细介绍了启动 Azurite 时可用的命令行开关。

### <a name="help"></a>帮助

**可选**-通过使用或开关获取命令行帮助 `-h` `--help` 。

```console
azurite -h
azurite --help
```

### <a name="blob-listening-host"></a>Blob 侦听主机

**可选**-默认情况下，Azurite 将以本地服务器的形式侦听127.0.0.1。 使用 `--blobHost` 开关将地址设置为你的要求。

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

**可选**-默认情况下，Azurite 将在端口10000上侦听 Blob 服务。 使用 `--blobPort` 开关指定所需的侦听端口。

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

**可选**-默认情况下，Azurite 将以本地服务器的形式侦听127.0.0.1。 使用 `--queueHost` 开关将地址设置为你的要求。

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

**可选**-默认情况下，Azurite 会侦听端口10001上的队列服务。 使用 `--queuePort` 开关指定所需的侦听端口。

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

**可选**-Azurite 在执行过程中将数据存储到本地磁盘。 使用 `-l` 或 `--location` 开关将路径指定为工作区位置。 默认将使用当前进程的工作目录。 请注意小写的 "l"。

```console
azurite -l c:\azurite
azurite --location c:\azurite
```

### <a name="access-log"></a>访问日志

**可选**-默认情况下，访问日志显示在控制台窗口中。 使用或开关禁用访问日志的显示 `-s` `--silent` 。

```console
azurite -s
azurite --silent
```
### <a name="debug-log"></a>调试日志

**可选**-调试日志包含每个请求和异常堆栈跟踪的详细信息。 通过提供到或开关的有效本地文件路径来启用调试 `-d` 日志 `--debug` 。

```console
azurite -d path/debug.log
azurite --debug path/debug.log
```

### <a name="loose-mode"></a>松散模式

**可选**-默认情况下，Azurite 应用严格模式来阻止不受支持的请求标头和参数。 使用或开关禁用严格模式 `-L` `--loose` 。 请注意大写 "L"。

```console
azurite -L
azurite --loose
```
### <a name="version"></a>版本

**可选**-使用或开关显示已安装的 Azurite 版本号 `-v` `--version` 。

```console
azurite -v
azurite --version
```

### <a name="certificate-configuration-https"></a>证书配置（HTTPS）

**可选**-默认情况下，AZURITE 使用 HTTP 协议。 通过向交换机提供隐私增强邮件（pem）或[个人信息交换（.pfx）](https://docs.microsoft.com/windows-hardware/drivers/install/personal-information-exchange---pfx--files)证书文件的路径，启用 HTTPS 模式 `--cert` 。

为 `--cert` PEM 文件提供时，必须提供相应的 `--key` 开关。

```console
azurite --cert path/server.pem --key path/key.pem
```

为 `--cert` PFX 文件提供时，必须提供相应的 `--pwd` 开关。

```console
azurite --cert path/server.pfx --pwd pfxpassword
```

有关创建 PEM 和 PFX 文件的详细信息，请参阅[HTTPS 设置](https://github.com/Azure/Azurite/blob/master/README.md#https-setup)。

### <a name="oauth-configuration"></a>OAuth 配置

**可选**-使用开关为 Azurite 启用 OAuth 身份验证 `--oauth` 。

```console
azurite --oauth basic --cert path/server.pem --key path/key.pem
```

> [!NOTE]
> OAuth 需要 HTTPS 终结点。 请确保通过提供 `--cert` 开关以及开关来启用 HTTPS `--oauth` 。

Azurite 通过指定开关的参数来支持基本身份验证 `basic` `--oauth` 。 Azurite 将执行基本身份验证，如验证传入持有者令牌、检查颁发者、受众和到期时间。 Azurite 不会检查令牌签名或权限。

## <a name="authorization-for-tools-and-sdks"></a>工具和 SDK 的授权

使用任何身份验证策略从 Azure 存储 SDK 或工具（例如 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)）连接到 Azurite。 需要身份验证。 Azurite 支持使用 OAuth、共享密钥和共享访问签名（SAS）进行授权。 Azurite 还支持匿名访问公共容器。

如果使用的是 Azure Sdk，请使用选项启动 Azurite `--oauth basic and --cert --key/--pwd` 。

### <a name="well-known-storage-account-and-key"></a>已知的存储帐户和密钥

Azurite 接受旧版 Azure 存储模拟器使用的众所周知帐户和密钥。

- 帐户名称：`devstoreaccount1`
- 帐户密钥：`Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### <a name="custom-storage-accounts-and-keys"></a>自定义存储帐户和密钥

Azurite 支持自定义存储帐户名称和密钥，但需将 `AZURITE_ACCOUNTS` 环境变量设置为以下格式：`account1:key1[:key2];account2:key1[:key2];...`。

例如，使用包含一个密钥的自定义存储帐户：

```cmd
set AZURITE_ACCOUNTS="account1:key1"
```

```bash
export AZURITE_ACCOUNTS="account1:key1"
```

或使用多个存储帐户，每个存储帐户有两个密钥：

```cmd
set AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

```bash
export AZURITE_ACCOUNTS="account1:key1:key2;account2:key1:key2"
```

默认情况下，Azurite 每分钟刷新环境变量中的自定义帐户名和密钥。 利用此功能，可以动态轮换帐户密钥，或添加新的存储帐户，而无需重启 Azurite。

> [!NOTE]
> 设置自定义存储帐户时，将禁用默认的 `devstoreaccount1` 存储帐户。

### <a name="connection-strings"></a>连接字符串

从应用程序连接到 Azurite 的最简单方法是在应用程序的配置文件中配置一个引用快捷方式 *UseDevelopmentStorage=true* 的连接字符串。 下面是 *app.config* 文件中的连接字符串示例：

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="http-connection-strings"></a>HTTP 连接字符串

可以将以下连接字符串传递给[Azure sdk](https://aka.ms/azsdk)或工具，如 Azure CLI 2.0 或存储资源管理器。

完整的连接字符串是：

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

若要仅连接到 blob 服务，连接字符串是：

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;`

若要仅连接到队列服务，连接字符串是：

`DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;`

#### <a name="https-connection-strings"></a>HTTPS 连接字符串

完整的 HTTPS 连接字符串是：

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

若要仅使用 blob 服务，HTTPS 连接字符串为：

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;`

若要仅使用队列服务，HTTPS 连接字符串为：

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;`

如果你使用 `dotnet dev-certs` 生成自签名证书，请使用以下连接字符串。

`DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://localhost:10000/devstoreaccount1;QueueEndpoint=https://localhost:10001/devstoreaccount1;`

使用[自定义存储帐户和密钥](#custom-storage-accounts-and-keys)更新连接字符串。

有关详细信息，请参阅[配置 Azure 存储连接字符串](storage-configure-connection-string.md)。

### <a name="azure-sdks"></a>Azure SDK

若要将 Azurite 与[Azure sdk](https://aka.ms/azsdk)配合使用，请使用 OAUTH 和 HTTPS 选项：

```console
azurite --oauth basic --cert certname.pem --key certname-key.pem
```

#### <a name="azure-blob-storage"></a>Azure Blob 存储

然后，可以实例化 BlobContainerClient、Blobserviceclient 使用或 BlobClient。

```csharp
// With container URL and DefaultAzureCredential
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new BlobContainerClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "container-name"
  );

// With account name and key
var client = new BlobContainerClient(
    new Uri("https://127.0.0.1:10000/devstoreaccount1/container-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

#### <a name="azure-queue-storage"></a>Azure Queue Storage

还可以实例化 QueueClient 或 QueueServiceClient。

```csharp
// With queue URL and DefaultAzureCredential
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"), new DefaultAzureCredential()
  );

// With connection string
var client = new QueueClient(
    "DefaultEndpointsProtocol=https;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=https://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=https://127.0.0.1:10001/devstoreaccount1;", "queue-name"
  );

// With account name and key
var client = new QueueClient(
    new Uri("https://127.0.0.1:10001/devstoreaccount1/queue-name"),
    new StorageSharedKeyCredential("devstoreaccount1", "Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==")
  );
```

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure 存储资源管理器

您可以使用存储资源管理器查看存储在 Azurite 中的数据。

#### <a name="connect-to-azurite-using-http"></a>使用 HTTP 连接到 Azurite

在存储资源管理器中，按照以下步骤连接到 Azurite：

 1. 选择 "**管理帐户**" 图标
 1. 选择 "**添加帐户**"
 1. 选择 "**附加到本地模拟器**"
 1. 选择 **“下一步”**。
 1. 将 "**显示名称**" 字段编辑为你选择的名称
 1. 再次选择 "**下一步**"
 1. 选择**连接**

#### <a name="connect-to-azurite-using-https"></a>使用 HTTPS 连接到 Azurite

默认情况下，存储资源管理器不会打开使用自签名证书的 HTTPS 终结点。 如果正在使用 HTTPS 运行 Azurite，则可能使用的是自签名证书。 在存储资源管理器中，通过 "**编辑**  ->  **ssl 证书**" "  ->  **导入证书**" 对话框导入 ssl 证书。

##### <a name="import-certificate-to-storage-explorer"></a>将证书导入存储资源管理器

1. 查找本地计算机上的证书。
1. 在存储资源管理器中，请参阅**编辑**  ->  **SSL 证书**  ->  **导入证书**和导入证书。

如果不导入证书，则会出现错误：

`unable to verify the first certificate` 或 `self signed certificate in chain`

##### <a name="add-azurite-via-https-connection-string"></a>通过 HTTPS 连接字符串添加 Azurite

按照以下步骤将 Azurite HTTPS 添加到存储资源管理器：

1. 选择**切换资源管理器**
1. 选择**本地 &**
1. 右键单击 "**存储帐户**"，然后选择 "**连接到 Azure 存储**"。
1. 选择 "**使用连接字符串**"
1. 选择“下一步”  。
1. 在 "**显示名称**" 字段中输入一个值。
1. 在本文档的上一节中输入[HTTPS 连接字符串](#https-connection-strings)
1. 选择 **“下一步”**。
1. 选择**连接**

## <a name="workspace-structure"></a>工作区结构

初始化 Azurite 时，可以在工作区位置创建以下文件和文件夹。

- `__blobstorage__`-包含 Azurite blob 服务持久化二进制数据的目录
- `__queuestorage__`-包含 Azurite 队列服务持久化二进制数据的目录
- `__azurite_db_blob__.json`-Azurite blob 服务元数据文件
- `__azurite_db_blob_extent__.json`-Azurite blob 服务区元数据文件
- `__azurite_db_queue__.json`-Azurite queue service metadata file
- `__azurite_db_queue_extent__.json`-Azurite 队列服务区元数据文件

若要清理 Azurite，请删除以上文件和文件夹，然后重新启动模拟器。

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

Azurite 不支持大量连接的客户端。 它不提供性能方面的保证。 Azurite 用于开发和测试目的。

### <a name="error-handling"></a>错误处理

Azurite 的错误处理逻辑与 Azure 存储相符，但存在一些差别。 例如，错误消息可能不同，而错误状态代码是一致的。

### <a name="ra-grs"></a>RA-GRS

Azurite 支持读取访问异地冗余复制 (RA-GRS)。 对于存储资源，通过在帐户名称后面追加来访问辅助位置 `-secondary` 。 例如，以下地址可用于访问 Azurite 中使用只读辅助副本的 Blob：

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## <a name="azurite-is-open-source"></a>Azurite 是开源的

欢迎为 Azurite 贡献作品和提出建议。 请访问 Azurite [GitHub 项目](https://github.com/Azure/Azurite/projects)页或 [GitHub 问题](https://github.com/Azure/Azurite/issues)了解产品里程碑，以及我们针对即将推出的功能和 bug 修复正在跟进的工作项。 GitHub 中也列出了详细的工作项。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 存储仿真器进行开发和测试](storage-use-emulator.md)介绍了旧式 Azure 存储仿真器（现已由 Azurite 取代）。
- [配置 Azure 存储连接字符串](storage-configure-connection-string.md)介绍了如何汇编有效的 Azure 存储连接字符串。
