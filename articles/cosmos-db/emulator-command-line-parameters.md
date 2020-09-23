---
title: Azure Cosmos DB 模拟器的命令行和 PowerShell 参考
description: 了解 Azure Cosmos DB 模拟器的命令行参数，如何用 PowerShell 控制模拟器，以及如何更改可在模拟器中创建的容器数。
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.openlocfilehash: 70e68c566ccb0fe08ce3b8d2dc11fc3c141c16bc
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994839"
---
# <a name="command-line-and-powershell-reference-for-azure-cosmos-db-emulator"></a>Azure Cosmos DB 模拟器的命令行和 PowerShell 参考

Azure Cosmos 模拟器提供了一个模拟 Azure Cosmos DB 服务的本地环境，用于本地开发目的。 [安装仿真程序](local-emulator.md)后，可以通过命令行和 PowerShell 命令控制模拟器。 本文介绍如何使用命令行和 PowerShell 命令来启动和停止模拟器、配置选项和执行其他操作。 必须从安装位置运行命令。

##  <a name="manage-the-emulator-with-command-line-syntax"></a><a id="command-line"></a>用命令行语法管理模拟器

```cmd
Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]
```

若要查看选项列表，请在命令提示符下键入 `Microsoft.Azure.Cosmos.Emulator.exe /?`。

|**选项** | **说明** | **命令**| **参数**|
|---|---|---|---|
|[无参数] | 用默认设置启动 Azure Cosmos 模拟器。 |Microsoft.Azure.Cosmos.Emulator.exe| |
|[帮助] |显示支持的命令行参数列表。|Microsoft.Azure.Cosmos.Emulator.exe /? | |
| GetStatus |获取 Azure Cosmos 模拟器的状态。 状态由退出代码指示：1 = 正在启动，2 = 正在运行，3 = 已停止。 退出代码为负表示发生了错误。 不生成其他输出。 | Microsoft.Azure.Cosmos.Emulator.exe /GetStatus| |
| Shutdown| 关闭 Azure Cosmos 模拟器。| Microsoft.Azure.Cosmos.Emulator.exe /Shutdown | |
|DataPath | 指定要在其中存储数据文件的路径。 默认值为 %LocalAppdata%\CosmosDBEmulator。 | Microsoft.Azure.Cosmos.Emulator.exe /DataPath=\<datapath\> | \<datapath\>：可访问路径 |
|端口 | 指定用于模拟器的端口号。 默认值为 8081。 |Microsoft.Azure.Cosmos.Emulator.exe /Port=\<port\> | \<port\>：单个端口号 |
| ComputePort | 指定用于计算互操作网关服务的端口号。 该网关的 HTTP 终结点探测端口计算得出 ComputePort + 79。 因此，ComputePort 和 ComputePort + 79 必须打开且可使用。 默认值为 8900。 | Microsoft.Azure.Cosmos.Emulator.exe /ComputePort=\<computeport\> | \<computeport\>：单个端口号 |
| EnableMongoDbEndpoint=3.2 | 启用 MongoDB API 3.2 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.2 | |
| EnableMongoDbEndpoint=3.6 | 启用 MongoDB API 3.6 | Microsoft.Azure.Cosmos.Emulator.exe /EnableMongoDbEndpoint=3.6 | |
| MongoPort | 指定用于 MongoDB 兼容性 API 的端口号。 默认值为 10255。 |Microsoft.Azure.Cosmos.Emulator.exe /MongoPort=\<mongoport\>|\<mongoport\>：单个端口号|
| EnableCassandraEndpoint | 启用 Cassandra API | Microsoft.Azure.Cosmos.Emulator.exe /EnableCassandraEndpoint | |
| CassandraPort | 指定用于 Cassandra 终结点的端口号。 默认值为 10350。 | Microsoft.Azure.Cosmos.Emulator.exe /CassandraPort=\<cassandraport\> | \<cassandraport\>：单个端口号 |
| EnableGremlinEndpoint | 启用 Gremlin API | Microsoft.Azure.Cosmos.Emulator.exe /EnableGremlinEndpoint | |
| GremlinPort | 用于 Gremlin 终结点的端口号。 默认值为 8901。 | Microsoft.Azure.Cosmos.Emulator.exe /GremlinPort=\<port\> | \<port\>：单个端口号 |
|EnableTableEndpoint | 启用 Azure 表 API | Microsoft.Azure.Cosmos.Emulator.exe /EnableTableEndpoint | |
|TablePort | 用于 Azure 表终结点的端口号。 默认值为 8902。 | Microsoft.Azure.Cosmos.Emulator.exe /TablePort=\<port\> | \<port\>：单个端口号|
| KeyFile | 从指定文件中读取授权密钥。 使用 /GenKeyFile 选项来生成密钥文件 | Microsoft.Azure.Cosmos.Emulator.exe /KeyFile=\<file_name\> | \<file_name\>：文件的路径 |
| ResetDataPath | 以递归方式删除指定路径中的所有文件。 如果不指定路径，则默认为 %LOCALAPPDATA%\CosmosDbEmulator | Microsoft.Azure.Cosmos.Emulator.exe /ResetDataPath=\<path> | \<path\>：文件路径  |
| StartTraces  |  开始使用 LOGMAN 收集调试跟踪日志。 | Microsoft.Azure.Cosmos.Emulator.exe /StartTraces | |
| StopTraces     | 停止使用 LOGMAN 收集调试跟踪日志。 | Microsoft.Azure.Cosmos.Emulator.exe /StopTraces  | |
| StartWprTraces  |  开始使用 Windows 性能记录工具收集调试跟踪日志。 | Microsoft.Azure.Cosmos.Emulator.exe /StartWprTraces | |
| StopWprTraces     | 停止使用 Windows 性能记录工具收集调试跟踪日志。 | Microsoft.Azure.Cosmos.Emulator.exe /StopWprTraces  | |
|FailOnSslCertificateNameMismatch | 默认情况下，如果证书的 SAN 不包含模拟器主机的域名、本地 IPv4 地址、"localhost" 和 "127.0.0.1"，则模拟器会重新生成其自签名的 TLS/SSL 证书。 启用此选项后，模拟器在启动时会失败。 然后，你应使用 /GenCert 选项来创建并安装新的自签名 TLS/SSL 证书。 | Microsoft.Azure.Cosmos.Emulator.exe /FailOnSslCertificateNameMismatch  | |
| GenCert | 生成并安装新的自签名 TLS/SSL 证书。 （可选）包括以逗号分隔的列表，其中包含用于通过网络访问模拟器的其他 DNS 名称。 | Microsoft.Azure.Cosmos.Emulator.exe /GenCert=\<dns-names\> |\<dns-names\>：其他 dns 名称的逗号分隔列表（可选）  |
| DirectPorts |指定用于直接连接的端口。 默认值为 10251、10252、10253、10254。 | Microsoft.Azure.Cosmos.Emulator.exe /DirectPorts:\<directports\> | \<directports\>：以逗号分隔的 4 个端口的列表 |
| 密钥 |模拟器的授权密钥。 密钥必须是 64 字节向量的 base 64 编码。 | Microsoft.Azure.Cosmos.Emulator.exe /Key:\<key\> | \<key\>：密钥必须是 64 字节向量的 base 64 编码|
| EnableRateLimiting | 指定已启用请求速率限制行为。 |Microsoft.Azure.Cosmos.Emulator.exe /EnableRateLimiting | |
| DisableRateLimiting |指定已禁用请求速率限制行为。 |Microsoft.Azure.Cosmos.Emulator.exe /DisableRateLimiting | |
| NoUI | 不显示模拟器用户界面。 | Microsoft.Azure.Cosmos.Emulator.exe /NoUI | |
| NoExplorer | 在启动时不显示数据资源管理器。 |Microsoft.Azure.Cosmos.Emulator.exe /NoExplorer | | 
| PartitionCount | 指定已分区的容器的最大数。 有关详细信息，请参阅[更改容器数量](#set-partitioncount)。 | Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=\<partitioncount\> | \<partitioncount\>：允许的单分区容器的最大数量。 默认值为 25。 允许的最大值为 250。|
| DefaultPartitionCount| 指定分区容器的默认分区数。 | Microsoft.Azure.Cosmos.Emulator.exe /DefaultPartitionCount=\<defaultpartitioncount\> | \<defaultpartitioncount\> 默认值为 25。|
| AllowNetworkAccess | 通过网络启用对仿真器的访问。 要启用网络访问，还必须传递 /Key=\<key_string\> 或 /KeyFile=\<file_name\>。 | Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /Key=\<key_string\> 或 Microsoft.Azure.Cosmos.Emulator.exe /AllowNetworkAccess /KeyFile=\<file_name\>| |
| NoFirewall | 使用 /AllowNetworkAccess 选项时，不要调整防火墙规则。 |Microsoft.Azure.Cosmos.Emulator.exe /NoFirewall | |
| GenKeyFile | 生成新的授权密钥并保存至指定文件。 所生成的密钥可与 /Key 或 /KeyFile 选项配合使用。 | Microsoft.Azure.Cosmos.Emulator.exe /GenKeyFile=\<path to key file\> | |
| 一致性 | 为帐户设置默认一致性级别。 | Microsoft.Azure.Cosmos.Emulator.exe /Consistency=\<consistency\> | \<consistency\>：值必须是以下[一致性级别](consistency-levels.md)之一：Session、Strong、Eventual 或 BoundedStaleness。 默认值为“Session”。 |
| ? | 显示帮助消息。| | |

## <a name="manage-the-emulator-with-powershell"></a>通过 PowerShell 管理模拟器

该模拟器附带一个 PowerShell 模块，它可用于启动、停止、卸载和检索服务的状态。 运行下列 cmdlet 以使用 PowerShell 模块：

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

或者，将 `PSModules` 目录放置在 `PSModulesPath` 上，并按如下命令中所示将其导入：

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

下面汇总的命令用于通过 PowerShell 来控制模拟器：

### `Get-CosmosDbEmulatorStatus`

**语法**

`Get-CosmosDbEmulatorStatus`

**备注**

返回以下 ServiceControllerStatus 值之一：ServiceControllerStatus.StartPending、ServiceControllerStatus.Running 或 ServiceControllerStatus.Stopped。

### `Start-CosmosDbEmulator`

**语法**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**备注**

启动模拟器。 默认情况下，此命令会一直等待，直至模拟器做好接受请求的准备。 如果希望 cmdlet 在启动模拟器后立即返回，请使用 -NoWait 选项。

### `Stop-CosmosDbEmulator`

**语法**

 `Stop-CosmosDbEmulator [-NoWait]`

**备注**

停止模拟器。 默认情况下，此命令会一直等待，直至模拟器完全关闭。 如果希望 cmdlet 在模拟器开始关闭后立即返回，请使用 -NoWait 选项。

### `Uninstall-CosmosDbEmulator`

**语法**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**备注**

卸载模拟器，并可视需要删除 $env:LOCALAPPDATA\CosmosDbEmulator 的完整内容。
此 cmdlet 可确保在卸载模拟器之前，模拟器已停止。

## <a name="change-the-number-of-default-containers"></a><a id="set-partitioncount"></a>更改默认容器的数目

默认情况下，可最多创建 25 个固定大小的容器（仅支持使用 Azure Cosmos DB SDK 创建），或使用 Azure Cosmos 模拟器创建 5 个不受限容器。 通过修改 PartitionCount 值，可最多创建 250 个固定大小的容器或 50 个不受限容器，也可创建两者的任意组合（前提是总数不超过 250 个固定大小的容器，其中 1 个不受限容器 = 5 个固定大小的容器）。 但是，建议不要设置用 200 个以上固定大小的容器进行运行的模拟器。 因为这会造成磁盘 IO 操作的开销增加，导致在运行终结点 API 时出现不可预测的超时情况。

如果在已超过当前分区计数后尝试创建容器，则模拟器将引发 ServiceUnavailable 异常，并收到以下消息。

> 很抱歉，我们当前在此区域中遇到了高需求，此时无法完成你的请求。 我们在持续努力推出越来越多的容量，请进行重试。
> ActivityId：12345678-1234-1234-1234-123456789abc

要更改 Azure Cosmos 模拟器中可用容器的数量，请执行以下步骤：

1. 右键单击系统托盘上的 " **Azure Cosmos DB 模拟器** " 图标，然后单击 " **重置数据 ...**"，删除所有本地 Azure Cosmos 模拟器数据。

1. 删除文件夹 `%LOCALAPPDATA%\CosmosDBEmulator` 中的所有模拟器数据。

1. 通过在系统任务栏上右键单击“Azure Cosmos DB 模拟器”图标，并单击“退出”，退出所有打开的实例。 退出所有实例可能需要一分钟。

1. 安装最新版的 [Azure Cosmos 模拟器](https://aka.ms/cosmosdb-emulator)。

1. 通过设置一个 <= 250 的值启动具有 PartitionCount 标志的模拟器。 例如：`C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`。
 
## <a name="next-steps"></a>后续步骤

* [导出 Azure Cosmos 模拟器证书以用于 Java、Python 和 Node.js 应用](local-emulator-export-ssl-certificates.md)
* [调试模拟器的问题](troubleshoot-local-emulator.md)
