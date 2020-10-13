---
title: 在 Azure Cosmos DB 模拟器的本地安装和开发
description: 了解如何在 Windows、Linux、macOS 和 Windows docker 环境中安装和使用 Azure Cosmos DB 模拟器。 使用模拟器，可以在本地免费开发和测试应用程序，而无需创建 Azure 订阅。
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/22/2020
ms.custom: devx-track-csharp, contperfq1
ms.openlocfilehash: 64da8084ec8d40e17a0005f2e70486c7d51bf640
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91627580"
---
# <a name="install-and-use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>安装并使用 Azure Cosmos 模拟器进行本地开发和测试

Azure Cosmos 模拟器提供了一个模拟 Azure Cosmos DB 服务以进行开发的本地环境。 使用 Azure Cosmos 模拟器，可以在本地开发和测试应用程序，而无需创建 Azure 订阅，也不会产生任何费用。 如果对应用程序在 Azure Cosmos 模拟器中的工作情况感到满意，则可以切换到在云中使用 Azure Cosmos 帐户。 本文介绍如何在 Windows、Linux、macOS 和 Windows docker 环境中安装和使用模拟器。

## <a name="download-the-emulator"></a>下载模拟器

若要开始，请在本地计算机上下载并安装最新版本的 Azure Cosmos 模拟器。 [模拟器发行说明](local-emulator-release-notes.md)一文列出了每个版本中的所有可用版本和功能更新。

:::image type="icon" source="media/local-emulator/download-icon.png" border="false":::**[下载 Azure Cosmos 模拟器](https://aka.ms/cosmosdb-emulator)**

可以使用 Azure Cosmos 模拟器和 [SQL](local-emulator.md#sql-api)、 [Cassandra](local-emulator.md#cassandra-api)、 [MongoDB](local-emulator.md#azure-cosmos-dbs-api-for-mongodb)、 [Gremlin](local-emulator.md#gremlin-api)和 [表](local-emulator.md#table-api) API 帐户来开发应用程序。 目前，模拟器中的数据资源管理器完全支持仅查看 SQL 数据;使用 MongoDB、Gremlin/Graph 和 Cassandra 客户端应用程序创建的数据此时无法查看。 若要了解详细信息，请参阅如何从不同 Api [连接到模拟器终结点](#connect-with-emulator-apis) 。

## <a name="how-does-the-emulator-work"></a>模拟器的工作原理是什么？

Azure Cosmos 模拟器提供 Azure Cosmos DB 服务的高保真模拟。 它支持作为 Azure Cosmos DB 的等效功能，其中包括创建数据、查询数据、预配和缩放容器以及执行存储过程和触发器。 可以使用 Azure Cosmos 模拟器开发和测试应用程序，并通过更新 Azure Cosmos DB 连接终结点将其部署到全球范围的 Azure。

虽然模拟 Azure Cosmos DB 服务很逼真，但模拟器的实现不同于服务。 例如，模拟器使用标准 OS 组件，例如用于暂留的本地文件系统和用于连接的 HTTPS 协议堆栈。 使用模拟器时，依赖于 Azure 基础结构（如全局复制、读/写的一位数毫秒延迟和可优化的一致性级别）的功能不适用。

可以通过使用 [Azure Cosmos DB 数据迁移工具](https://github.com/azure/azure-documentdb-datamigrationtool)在 Azure Cosmos 模拟器与 Azure Cosmos DB 服务之间迁移数据。

## <a name="differences-between-the-emulator-and-the-cloud-service"></a>模拟器与云服务之间的差异

由于 Azure Cosmos 模拟器提供在本地开发人员工作站上运行的模拟环境，因此模拟器与云中的 Azure Cosmos 帐户之间存在一些功能差异：

* 目前，模拟器中的 " **数据资源管理器** " 窗格完全仅支持 SQL API 客户端。 不完全支持 Azure Cosmos DB Api 的 **数据资源管理器** 视图和操作，例如 MongoDB、Table、Graph 和 Cassandra api。

* 模拟器仅支持一个固定的帐户和一个众所周知的主密钥。 使用 Azure Cosmos 模拟器时，无法重新生成密钥，不过，可以使用 [命令行](emulator-command-line-parameters.md) 选项更改默认密钥。

* 使用模拟器，只能在 [预配吞吐量](set-throughput.md) 模式下创建 Azure Cosmos 帐户;它目前不支持 [无服务器](serverless.md) 模式。

* 模拟器不是可缩放的服务，并且不支持大量的容器。 使用 Azure Cosmos 模拟器时，默认情况下，最多可以在 400 RU/s 创建25个固定大小的容器 (仅支持使用 Azure Cosmos DB Sdk) 或5个无限制容器。 有关如何更改此值的详细信息，请参阅 [设置 PartitionCount 值一](emulator-command-line-parameters.md#set-partitioncount) 文。

* 模拟器不提供与云服务不同的 [Azure Cosmos DB 一致性级别](consistency-levels.md) 。

* 模拟器不提供 [多区域复制](distribute-data-globally.md)。

* 由于 Azure Cosmos 模拟器的副本可能并不总是与 Azure Cosmos DB 服务中最新的更改有关，因此应始终参阅 [Azure Cosmos DB 容量规划](estimate-ru-with-capacity-planner.md) 器以准确估计应用程序所需的吞吐量 (ru) 需求。

* 模拟器支持的最大 ID 属性大小为254个字符。

## <a name="install-the-emulator"></a>安装模拟器

安装仿真程序之前，请确保满足以下硬件和软件要求：

* 所需软件：
  * 目前，Windows Server 2012 R2、Windows Server 2016、2019或 Windows 8、10主机操作系统都受支持。 当前不支持启用了 Active Directory 的主机操作系统。
  * 64 位操作系统

* 最低硬件要求：
  * 2-GB RAM
  * 10-GB 可用硬盘空间

* 若要安装、配置和运行 Azure Cosmos 模拟器，必须在计算机上具有管理权限。 模拟器将添加一个证书，并设置防火墙规则，以便运行其服务。 因此，需要管理员权限才能执行此类操作。

若要开始，请在本地计算机上下载并安装最新版本的 [Azure Cosmos 模拟器](https://aka.ms/cosmosdb-emulator) 。 如果在安装模拟器时遇到任何问题，请参阅 [模拟器故障排除](troubleshoot-local-emulator.md) 文章以进行调试。

根据你的系统要求，你可以在 [Windows](#run-on-windows)、 [用于 Windows 的 Docker](#run-on-windows-docker)、 [Linux 或 macOS](#run-on-linux-macos) 上运行模拟器，如本文后续部分中所述。

## <a name="check-for-emulator-updates"></a>检查模拟器更新

每个版本的模拟器附带一组功能更新或 bug 修复。 若要查看可用版本，请阅读 [模拟器发行说明](local-emulator-release-notes.md) 一文。

安装后，如果已使用默认设置，则与模拟器相对应的数据将保存在%LOCALAPPDATA%\CosmosDBEmulator 位置。 您可以使用可选的数据路径设置来配置其他位置; `/DataPath=PREFERRED_LOCATION` 作为 [命令行参数](emulator-command-line-parameters.md)。 在 Azure Cosmos 模拟器的一个版本中创建的数据不保证在使用不同版本时可以访问。 如果需要长期保存数据，建议将该数据存储在 Azure Cosmos 帐户，而不是 Azure Cosmos 模拟器中。

## <a name="use-the-emulator-on-windows"></a><a id="run-on-windows"></a>在 Windows 上使用模拟器

默认情况下，Azure Cosmos 模拟器安装在 `C:\Program Files\Azure Cosmos DB Emulator` 位置。 若要在 Windows 上启动 Azure Cosmos 模拟器，请选择 " **开始** " 按钮或按 windows 键。 开始键入“Azure Cosmos 模拟器”，再从应用程序列表中选择该模拟器。

:::image type="content" source="./media/local-emulator/database-local-emulator-start.png" alt-text="选择 &quot;开始&quot; 按钮或按 Windows 键，开始键入 &quot;Azure Cosmos 模拟器&quot;，然后从应用程序列表中选择该模拟器":::

启动模拟器后，Windows 任务栏通知区域中会显示一个图标。 它会自动在浏览器的此 URL url 中打开 Azure Cosmos 数据资源管理器 `https://localhost:8081/_explorer/index.html` 。

:::image type="content" source="./media/local-emulator/database-local-emulator-taskbar.png" alt-text="选择 &quot;开始&quot; 按钮或按 Windows 键，开始键入 &quot;Azure Cosmos 模拟器&quot;，然后从应用程序列表中选择该模拟器" ) 在端口8081上侦听。 地址显示为 `https://localhost:8081/_explorer/index.html`。 如果关闭资源管理器，稍后又想要重新打开它，可以在浏览器中打开该 URL，或者在 Windows 任务栏图标中从 Azure Cosmos 模拟器启动它，如下所示。

:::image type="content" source="./media/local-emulator/database-local-emulator-data-explorer-launcher.png" alt-text="选择 &quot;开始&quot; 按钮或按 Windows 键，开始键入 &quot;Azure Cosmos 模拟器&quot;，然后从应用程序列表中选择该模拟器":::

## <a name="use-the-emulator-on-docker-for-windows"></a><a id="run-on-windows-docker"></a>在用于 Windows 的 Docker 上使用模拟器

可以在 Windows Docker 容器上运行 Azure Cosmos 模拟器。 有关的详细信息，请参阅 [Docker Hub](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/) docker pull 命令和 [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker) `Dockerfile` 。 目前，仿真程序无法在 Docker 上运行 Oracle Linux。 使用以下说明在用于 Windows 的 Docker 上运行模拟器：

1. 安装 [用于 Windows 的 Docker](https://www.docker.com/docker-windows) 后，通过右键单击工具栏上的 Docker 图标，然后选择 " **切换到 windows 容器**"，切换到 windows 容器。

1. 接下来，通过从你喜欢使用的 shell 运行以下命令，从 Docker 中心拉取模拟器映像。

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```

1. 若要启动映像，请运行以下命令，具体取决于命令行或 PowerShell 环境：

   # <a name="command-line"></a>[命令行](#tab/cli)

   ```bash

   md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```
   基于 windows 的 Docker 映像通常不与每个 Windows 主机操作系统兼容。 例如，默认的 Azure Cosmos 模拟器映像仅兼容于 Windows 10 和 Windows Server 2016。 如果需要与 Windows Server 2019 兼容的映像，请改为运行以下命令：

   ```bash
   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%hostDirectory%,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/winsrv2019/azure-cosmos-emulator:latest
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell

   md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

   ```

   响应类似于以下内容：

   ```bash
   Starting emulator
   Emulator Endpoint: https://172.20.229.193:8081/
   Primary Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   Exporting SSL Certificate
   You can import the SSL certificate from an administrator command prompt on the host by running:
   cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
   powershell .\importcert.ps1
   --------------------------------------------------------------------------------------------------
   Starting interactive shell
   ```
   ---

   > [!NOTE]
   > 执行命令时 `docker run` ，如果出现端口冲突错误 (即指定的端口已在使用) ，请通过更改端口号来传递自定义端口。 例如，可以将 "-p 8081:8081" 参数更改为 "-p 443:8081"

1. 现在，使用响应中的模拟器终结点和主密钥，并将 TLS/SSL 证书导入到主机中。 若要导入 TLS/SSL 证书，请在管理员命令提示符下运行以下步骤：

   # <a name="command-line"></a>[命令行](#tab/cli)

   ```bash
   cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
   powershell .\importcert.ps1
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell
   cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
   .\importcert.ps1
   ```
   ---

1. 如果在仿真程序启动后关闭交互式 shell，它会关闭模拟器的容器。 若要重新打开数据资源管理器，请在浏览器中导航到以下 URL。 上面所示的响应消息中提供了模拟器终结点。

   `https://<emulator endpoint provided in response>/_explorer/index.html`

如果你有一个在 Linux docker 容器上运行的 .NET 客户端应用程序，并且如果在主机上运行 Azure Cosmos 模拟器，请按照下一节中的说明将证书导入到 Linux docker 容器。

### <a name="regenerate-the-emulator-certificates-when-running-on-a-docker-container"></a>在 Docker 容器上运行时重新生成模拟器证书

在 Docker 容器中运行模拟器时，每次停止并重新启动各自的容器时，都会重新生成与模拟器关联的证书。 因此，必须在每个容器启动后重新导入证书。 若要解决此限制，可以使用 Docker 合成文件将 Docker 容器绑定到特定的 IP 地址和容器映像。

例如，你可以在 Docker 合成文件中使用以下配置，请确保根据你的要求对其进行格式化： 

```yml
version: '2.4' # Do not upgrade to 3.x yet, unless you plan to use swarm/docker stack: https://github.com/docker/compose/issues/4513

networks:
  default:
    external: false
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"

services:

  # First create a directory that will hold the emulator traces and certificate to be imported
  # set hostDirectory=C:\emulator\bind-mount
  # mkdir %hostDirectory%

  cosmosdb:
    container_name: "azurecosmosemulator"
    hostname: "azurecosmosemulator"
    image: 'mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator'
    platform: windows
    tty: true
    mem_limit: 3GB
    ports:
        - '8081:8081'
        - '8900:8900'
        - '8901:8901'
        - '8902:8902'
        - '10250:10250'
        - '10251:10251'
        - '10252:10252'
        - '10253:10253'
        - '10254:10254'
        - '10255:10255'
        - '10256:10256'
        - '10350:10350'
    networks:
      default:
        ipv4_address: 172.16.238.246
    volumes:
        - '${hostDirectory}:C:\CosmosDB.Emulator\bind-mount'
```

## <a name="use-the-emulator-on-linux-or-macos"></a><a id="run-on-linux-macos"></a>在 Linux 或 macOS 上使用模拟器

目前只能在 Windows 上运行 Azure Cosmos 模拟器。 如果你使用的是 Linux 或 macOS，则可以在虚拟机监控程序（如类似或 VirtualBox）中运行的 Windows 虚拟机上运行模拟器。

> [!NOTE]
> 每次重新启动虚拟机监控程序中托管的 Windows 虚拟机时，都必须重新导入证书，因为虚拟机的 IP 地址会发生更改。 如果已将虚拟机配置为保留 IP 地址，则不需要导入证书。

使用以下步骤在 Linux 或 macOS 环境中使用模拟器：

1. 在 Windows 虚拟机中运行以下命令，并记下 IPv4 地址：

   ```bash
   ipconfig.exe
   ```

1. 在应用程序中，将终结点 URL 更改为使用返回的 IPv4 地址， `ipconfig.exe` 而不是 `localhost` 。

1. 通过 Windows VM，使用以下选项从命令行启动 Azure Cosmos 模拟器。 有关命令行支持的参数的详细信息，请参阅 [模拟器命令行工具参考](emulator-command-line-parameters.md)：

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM +4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   ```

1. 最后，需要在运行在 Linux 或 Mac 环境上的应用程序与模拟器之间解析证书信任进程。 可以使用以下两个选项之一来解析证书：

   1. 将[模拟器 TLS/SSL 证书导入到 Linux 或 Mac 环境](#import-certificate)或
   2. [在应用程序中禁用 TLS/SSL 验证](#disable-ssl-validation)

### <a name="option-1-import-the-emulator-tlsssl-certificate"></a><a id="import-certificate"></a>选项1：导入模拟器 TLS/SSL 证书

以下部分介绍如何将仿真程序 TLS/SSL 证书导入到 Linux 和 macOS 环境中。

#### <a name="linux-environment"></a>Linux 环境

如果在 Linux 中工作，则 .NET 依赖于 OpenSSL 来执行验证：

1. [导出 PFX 格式的证书](local-emulator-export-ssl-certificates.md#export-emulator-certificate)。 选择导出私钥时，PFX 选项可用。

1. 将该 PFX 文件复制到 Linux 环境。

1. 将 PFX 文件转换为 CRT 文件

   ```bash
   openssl pkcs12 -in YourPFX.pfx -clcerts -nokeys -out YourCTR.crt
   ```

1. 将 CRT 文件复制到你的 Linux 分发版中包含自定义证书的文件夹。 在 Debian 分发版中，它通常位于 `/usr/local/share/ca-certificates/`。

   ```bash
   cp YourCTR.crt /usr/local/share/ca-certificates/
   ```

1. 更新 TLS/SSL 证书，这将更新 `/etc/ssl/certs/` 文件夹。

   ```bash
   update-ca-certificates
   ```

#### <a name="macos-environment"></a>macOS 环境

如果在 Mac 中工作，请使用以下步骤：

1. [导出 PFX 格式的证书](local-emulator-export-ssl-certificates.md#export-emulator-certificate)。 选择导出私钥时，PFX 选项可用。

1. 将该 PFX 文件复制到 Mac 环境。

1. 打开 *Keychain Access* 应用程序并导入 PFX 文件。

1. 打开证书列表，并找到名为 `localhost` 的证书。

1. 打开该特定项的上下文菜单，选择“获取项”，然后在“信任” > “使用此证书时”选项下选择“始终信任”。   

   :::image type="content" source="./media/local-emulator/mac-trust-certificate.png" alt-text="选择 &quot;开始&quot; 按钮或按 Windows 键，开始键入 &quot;Azure Cosmos 模拟器&quot;，然后从应用程序列表中选择该模拟器":::
  
### <a name="option-2-disable-the-ssl-validation-in-the-application"></a><a id="disable-ssl-validation"></a>选项2：在应用程序中禁用 SSL 验证

建议仅出于开发目的禁用 SSL 验证，并且在生产环境中运行时不应这样做。 下面的示例演示如何对 .NET 和 Node.js 应用程序禁用 SSL 验证。

# <a name="net-standard-21"></a>[.NET Standard 2.1+](#tab/ssl-netstd21)

对于在与 .NET Standard 2.1 或更高版本兼容的框架中运行的任何应用程序，我们可以利用 `CosmosClientOptions.HttpClientFactory`：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET Standard 2.0](#tab/ssl-netstd20)

对于在与 .NET Standard 2.0 兼容的框架中运行的任何应用程序，我们可以利用 `CosmosClientOptions.HttpClientFactory`：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

# <a name="nodejs"></a>[Node.js](#tab/ssl-nodejs)

对于 Node.js 应用程序，可以在启动应用程序时修改 `package.json` 文件以设置 `NODE_TLS_REJECT_UNAUTHORIZED`：

```json
"start": NODE_TLS_REJECT_UNAUTHORIZED=0 node app.js
```
--- 

## <a name="enable-access-to-emulator-on-a-local-network"></a>在本地网络上启用对模拟器的访问

如果你有多台计算机使用单个网络，并且你在一台计算机上设置了模拟器，并且想要从其他计算机访问它。 在这种情况下，需要在本地网络上启用对仿真器的访问。

可在本地网络中运行仿真器。 要启用网络访问，请在[命令行](emulator-command-line-parameters.md)中指定 `/AllowNetworkAccess` 选项，同时还需指定 `/Key=key_string` 或 `/KeyFile=file_name`。 可使用 `/GenKeyFile=file_name` 提前生成具有随机密钥的文件。 然后，可将其传递到 `/KeyFile=file_name` 或 `/Key=contents_of_file`。

若要首次启用网络访问，用户应关闭模拟器并删除仿真程序的数据目录 *%LOCALAPPDATA%\CosmosDBEmulator*。

## <a name="authenticate-connections-when-using-emulator"></a><a id="authenticate-requests"></a>使用模拟器时对连接进行身份验证

与云中 Azure Cosmos DB 一样，针对 Azure Cosmos 模拟器发出的每个请求都必须进行身份验证。 Azure Cosmos 模拟器仅支持通过 TLS 进行安全通信。 Azure Cosmos 模拟器支持单一固定帐户和众所周知的身份验证密钥进行主密钥身份验证。 此帐户和密钥是允许用于 Azure Cosmos 模拟器的唯一凭据。 它们分别是：

```bash
Account name: localhost:<port>
Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
```

> [!NOTE]
> Azure Cosmos 模拟器支持的主密钥仅适用于模拟器。 不能在 Azure Cosmos DB 模拟器中使用生产 Azure Cosmos DB 帐户和密钥。

> [!NOTE]
> 如果已使用/Key 选项启动模拟器，则使用生成的密钥而不是默认密钥 `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` 。 有关 /Key 选项的详细信息，请参阅[命令行工具参考](emulator-command-line-parameters.md)。

## <a name="connect-to-different-apis-with-the-emulator"></a><a id="connect-with-emulator-apis"></a>用模拟器连接到不同的 Api

### <a name="sql-api"></a>SQL API

在桌面上运行 Azure Cosmos 模拟器后，可使用任何受支持的 [AZURE COSMOS DB SDK](sql-api-sdk-dotnet-standard.md) 或 [Azure Cosmos DB REST API](/rest/api/cosmos-db/) 与模拟器进行交互。 Azure Cosmos 模拟器还包括一个内置数据资源管理器，可让你为 Mongo DB API 创建用于 SQL API 或 Azure Cosmos DB 的容器。 使用数据资源管理器，您可以查看和编辑项，而无需编写任何代码。

```csharp
// Connect to the Azure Cosmos emulator running locally
CosmosClient client = new CosmosClient(
   "https://localhost:8081", 
    "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

```

### <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB 的用于 MongoDB 的 API

在桌面上运行 Azure Cosmos 模拟器后，可以使用 [Azure Cosmos DB 的 API For MongoDB](mongodb-introduction.md) 与模拟器进行交互。 使用 "/EnableMongoDbEndpoint" 作为管理员，从 [命令提示符](emulator-command-line-parameters.md) 启动模拟器。 然后，使用以下连接字符串来连接到 MongoDB API 帐户：

```bash
mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true
```

### <a name="table-api"></a>表 API

在桌面上运行 Azure Cosmos 模拟器后，可以使用 [Azure Cosmos DB 表 API SDK](table-storage-how-to-use-dotnet.md) 与模拟器进行交互。 使用 "/EnableTableEndpoint" 作为管理员，从 [命令提示符](emulator-command-line-parameters.md) 启动模拟器。 接下来，运行下列代码以连接到表 API 帐户：

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Table;
using CloudTable = Microsoft.WindowsAzure.Storage.Table.CloudTable;
using CloudTableClient = Microsoft.WindowsAzure.Storage.Table.CloudTableClient;

string connectionString = "DefaultEndpointsProtocol=http;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=http://localhost:8902/;";

CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("testtable");
table.CreateIfNotExists();
table.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
```

### <a name="cassandra-api"></a>Cassandra API

使用 "/EnableCassandraEndpoint" 从管理员 [命令提示符](emulator-command-line-parameters.md) 启动模拟器。 或者，也可设置环境变量 `AZURE_COSMOS_EMULATOR_CASSANDRA_ENDPOINT=true`。

1. [安装 Python 2.7](https://www.python.org/downloads/release/python-2716/)

1. [安装 Cassandra CLI/CQLSH](https://cassandra.apache.org/download/)

1. 在常规命令提示符窗口中运行以下命令：

   ```bash
   set Path=c:\Python27;%Path%
   cd /d C:\sdk\apache-cassandra-3.11.3\bin
   set SSL_VERSION=TLSv1_2
   set SSL_VALIDATE=false
   cqlsh localhost 10350 -u localhost -p C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw== --ssl
   ```

1. 在 CQLSH shell 中，运行下列命令以连接到 Cassandra 终结点：

   ```bash
   CREATE KEYSPACE MyKeySpace WITH replication = {'class':'MyClass', 'replication_factor': 1};
   DESCRIBE keyspaces;
   USE mykeyspace;
   CREATE table table1(my_id int PRIMARY KEY, my_name text, my_desc text);
   INSERT into table1 (my_id, my_name, my_desc) values( 1, 'name1', 'description 1');
   SELECT * from table1;
   EXIT
   ```

### <a name="gremlin-api"></a>Gremlin API

使用 "/EnableGremlinEndpoint" 从管理员 [命令提示符](emulator-command-line-parameters.md)启动模拟器。 或者，也可设置环境变量 `AZURE_COSMOS_EMULATOR_GREMLIN_ENDPOINT=true`

1. [安装 apache-tinkerpop-gremlin-console-3.3.4](https://archive.apache.org/dist/tinkerpop/3.3.4)。

1. 在模拟器的数据资源管理器中，创建一个数据库 "db1" 和集合 "coll1";对于分区键，请选择 "/name"

1. 在常规命令提示符窗口中运行以下命令：

   ```bash
   cd /d C:\sdk\apache-tinkerpop-gremlin-console-3.3.4-bin\apache-tinkerpop-gremlin-console-3.3.4
  
   copy /y conf\remote.yaml conf\remote-localcompute.yaml
   notepad.exe conf\remote-localcompute.yaml
     hosts: [localhost]
     port: 8901
     username: /dbs/db1/colls/coll1
     password: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
     connectionPool: {
     enableSsl: false}
     serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV1d0,
     config: { serializeResultToString: true  }}

   bin\gremlin.bat
   ```

1. 在 Gremlin shell 中运行以下命令以连接到 Gremlin 终结点：

   ```bash
   :remote connect tinkerpop.server conf/remote-localcompute.yaml
   :remote console
   :> g.V()
   :> g.addV('person1').property(id, '1').property('name', 'somename1')
   :> g.addV('person2').property(id, '2').property('name', 'somename2')
   :> g.V()
   ```

## <a name="uninstall-the-local-emulator"></a><a id="uninstall"></a>卸载本地模拟器

使用以下步骤卸载模拟器：

1. 右键单击系统任务栏上的 " **Azure Cosmos 模拟器** " 图标，然后选择 " **退出**"，退出本地模拟器的所有打开实例。 退出所有实例可能需要一分钟。

1. 在 Windows 搜索框中，键入 " **应用 & 功能** "，然后选择 "应用" " **& 功能 (系统设置 ** " ") 结果"。

1. 在应用列表中，滚动到 **Azure Cosmos DB 模拟器**，选择它，单击 " **卸载**"，然后确认并再次选择 " **卸载** "。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用本地模拟器进行免费的本地开发。 现在，你可以继续学习下一篇文章：

* [导出 Azure Cosmos 模拟器证书以用于 Java、Python 和 Node.js 应用](local-emulator-export-ssl-certificates.md)
* [使用命令行参数和 PowerShell 命令控制模拟器](emulator-command-line-parameters.md)
* [调试模拟器的问题](troubleshoot-local-emulator.md)
