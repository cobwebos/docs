---
title: 通过 Azure Cosmos DB 模拟器在本地开发 | Microsoft Docs
description: 利用 Azure Cosmos DB 模拟器，可以在本地免费开发和测试应用程序，无需创建 Azure 订阅。
services: cosmos-db
documentationcenter: ''
keywords: Azure Cosmos DB 模拟器
author: David-Noble-at-work
manager: jhubbard
editor: ''
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: danoble
ms.openlocfilehash: e0d23a163f16763dd4764eb7857dec8076f4754c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="use-the-azure-cosmos-db-emulator-for-local-development-and-testing"></a>将 Azure Cosmos DB 模拟器用于本地开发和测试

<table>
<tr>
  <td><strong>二进制文件</strong></td>
  <td>[下载 MSI](https://aka.ms/cosmosdb-emulator)</td>
</tr>
<tr>
  <td><strong>Docker</strong></td>
  <td>[Docker 中心](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/)</td>
</tr>
<tr>
  <td><strong>Docker 源</strong></td>
  <td>[Github](https://github.com/Azure/azure-cosmos-db-emulator-docker)</td>
</tr>
</table>
  
为方便进行开发，Azure Cosmos DB 模拟器提供了一个模拟 Azure Cosmos DB 服务的本地环境。 使用 Azure Cosmos DB 模拟器可在本地开发和测试应用程序，无需创建 Azure 订阅且不会产生任何费用。 如果对应用程序在 Azure Cosmos DB 模拟器中的工作情况感到满意，可以转为在云中使用 Azure Cosmos DB 帐户。

> [!NOTE]
> 目前，模拟器中的数据资源管理器仅完全支持 SQL API 集合和 MongoDB 集合。 不完全支持表、图和 Cassandra 容器。 

本文涵盖以下任务： 

> [!div class="checklist"]
> * 安装模拟器
> * 对请求进行身份验证
> * 在模拟器中使用数据资源管理器
> * 导出 SSL 证书
> * 从命令行调用模拟器
> * 在 Docker for Windows 上运行模拟器
> * 收集跟踪文件
> * 故障排除

建议通过观看以下视频来入门，Kirill Gavrylyuk 在视频中演示了如何开始使用 Azure Cosmos DB 模拟器。 请注意，视频中将模拟器称为 DocumentDB 模拟器，但自视频录制后，该工具本身已重命名为 Azure Cosmos DB 模拟器。 视频中的所有信息对于 Azure Cosmos DB 模拟器仍然准确。 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="how-the-emulator-works"></a>模拟器的工作原理
Azure Cosmos DB 模拟器提供对 Azure Cosmos DB 服务的高保真模拟。 它支持与 Azure Cosmos DB 相同的功能，包括创建和查询 JSON 文档，预配和缩放集合，以及执行存储过程和触发器。 可以使用 Azure Cosmos DB 模拟器开发和测试应用程序，并通过对 Azure Cosmos DB 的连接终结点进行单一配置更改以将其部署到全局范围的 Azure。

虽然已创建实际 Azure Cosmos DB 服务的高保真本地模拟，但 Azure Cosmos DB 模拟器的实现不同于该服务。 例如，Azure Cosmos DB 模拟器使用标准 OS 组件，例如用于保存的本地文件系统和用于连接的 HTTPS 协议堆栈。 这意味着，不可通过 Azure Cosmos DB 模拟器使用某些依赖于 Azure 基础结构的功能，如全局复制、读/写的单位数毫秒延迟，以及可调整的一致性级别。

## <a name="differences-between-the-emulator-and-the-service"></a>模拟器和服务之间的差异 
由于 Azure Cosmos DB 模拟器提供在本地开发人员工作站上运行的模拟环境，因此模拟器与云中的 Azure Cosmos DB 帐户之间在功能上存在一些差异：

* Azure Cosmos DB 模拟器只支持单一固定帐户和公开的主密钥。  在 Azure Cosmos DB 模拟器中无法重新生成密钥。
* Azure Cosmos DB 模拟器不是可缩放的服务，并且不支持大量的集合。
* Azure Cosmos DB 模拟器不模拟不同的 [Azure Cosmos DB 一致性级别](consistency-levels.md)。
* Azure Cosmos DB 模拟器不模拟[多区域复制](distribute-data-globally.md)。
* Azure Cosmos DB 模拟器不支持服务配额替代，而 Azure Cosmos DB 服务支持（例如文档大小限制、增加的已分区集合存储）。
* 由于 Azure Cosmos DB 模拟器副本不一定能反映 Azure Cosmos DB 服务的最新更改，因此请使用 [Azure Cosmos DB Capacity Planner](https://www.documentdb.com/capacityplanner) 准确估计应用程序的生产吞吐量 (RU) 需求。

## <a name="system-requirements"></a>系统要求
Azure Cosmos DB 模拟器具有以下硬件和软件要求：

* 软件要求
  * Windows Server 2012 R2、Windows Server 2016 或 Windows 10
*   最低硬件要求
  * 2 GB RAM
  * 10 GB 可用硬盘空间

## <a name="installation"></a>安装
可以从 [Microsoft 下载中心](https://aka.ms/cosmosdb-emulator)下载并安装 Azure Cosmos DB 模拟器，也可以在用于 Windows 的 Docker 上运行模拟器。 有关在用于 Windows 的 Docker 上使用模拟器的说明，请参阅[在 Docker 上运行](#running-on-docker)。 

> [!NOTE]
> 若要安装、配置和运行 Azure Cosmos DB 模拟器，必须在计算机上具有管理特权。

## <a name="running-on-windows"></a>在 Windows 上运行

若要启动 Azure Cosmos DB 模拟器，请选择“开始”按钮或按 Windows 键。 开始键入“Azure Cosmos DB 模拟器”，并从应用程序列表中选择该模拟器。 

![选择“开始”按钮或按 Windows 键，开始键入“Azure Cosmos DB 模拟器”，并从应用程序列表中选择该模拟器](./media/local-emulator/database-local-emulator-start.png)

运行模拟器时，在 Windows 任务栏通知区域中会显示一个图标。 ![Azure Cosmos DB 本地模拟器任务栏通知](./media/local-emulator/database-local-emulator-taskbar.png)

默认情况下，Azure Cosmos DB 模拟器在本地计算机（“localhost”）上运行，侦听端口 8081。

Azure Cosmos DB 模拟器默认安装到 `C:\Program Files\Azure Cosmos DB Emulator` 目录。 还可以从命令行启动和停止该模拟器。 有关详细信息，请参阅[命令行工具参考](#command-line)。

## <a name="start-data-explorer"></a>启动数据资源管理器

Azure Cosmos DB 模拟器启动时，会在浏览器中自动打开 Azure Cosmos DB 数据资源管理器。 地址显示为 [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html)。 如果关闭数据资源管理器后要重新打开它，可以在浏览器中打开该 URL 或通过 Windows 任务栏图标中的 Azure Cosmos DB 模拟器启动，如下所示。

![Azure Cosmos DB 本地模拟器数据资源管理器启动器](./media/local-emulator/database-local-emulator-data-explorer-launcher.png)

## <a name="checking-for-updates"></a>检查更新
数据资源管理器指示是否有新的更新可供下载。 

> [!NOTE]
> 在某版本的 Azure Cosmos DB 模拟器中创建数据后，不能保证在使用其他版本时可访问。 如果需要长期保存数据，建议将该数据存储在 Azure Cosmos DB 帐户中，而不是存储在 Azure Cosmos DB 模拟器中。 

## <a name="authenticating-requests"></a>对请求进行身份验证
与云中的 Azure Cosmos DB 一样，针对 Azure Cosmos DB 模拟器的每个请求都必须进行身份验证。 Azure Cosmos DB 模拟器支持单一固定帐户和用于主密钥身份验证的公开的身份验证密钥。 此帐户和密钥是允许用于 Azure Cosmos DB 模拟器的唯一凭据。 它们是：

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> Azure Cosmos DB 模拟器支持的主密钥仅可用于模拟器。 不能在 Azure Cosmos DB 模拟器中使用生产 Azure Cosmos DB 帐户和密钥。 

> [!NOTE] 
> 如果使用“/Key”选项启动仿真器，请使用生成的密钥而不是“C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==”

此外，与 Azure Cosmos DB 服务一样，Azure Cosmos DB 模拟器仅支持采用 SSL 的安全通信。

## <a name="running-on-a-local-network"></a>在本地网络上运行

可在本地网络中运行仿真器。 要启用网络访问，请在[命令行](#command-line-syntax)中指定 /AllowNetworkAccess 选项（同时还需指定 /Key=key_string 或 /KeyFile=file_name）。 可使用 /GenKeyFile=file_name 提前生成具有随机密钥的文件。  然后可将其传递至 /KeyFile=file_name 或 /Key=contents_of_file。

首次启用网络访问时，用户应关闭仿真器，并删除仿真器的数据目录 (C:\Users\user_name\AppData\Local\CosmosDBEmulator)。

## <a name="developing-with-the-emulator"></a>通过模拟器进行开发
在桌面上运行 Azure Cosmos DB 模拟器以后，可以使用任何支持的 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) 或 [Azure Cosmos DB REST API](/rest/api/cosmos-db/) 与模拟器进行交互。 Azure Cosmos DB 模拟器还包括内置数据资源管理器，可以利用它在不编写任何代码的情况下，为 SQL API 和 MongoDB API 创建集合以及查看和编辑文档。   

    // Connect to the Azure Cosmos DB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

如果使用 [Azure Cosmos DB 的 MongoDB 协议支持](mongodb-introduction.md)，请使用以下连接字符串：

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true

可以通过现有工具（例如 [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio)）连接到 Azure Cosmos DB 模拟器。 还可以通过 [Azure Cosmos DB 数据迁移工具](https://github.com/azure/azure-documentdb-datamigrationtool)在 Azure Cosmos DB 模拟器和 Azure Cosmos DB 服务之间迁移数据。

> [!NOTE] 
> 如果使用“/Key”选项启动仿真器，请使用生成的密钥而不是“C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==”

默认情况下，使用 Azure Cosmos DB 模拟器，可创建多达 25 个单分区集合或 1 个已分区集合。 有关如何更改此值的详细信息，请参阅[设置 PartitionCount 值](#set-partitioncount)。

## <a name="export-the-ssl-certificate"></a>导出 SSL 证书

.NET 语言和运行时使用 Windows 证书存储来安全地连接到 Azure Cosmos DB 本地模拟器。 其他语言有自己管理和使用证书方法。 Java 使用自己的[证书存储](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)，而 Python 使用[套接字包装器](https://docs.python.org/2/library/ssl.html)。

为了获得用于语言和运行时（未与 Windows 证书存储集成）的证书，需要通过 Windows 证书管理器将其导出。 可通过运行 certlm.msc 进行启动，也可按照[导出 Azure Cosmos DB 模拟器证书](./local-emulator-export-ssl-certificates.md)中的分步说明进行操作。 证书管理器开始运行后，打开个人证书（如下所示），并将友好名称为“DocumentDBEmulatorCertificate”的证书导出为 BASE-64 编码的 X.509 (.cer) 文件。

![Azure Cosmos DB 本地模拟器 SSL 证书](./media/local-emulator/database-local-emulator-ssl_certificate.png)

可按照[将证书添加到 Java CA 证书存储](https://docs.microsoft.com/azure/java-add-certificate-ca-store)中的说明，将 X.509 证书导入 Java 证书存储。 证书导入证书存储后，Java 和 MongoDB 应用程序即能够连接到 Azure Cosmos DB 模拟器。

从 Python 和 Node.js SDK 连接到模拟器时，将禁用 SSL 验证。

## <a id="command-line"></a>命令行工具参考
从安装位置中，可以使用命令行启动和停止模拟器、配置选项和执行其他操作。

### <a name="command-line-syntax"></a>命令行语法

    CosmosDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

若要查看选项列表，请在命令提示符下键入 `CosmosDB.Emulator.exe /?`。

<table>
<tr>
  <td><strong>选项</strong></td>
  <td><strong>说明</strong></td>
  <td><strong>命令</strong></td>
  <td><strong>参数</strong></td>
</tr>
<tr>
  <td>[无参数]</td>
  <td>使用默认设置启动 Azure Cosmos DB 模拟器。</td>
  <td>CosmosDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[帮助]</td>
  <td>显示支持的命令行参数列表。</td>
  <td>CosmosDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>GetStatus</td>
  <td>获取 Azure Cosmos DB 模拟器的状态。 状态由退出代码指示：1 = 正在启动，2 = 正在运行，3 = 已停止。 退出代码为负表示发生了错误。 不生成其他输出。</td>
  <td>CosmosDB.Emulator.exe /GetStatus</td>
  <td></td>
<tr>
  <td>关机</td>
  <td>关闭 Azure Cosmos DB 模拟器。</td>
  <td>CosmosDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>指定要在其中存储数据文件的路径。 默认路径为 %LocalAppdata%\CosmosDBEmulator。</td>
  <td>CosmosDB.Emulator.exe /DataPath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt;：可访问的路径</td>
</tr>
<tr>
  <td>端口</td>
  <td>指定用于模拟器的端口号。  默认值为 8081。</td>
  <td>CosmosDB.Emulator.exe /Port=&lt;port&gt;</td>
  <td>&lt;port&gt;：单个端口号</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>指定用于 MongoDB 兼容性 API 的端口号。 默认为 10255。</td>
  <td>CosmosDB.Emulator.exe /MongoPort=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;：单个端口号</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>指定用于直接连接的端口。 默认值为 10251、10252、10253、10254。</td>
  <td>CosmosDB.Emulator.exe /DirectPorts:&lt;directports&gt;</td>
  <td>&lt;directports&gt;：以逗号分隔的 4 个端口的列表</td>
</tr>
<tr>
  <td>密钥</td>
  <td>模拟器的授权密钥。 密钥必须是 64 字节向量的 base 64 编码。</td>
  <td>CosmosDB.Emulator.exe /Key:&lt;key&gt;</td>
  <td>&lt;key&gt;：密钥必须是 64 字节向量的 base 64 编码</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>指定已启用请求速率限制行为。</td>
  <td>CosmosDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>指定已禁用请求速率限制行为。</td>
  <td>CosmosDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>不显示模拟器用户界面。</td>
  <td>CosmosDB.Emulator.exe /NoUI</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>在启动时不显示数据资源管理器。</td>
  <td>CosmosDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>指定已分区的集合的最大数。 有关详细信息，请参阅[更改集合数](#set-partitioncount)。</td>
  <td>CosmosDB.Emulator.exe /PartitionCount=&lt;partitioncount&gt;</td>
  <td>&lt;partitioncount&gt;：允许的单分区集合的最大数量。 默认值为 25。 允许的最大值为 250。</td>
</tr>
<tr>
  <td>DefaultPartitionCount</td>
  <td>指定分区集合的默认分区数。</td>
  <td>CosmosDB.Emulator.exe /DefaultPartitionCount=&lt;defaultpartitioncount&gt;</td>
  <td>&lt;defaultpartitioncount&gt; 默认值为 25。</td>
</tr>
<tr>
  <td>AllowNetworkAccess</td>
  <td>通过网络启用对仿真器的访问。 要启用网络访问，还必须传递 /Key=&lt;key_string&gt; 或 /KeyFile=&lt;file_name&gt;。</td>
  <td>CosmosDB.Emulator.exe /AllowNetworkAccess /Key=&lt;key_string&gt;<br><br>或<br><br>CosmosDB.Emulator.exe /AllowNetworkAccess /KeyFile=&lt;file_name&gt;</td>
  <td></td>
</tr>
<tr>
  <td>NoFirewall</td>
  <td>在使用 /AllowNetworkAccess 时，请勿调整防火墙规则。</td>
  <td>CosmosDB.Emulator.exe /NoFirewall</td>
  <td></td>
</tr>
<tr>
  <td>GenKeyFile</td>
  <td>生成新的授权密钥并保存至指定文件。 所生成的密钥可与 /Key 或 /KeyFile 选项配合使用。</td>
  <td>CosmosDB.Emulator.exe  /GenKeyFile=&lt;path to key file&gt;</td>
  <td></td>
</tr>
<tr>
  <td>一致性</td>
  <td>为帐户设置默认一致性级别。</td>
  <td>CosmosDB.Emulator.exe /Consistency=&lt;consistency&gt;</td>
  <td>&lt;一致性&gt;：值必须是下列[一致性级别](consistency-levels.md)之一：Session、Strong、Eventual 或 BoundedStaleness。  默认值为“Session”。</td>
</tr>
<tr>
  <td>?</td>
  <td>显示帮助消息。</td>
  <td></td>
  <td></td>
</tr>
</table>

## <a id="set-partitioncount"></a>更改集合数

默认情况下，使用 Azure Cosmos DB 模拟器，可创建多达 25 个单分区集合或 1 个已分区集合。 通过修改 **PartitionCount** 值，可以创建最多 250 个单分区集合或 10 个已分区集合，或两者的任意组合（不得超过 250 个单分区，其中 1 个已分区集合 = 25 个单分区集合）。

如果在已超过当前分区计数后尝试创建集合，则模拟器将引发 ServiceUnavailable 异常，并收到以下消息。

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email askcosmosdb@microsoft.com at any time or
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

若要更改 Azure Cosmos DB 模拟器可用的集合数，请执行以下操作：

1. 通过在系统任务栏上右键单击“Azure Cosmos DB 模拟器”图标，并单击“重置数据...”，删除所有本地 Azure Cosmos DB 模拟器数据。
2. 删除文件夹 C:\Users\user_name\AppData\Local\CosmosDBEmulator 中的所有模拟器数据。
3. 通过在系统任务栏上右键单击“Azure Cosmos DB 模拟器”图标，并单击“退出”，退出所有打开的实例。 退出所有实例可能需要一分钟。
4. 安装最新版的 [Azure Cosmos DB 模拟器](https://aka.ms/cosmosdb-emulator)。
5. 通过设置一个 <= 250 的值启动具有 PartitionCount 标志的模拟器。 例如：`C:\Program Files\Azure CosmosDB Emulator>CosmosDB.Emulator.exe /PartitionCount=100`。

## <a name="controlling-the-emulator"></a>控制模拟器

模拟器附带一个 PowerShell 模块，用于启动、停止、卸载和检索服务的状态。 使用方式：

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

或者将 `PSModules` 目录置于 `PSModulesPath` 并导入，如下所示：

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

下面汇总的命令用于通过 PowerShell 来控制模拟器：

### `Get-CosmosDbEmulatorStatus`

#### <a name="syntax"></a>语法

`Get-CosmosDbEmulatorStatus`

#### <a name="remarks"></a>备注

返回以下 ServiceControllerStatus 值之一：ServiceControllerStatus.StartPending、ServiceControllerStatus.Running 或 ServiceControllerStatus.Stopped。

### `Start-CosmosDbEmulator`

#### <a name="syntax"></a>语法

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>]  [<CommonParameters>]`

#### <a name="remarks"></a>备注

启动模拟器。 默认情况下，此命令会一直等待，直至模拟器做好接受请求的准备。 如果希望 cmdlet 在启动模拟器后立即返回，请使用 -NoWait 选项。

### `Stop-CosmosDbEmulator`

#### <a name="syntax"></a>语法

 `Stop-CosmosDbEmulator [-NoWait]`

#### <a name="remarks"></a>备注

停止模拟器。 默认情况下，此命令会一直等待，直至模拟器完全关闭。 如果希望 cmdlet 在模拟器开始关闭后立即返回，请使用 -NoWait 选项。

### `Uninstall-CosmosDbEmulator`

#### <a name="syntax"></a>语法

`Uninstall-CosmosDbEmulator [-RemoveData]`

#### <a name="remarks"></a>备注

卸载模拟器，并可视需要删除 $env:LOCALAPPDATA\CosmosDbEmulator 的完整内容。
此 cmdlet 可确保在卸载模拟器之前，模拟器已停止。

## <a name="running-on-docker"></a>在 Docker 上运行

可以在 Docker for Windows 上运行 Azure Cosmos DB 模拟器。 该模拟器不适合于用于 Oracle Linux 的 Docker。

安装[用于 Windows 的 Docker](https://www.docker.com/docker-windows) 后，通过右键单击工具栏上的 Docker 图标并选择“切换到 Windows 容器”切换到 Windows 容器。

接下来，通过从常用的 shell 运行以下命令，从 Docker 中心拉取模拟器映像。

```     
docker pull microsoft/azure-cosmosdb-emulator 
```
若要启动映像，请运行以下命令。

通过命令行：
```cmd 
md %LOCALAPPDATA%\CosmosDBEmulatorCert 2>null
docker run -v %LOCALAPPDATA%\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

通过 PowerShell：
```powershell
md $env:LOCALAPPDATA\CosmosDBEmulatorCert 2>null
docker run -v $env:LOCALAPPDATA\CosmosDBEmulatorCert:c:\CosmosDBEmulator\CosmosDBEmulatorCert -P -t -i -m 2GB microsoft/azure-cosmosdb-emulator 
```

响应类似于以下内容：

```
Starting Emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
``` 

现在，在客户端中使用来自响应的终结点和主密钥，并将 SSL 证书导入到主机中。 若要导入 SSL 证书，请从管理员命令提示符执行以下操作：

通过命令行：
```cmd 
cd %LOCALAPPDATA%\CosmosDBEmulatorCert
powershell .\importcert.ps1
```

通过 PowerShell：
```powershell
cd $env:LOCALAPPDATA\CosmosDBEmulatorCert
.\importcert.ps1
```

在模拟器启动之后便关闭交互式 shell 会关闭模拟器的容器。

若要打开数据资源管理器，请在浏览器中导航到以下 URL。 上面所示的响应消息中提供了模拟器终结点。

    https://<emulator endpoint provided in response>/_explorer/index.html


## <a name="troubleshooting"></a>故障排除

使用以下提示来帮助解决使用 Azure Cosmos DB 模拟器时遇到的问题：

- 如果安装了新版本的模拟器并遇到错误，请务必重置数据。 重置数据的方法如下：在系统任务栏上右键单击“Azure Cosmos DB 模拟器”图标，然后单击“重置数据...”。 如果这样做无法修复错误，则可卸载并重新安装该应用。 有关说明，请参阅[卸载本地模拟器](#uninstall)。

- 如果 Azure Cosmos DB 模拟器崩溃，请从 c:\Users\user_name\AppData\Local\CrashDumps 文件夹收集转储文件、进行压缩并将其附加到电子邮件，发送至 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com)。

- 如果 CosmosDB.StartupEntryPoint.exe 崩溃，请从管理员命令提示符运行以下命令：`lodctr /R` 

- 如果遇到连接问题，请[收集跟踪文件](#trace-files)、进行压缩并将其附加到电子邮件，发送至 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com)。

- 如果收到“服务不可用”消息，模拟器可能无法初始化网络堆栈。 请查看是否安装了 Pulse 安全客户端或 Juniper 网络客户端，因为其网络筛选器驱动程序可能会导致该问题。 卸载第三方网络筛选器驱动程序通常可修复此问题。

### <a id="trace-files"></a>收集跟踪文件

若要收集调试跟踪，请从管理命令提示符运行以下命令：

1. `cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"`
2. `CosmosDB.Emulator.exe /shutdown`。 监视系统托盘，确保该程序已关闭，这可能需要几分钟时间。 还可以直接在 Azure Cosmos DB 模拟器用户界面中单击“退出”。
3. `CosmosDB.Emulator.exe /starttraces`
4. `CosmosDB.Emulator.exe`
5. 再现问题。 如果数据资源管理器无法运行，只需等待几秒钟，待浏览器打开以捕获错误。
5. `CosmosDB.Emulator.exe /stoptraces`
6. 导航到 `%ProgramFiles%\Azure Cosmos DB Emulator`，查找 docdbemulator_000001.etl 文件。
7. 将 .etl 文件和重现步骤一起发送至 [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) 进行调试。

### <a id="uninstall"></a>卸载本地模拟器

1. 通过在系统任务栏上右键单击“Azure Cosmos DB 模拟器”图标，然后单击“退出”，退出所有打开的本地模拟器实例。 退出所有实例可能需要一分钟。
2. 在 Windows 搜索框中，键入“应用和功能”，然后单击“应用和功能(系统设置)”结果。
3. 在应用列表中，滚动到“Azure Cosmos DB 模拟器”并将其选中，单击“卸载”，然后确认并再次单击“卸载”。
4. 卸载应用后，导航到 C:\Users\<user>\AppData\Local\CosmosDBEmulator 并删除该文件夹。 

## <a name="change-list"></a>更改列表

可以通过右键单击任务栏上的本地模拟器图标并单击“关于”菜单项来查看版本号。

### <a name="12106-released-on-march-27-2018"></a>2018 年 3 月 27 日发布的 1.21.0.6 版

除了更新与 Cosmos DB 云服务配套的模拟器服务之外，我们还在此版本中包括了一项新功能和两个 bug 修复。

#### <a name="features"></a>功能

1. Start-CosmosDbEmulator 命令现在包括了启动选项。

#### <a name="bug-fixes"></a>Bug 修复

1. Microsoft.Azure.CosmosDB.Emulator PowerShell 模块现在会确保加载 `ServiceControllerStatus` 枚举。

2. Microsoft.Azure.CosmosDB.Emulator PowerShell 模块现在包括了一个清单；第一个版本中遗漏了此清单。

### <a name="1201084-released-on-february-14-2018"></a>1.20.108.4 在 2018 年 2 月 14 日发布

此版本有一项新功能，并有两个 Bug 修复。 感谢帮我们找到并修复这些问题的客户。

#### <a name="bug-fixes"></a>Bug 修复

1. 模拟器现在适用于带 1 个或 2 个核心（或虚拟 CPU）的计算机

   Cosmos DB 分配的任务可执行各种服务。 分配的任务数是主机上核心数的倍数。 默认的倍数适用于核心数很大的生产环境。 但在配置了 1 个或 2 个处理器的计算机上，应用该倍数时，并不会分配用于执行这些服务的任务。

   我们通过向模拟器添加替代配置纠正了此问题。 我们现在应用的倍数为 1。 现在，分配用来执行各项服务的任务数等于主机上的核心数。

   对于此版本来说，最重要的是解决此问题。 我们发现，许多开发/测试环境托管的模拟器有 1 到 2 个核心。

2. 模拟器不再要求安装 Microsoft Visual C++ 2015 Redistributable。

   我们发现，Windows（台式机版本和服务器版本）的全新安装不包括这个可再发行软件包。 因此，我们现在将这个可再发行二进制文件与模拟器捆绑在一起。

#### <a name="features"></a>功能

我们交谈过的许多客户都说：如果模拟器是可编脚本的就好了。 因此，在此版本中，我们添加了一些脚本功能。 模拟器现在包括一个用于自行启动、停止、获取状态和卸载的 PowerShell 模块：`Microsoft.Azure.CosmosDB.Emulator`。 

### <a name="120911-released-on-january-26-2018"></a>2018 年 1 月 26 日发布 1.20.91.1 版

* 默认情况下启用了 MongoDB 聚合管道。

## <a name="next-steps"></a>后续步骤

在本教程中，已完成以下内容：

> [!div class="checklist"]
> * 安装本地模拟器
> * 在 Docker for Windows 上运行模拟器
> * 对请求进行身份验证
> * 在模拟器中使用数据资源管理器
> * 导出 SSL 证书
> * 从命令行调用模拟器
> * 收集跟踪文件

在本教程中，已了解如何使用本地模拟器进行免费的本地开发。 现在可以继续学习下一教程，了解如何导出模拟器 SSL 证书。 

> [!div class="nextstepaction"]
> [导出 Azure Cosmos DB 模拟器证书](local-emulator-export-ssl-certificates.md)
