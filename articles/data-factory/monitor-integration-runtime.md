---
title: 在 Azure 数据工厂中监视集成运行时
description: 了解如何在 Azure 数据工厂中监视不同类型的集成运行时。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/11/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 4a0c2813a45fab497173d0101f87b30288e93884
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568894"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>在 Azure 数据工厂中监视集成运行时

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
集成运行时是 Azure 数据工厂 (ADF) 用于在不同的网络环境之间提供多种数据集成功能的计算基础结构。 数据工厂提供三种类型的集成运行时：

- Azure 集成运行时
- 自承载集成运行时
- Azure SQL Server Integration Services (SSIS) 集成运行时

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要获取集成运行时 (IR) 实例的状态，请运行以下 PowerShell 命令： 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

该 cmdlet 返回不同类型的集成运行时的不同信息。 本文解释每种类型的集成运行时的属性和状态。  

## <a name="azure-integration-runtime"></a>Azure 集成运行时

Azure 集成运行时的计算资源在 Azure 中以弹性方式受到完全管理。 下表提供了 **Get-AzDataFactoryV2IntegrationRuntime** 命令返回的属性的说明：

### <a name="properties"></a>属性

下表说明该 cmdlet 针对 Azure 集成运行时返回的属性：

| 属性 | 说明 |
-------- | ------------- | 
| 名称 | Azure 集成运行时的名称。 |  
| 状态 | Azure 集成运行时的状态。 | 
| 位置 | Azure 集成运行时的位置。 有关 Azure 集成运行时位置的详细信息，请参阅[集成运行时简介](concepts-integration-runtime.md)。 |
| DataFactoryName | Azure 集成运行时所属的数据工厂的名称。 | 
| ResourceGroupName | 数据工厂所属的资源组的名称。  |
| 说明 | Azure 集成运行时的说明。  |

### <a name="status"></a>状态

下表提供了 Azure 集成运行时的可能状态：

| 状态 | 注释/方案 | 
| ------ | ------------------ |
| 联机 | Azure 集成运行时处于联机状态，可供使用。 | 
| Offline | 由于内部错误，Azure 集成运行时处于脱机状态。 |

## <a name="self-hosted-integration-runtime"></a>自承载集成运行时

以下部分提供了 Get-AzDataFactoryV2IntegrationRuntime cmdlet 返回的属性的说明。 

> [!NOTE] 
> 返回的属性和状态包含有关整个自承载集成运行时和运行时中每个节点的信息。  

### <a name="properties"></a>属性

下表提供了**每个节点**的监视属性的说明：

| 属性 | 说明 | 
| -------- | ----------- | 
| 名称 | 自承载集成运行时的名称及其关联的节点。 节点是装有自承载集成运行时的本地 Windows 计算机。 |  
| 状态 | 整个自承载集成运行时和每个节点的状态。 示例：联机/脱机/受限等。有关这些状态的信息，请参阅下一个部分。 | 
| 版本 | 自承载集成运行时和每个节点的版本。 自承载集成运行时的版本根据组中多数节点的版本确定。 如果自承载集成运行时设置中包含不同版本的节点，则只有与逻辑自承载集成运行时的版本号相同的节点能正常运行。 其他节点将处于受限模式，需要手动进行更新（仅当自动更新失败时）。 | 
| 可用内存 | 自承载集成运行时节点上的可用内存。 此值为近实时快照。 | 
| CPU 使用率 | 自承载集成运行时节点的 CPU 利用率。 此值为近实时快照。 |
| 网络（进/出） | 自承载集成运行时节点的网络利用率。 此值为近实时快照。 | 
| 并发作业数（运行中/上限） | **正在运行**。 每个节点上运行的作业或任务数。 此值为近实时快照。 <br/><br/>**上限**。 上限表示每个节点的最大并发作业数。 此值根据计算机大小定义而来。 在 CPU、内存或网络未充分利用但活动即将超时的高级方案中，可提高上限来增强并发作业执行。 此功能也适用于单节点自承载集成运行时。 |
| 角色 | 多节点自承载集成运行时中有两种角色 – 调度程序和辅助角色。 所有节点均为辅助角色，表示它们可用于执行作业。 只有一个调度程序节点，用于从云服务中拉取任务/作业，并将其调度到不同的辅助角色节点。 调度程序节点也是一个辅助角色节点。 |

属性的某些设置更适用于自承载集成运行时中包含两个或两个以上节点的情况（即，在横向扩展方案中）。

#### <a name="concurrent-jobs-limit"></a>并发作业限制

并发作业限制的默认值是根据计算机大小设置的。 用于计算此值的因素取决于计算机的 RAM 量和 CPU 内核数。 因此，内核越多，内存越多，并发作业的默认限制就越高。

可以通过增加节点数进行横向扩展。 增加节点数时，并发作业限制为所有可用节点的并发作业限制值的总和。  例如，如果一个节点允许运行最多 12 个并发作业，那么再添加三个类似节点将允许运行最多 48 个并发作业（即 4 x 12）。 建议仅在以下情况下提高并发作业限制：在每个节点上使用默认值时，资源使用率较低。

可以在 Azure 门户中覆盖计算得出的默认值。 选择“创建者”>“连接”>“集成运行时”>“编辑”>“节点”>“修改每个节点的并发作业值”。 也可以使用 PowerShell [update-Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) 命令。
  
### <a name="status-per-node"></a>状态（每个节点）

下表提供了自承载集成运行时节点的可能状态：

| 状态 | 说明 |
| ------ | ------------------ | 
| 联机 | 节点已连接到数据工厂服务。 |
| Offline | 节点处于脱机状态。 |
| 正在升级 | 节点正在进行自动更新。 |
| 受限制 | 由于连接问题而受限。 可能的原因包括 HTTP 端口 8050 问题、服务总线连接问题或凭据同步问题。 |
| 非活动 | 节点的配置与其他多数节点的配置不同。 |

节点在无法与其他节点连接时可能处于非活动状态。

### <a name="status-overall-self-hosted-integration-runtime"></a>状态（整个自承载集成运行时）

下表提供了自承载集成运行时的可能状态： 此状态取决于属于运行时的所有节点的状态。 

| 状态 | 说明 |
| ------ | ----------- | 
| 需要注册 | 尚未将任何节点注册到此自承载集成运行时。 |
| 联机 | 所有节点处于联机状态。 |
| Offline | 没有任何节点处于联机状态。 |
| 受限制 | 此自承载集成运行时中的所有节点并非全都处于正常状态。 此状态是一种警告，表示某些节点可能已关闭。 此状态的可能原因是调度程序/辅助角色节点上出现凭据同步问题。 |

使用 **Get-AzDataFactoryV2IntegrationRuntimeMetric** cmdlet 提取包含详细自承载集成运行时属性，以及执行该 cmdlet 期间这些属性的快照值的 JSON 有效负载。

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

示例输出（假设有两个节点与此自承载集成运行时关联）：

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```

## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS 集成运行时

Azure-SSIS IR 是完全托管的 Azure 虚拟机（VM 或节点）群集，专用于运行 SSIS 包。 你可以使用各种方法对 Azure-SSIS IR 调用 SSIS 包执行：例如通过启用了 Azure 的 SQL Server Data Tools (SSDT)、AzureDTExec 命令行实用工具、SQL Server Management Studio (SSMS)/SQL Server 代理上的 T-SQL，以及 ADF 管道中的执行 SSIS 包活动。 Azure-SSIS IR 不会运行任何其他 ADF 活动。 预配后，可以通过 Azure PowerShell、Azure 门户和 Azure Monitor 监视其总体/特定于节点的属性和状态。

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-powershell"></a>使用 Azure PowerShell 监视 Azure-SSIS 集成运行时

使用以下 Azure PowerShell cmdlet 监视 Azure-SSIS IR 的总体/特定于节点的属性和状态。

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

#### <a name="properties"></a>属性

下表说明上述 cmdlet 针对 Azure-SSIS IR 返回的属性。

| 属性/状态              | 说明                  |
| ---------------------------- | ---------------------------- |
| CreateTime                   | Azure-SSIS IR 的创建时间（UTC 时间）。 |
| Nodes                        | Azure-SSIS IR 的已分配/可用节点，带有特定于节点的状态（正在启动/可用/正在回收/不可用）和可采取措施的错误。 |
| OtherErrors                  | Azure-SSIS IR 上发生的非特定于节点且可采取措施的错误。 |
| LastOperation                | 上次对 Azure-SSIS IR 执行的启动/停止操作的结果；如果失败，将出现可采取措施的错误。 |
| 状态                        | Azure-SSIS IR 的总体状态（初始化/正在启动/已启动/正在停止/已停止）。 |
| 位置                     | Azure-SSIS IR 的位置。 |
| NodeSize                     | Azure-SSIS IR 中每个节点的大小。 |
| NodeCount                    | Azure-SSIS IR 中的节点数目。 |
| MaxParallelExecutionsPerNode | Azure-SSIS IR 中每个节点的最大并行执行数。 |
| CatalogServerEndpoint        | 用于托管 SSIS 目录 (SSISDB) 的现有 Azure SQL Database 服务器或托管实例的终结点。 |
| CatalogAdminUserName         | 现有 Azure SQL Database 服务器或托管实例的管理员用户名。 ADF 使用此信息来代你准备和管理 SSISDB。 |
| CatalogAdminPassword         | 现有 Azure SQL Database 服务器或托管实例的管理员密码。 |
| CatalogPricingTier           | Azure SQL Database 服务器托管的 SSISDB 的定价层。  不适用于 Azure SQL 托管实例承载 SSISDB。 |
| VNetId                       | Azure-SSIS IR 要加入的虚拟网络资源 ID。 |
| 子网                       | Azure-SSIS IR 要加入的子网名称。 |
| ID                           | Azure-SSIS IR 的资源 ID。 |
| 类型                         | Azure-SSIS IR 的 IR 类型（托管/自承载）。 |
| ResourceGroupName            | 在其中创建了 ADF 和 Azure-SSIS IR 的 Azure 资源组的名称。 |
| DataFactoryName              | ADF 的名称。 |
| 名称                         | Azure-SSIS IR 的名称。 |
| 说明                  | Azure-SSIS IR 的说明。 |
  
#### <a name="status-per-azure-ssis-ir-node"></a>状态（每个 Azure-SSIS IR 节点）

下表提供了 Azure-SSIS IR 节点的可能状态：

| 特定于节点的状态 | 说明 |
| -------------------- | ----------- | 
| 正在启动             | 正在准备此节点。 |
| 可用            | 此节点已准备就绪，可在其中部署/执行 SSIS 包。 |
| Recycling            | 正在修复/重启此节点。 |
| 不可用          | 此节点未准备就绪，不能在其中部署/执行 SSIS 包，并出现了可采取措施解决的错误/问题。 |

#### <a name="status-overall-azure-ssis-ir"></a>状态（整个 Azure-SSIS IR）

下表提供了 Azure-SSIS IR 的可能整体状态。 整体状态又取决于属于 Azure-SSIS IR 的所有节点的组合状态。 

| 总体状态 | 说明 | 
| -------------- | ----------- | 
| 初始        | 尚未分配/准备 Azure-SSIS IR 的节点。 | 
| 正在启动       | 正在分配/准备 Azure-SSIS IR 的节点，计费已开始。 |
| Started        | 已分配/准备 Azure-SSIS IR 的节点，并可以在其中部署/执行 SSIS 包。 |
| 正在停止       | 正在释放 Azure-SSIS IR 的节点。 |
| 已停止        | 已释放 Azure-SSIS IR 的节点，计费已停止。 |

### <a name="monitor-the-azure-ssis-integration-runtime-in-azure-portal"></a>在 Azure 门户中监视 Azure-SSIS 集成运行时

若要在 Azure 门户中监视 Azure-SSIS IR，请转到 ADF UI 上“监视”中心的“集成运行时”页，在其中可以看到所有集成运行时 。

![监视所有集成运行时](media/monitor-integration-runtime/monitor-integration-runtimes.png)

接下来，选择 Azure-SSIS IR 的名称以打开其监视页，可以在其中查看其整体/特定于节点的属性和状态。 在本页上，你将看到各种信息/功能磁贴，具体取决于你如何配置 Azure-SSIS IR 的“常规”、“部署”和“高级设置”。  “类型”和“区域”信息磁贴分别显示 Azure-SSIS IR 的类型和区域 。 “节点大小”信息磁贴显示了 SKU（SSIS 版本/VM 层/VM 系列）、CPU 内核数以及 Azure-SSIS IR 每个节点的 RAM 大小。 “正在运行/已请求的节点”信息磁贴比较当前正在运行的节点数与先前为 Azure-SSIS IR 请求的节点总数。 下面将更详细地介绍功能磁贴。

![监视 Azure-SSIS IR](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime.png)

#### <a name="status-tile"></a>“状态”磁贴

在 Azure-SSIS IR 监视页的“状态”磁贴上，可以查看其总体状态，例如“正在运行”或“已停止”  。 选择“正在运行”状态会弹出一个窗口，窗口中有实时“停止”按钮以停止 Azure-SSIS IR 。 选择“已停止”状态会弹出一个窗口，窗口中有实时“启动”按钮以启动 Azure-SSIS IR 。 弹出窗口还有一个“执行 SSIS 包”按钮和一个“资源 ID”文本框，前者用于使用在 Azure-SSIS IR 上运行的“执行 SSIS 包”活动自动生成一个 ADF 管道（请参阅[在 ADF 管道中以“执行 SSIS 包”活动形式运行 SSIS 包](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)），后者用于复制 Azure-SSIS IR 资源 ID (`/subscriptions/YourAzureSubscripton/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourADF/integrationruntimes/YourAzureSSISIR`) 。 包含 ADF 和 Azure-SSIS IR 名称的 Azure-SSIS IR 资源 ID 的后缀构成一个群集 ID，可使用该 ID 从独立软件供应商 (ISV) 购买其他高级/已许可的 SSIS 组件，并将它们绑定到 Azure-SSIS IR（请参阅[在 Azure-SSIS IR 上安装高级/已许可的组件](https://docs.microsoft.com/azure/data-factory/how-to-develop-azure-ssis-ir-licensed-components)）。

![监视 Azure-SSIS IR - 状态磁贴](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-status.png)

#### <a name="ssisdb-server-endpoint-tile"></a>“SSISDB 服务器终结点”磁贴

如果你使用项目部署模型，其中包存储在由 Azure SQL Database 服务器或托管实例托管的 SSISDB 中，则可在 Azure-SSIS IR 监视页上看到“SSISDB 服务终结点”磁贴（请参阅[配置 Azure-SSIS IR 部署设置](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure#deployment-settings-page)）。 在此磁贴上，你可以选择一个指定 Azure SQL Database 服务器或托管实例的链接以弹出一个窗口，你可以在其中从一个文本框中复制服务器终结点，并在从 SSMS 连接时使用它来部署、配置、运行和管理包。 在该弹出窗口中，你还可以选择“查看 Azure SQL 数据库或托管实例设置”链接以在 Azure 门户中重新配置 SSISDB/重设 SSISDB 的大小。

![监视 Azure-SSIS IR - SSISDB 磁贴](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-ssisdb.png)

#### <a name="proxy--staging-tile"></a>“代理/暂存”磁贴

如果下载、安装和配置自承载 IR (SHIR) 作为 Azure-SSIS IR 的代理来访问本地数据，你将在 Azure-SSIS IR 监视页面上看到“代理/暂存”磁贴（请参阅[将 SHIR 配置为 Azure-SSIS IR 的代理](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)）。 在此磁贴上，你可以选择一个指定 SHIR 的链接，以打开其监视页面。 还可以选择另一个指定用于暂存的 Azure Blob 存储的链接，以重新配置其链接服务。

#### <a name="validate-vnet--subnet-tile"></a>“验证 VNET/子网”磁贴

如果你将 Azure-SSIS IR 加入 VNet，可在 Azure-SSIS IR 监视页上看到“验证 VNET/子网”磁贴（请参阅[将 Azure-SSIS IR 加入 VNet](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)）。 在此磁贴上，你可以选择一个指定 VNet 和子网的链接以弹出一个窗口，你可以在其中从文本框中复制 VNet 资源 ID (`/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworks/YourARMVNet`) 和子网名称，还可以验证 VNet 和子网配置，以确保所需的入站/出站网络流量和 Azure-SSIS IR 的管理不受阻碍。

![监视 Azure-SSIS IR - 验证磁贴](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-validate.png)

#### <a name="diagnose-connectivity-tile"></a>“诊断连接”磁贴

在 Azure-SSIS IR 监视页的“诊断连接”磁贴上，可以选择“测试连接”链接以弹出一个窗口，你可以在其中通过完全限定的域名 (FQDN)/IP 地址和指定端口检查 Azure-SSIS IR 和相关的包/配置/数据存储以及管理服务之间的连接（请参阅[测试来自 Azure-SSIS 的连接](https://docs.microsoft.com/azure/data-factory/ssis-integration-runtime-diagnose-connectivity-faq)） 。

![屏幕截图，显示了可在其中测试 Azure-SSIS IR 与相关包/配置/数据存储之间的连接的位置。](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-diagnose.png)

#### <a name="static-public-ip-addresses-tile"></a>“静态公共 IP 地址”磁贴

如果为 Azure-SSIS IR 提供了自己的静态公共 IP 地址，则会在 Azure-SSIS IR 监视页面上看到“静态公共 IP 地址”磁贴（请参阅[为 Azure-SSIS IR 提供自己的静态公共 IP 地址](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network#publicIP)）。 在此磁贴上，你可以选择为 Azure-SSIS IR 指定第一个/第二个静态公共 IP 地址的链接，以弹出一个窗口，并可在窗口的文本框中复制其资源 ID (`/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/publicIPAddresses/YourPublicIPAddress`)。 在弹出窗口中，你还可以选择“查看第一个/第二个静态公共 IP 地址设置”链接，以管理 Azure 门户中的第一个/第二个静态公共 IP 地址。

![屏幕截图显示你可以指定第一个/第二个静态公共 IP 地址的位置。](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-static.png)

#### <a name="package-stores-tile"></a>“包存储”磁贴

如果你使用包部署模型，其中包存储在由 Azure SQL 托管实例托管并通过 Azure-SSIS IR 包存储进行管理的文件系统/Azure 文件/SQL Server 数据库 (MSDB) 中，则可在 Azure-SSIS IR 监视页上看到“包存储”磁贴（请参阅[配置 Azure-SSIS IR 部署设置](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure#deployment-settings-page)）。 在此磁贴上，可以选择一个指定附加到 Azure-SSIS IR 的包存储数的链接以弹出一个窗口，你可以在其中在由 Azure SQL 托管实例托管的文件系统/Azure 文件/MSDB 上为 Azure-SSIS IR 包存储重新配置相关的链接服务。

![监视 Azure-SSIS IR - 包磁贴](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-package.png)

#### <a name="errors-tile"></a>“错误”磁贴

如果 Azure-SSIS IR 的启动/停止/维护/升级存在问题，可在 Azure-SSIS IR 监视页上看到一个附加的“错误”磁贴。 在此磁贴上，你可以选择一个指定由 Azure-SSIS IR 生成的错误数的链接以弹出一个窗口，你可以在其中详细了解这些错误并进行复制，以在我们的疑难解答指南中找到推荐的解决方案（请参阅[对 Azure-SSIS IR 进行故障排除](https://docs.microsoft.com/azure/data-factory/ssis-integration-runtime-management-troubleshoot)）。

![监视 Azure-SSIS IR - 诊断磁贴](media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-error.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-monitor"></a>使用 Azure Monitor 监视 Azure-SSIS 集成运行时

若要使用 Azure Monitor 监视 Azure-SSIS IR，请参阅[使用 Azure Monitor 监视 SSIS 操作](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#monitor-ssis-operations-with-azure-monitor)。

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>有关 Azure-SSIS 集成运行时的详细信息

请参阅以下文章了解有关 Azure-SSIS 集成运行时的详细信息：

- [Azure-SSIS 集成运行时](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 此文提供有关集成运行时（包括 Azure-SSIS IR）的一般概念性信息。 
- [教程：将 SSIS 包部署到 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 本文以分步说明的方式介绍了如何创建 Azure-SSIS IR 并使用 Azure SQL 数据库来承载 SSIS 目录 (SSISDB)。 
- [如何：创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。 本文延伸了教程的内容，提供了关于如何使用 Azure SQL 托管实例来承载 SSISDB 的说明。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)。 本文介绍了如何启动、停止或删除 Azure-SSIS IR。 还介绍了如何通过添加更多节点来横向扩展它。 
- [将 Azure-SSIS IR 加入虚拟网络](join-azure-ssis-integration-runtime-virtual-network.md)。 本文介绍了如何将 Azure-SSIS IR 加入虚拟网络。

## <a name="next-steps"></a>后续步骤
参阅以下文章，了解不同的管道监视方法： 

- [快速入门：创建数据工厂](quickstart-create-data-factory-dot-net.md)。
- [使用 Azure Monitor 监视数据工厂管道](monitor-using-azure-monitor.md)