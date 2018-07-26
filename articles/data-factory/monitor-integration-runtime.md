---
title: 在 Azure 数据工厂中监视集成运行时 | Microsoft Docs
description: 了解如何在 Azure 数据工厂中监视不同类型的集成运行时。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: douglasl
ms.openlocfilehash: 4da9696761747874395ec90cb3b446e3621650ba
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113251"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>在 Azure 数据工厂中监视集成运行时  
**集成运行时**是 Azure 数据工厂用于在不同的网络环境之间提供多种数据集成功能的计算基础结构。 数据工厂提供三种类型的集成运行时：

- Azure 集成运行时
- 自承载集成运行时
- Azure-SSIS 集成运行时

若要获取集成运行时 (IR) 实例的状态，请运行以下 PowerShell 命令： 

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

该 cmdlet 返回不同类型的集成运行时的不同信息。 本文解释每种类型的集成运行时的属性和状态。  

## <a name="azure-integration-runtime"></a>Azure 集成运行时
Azure 集成运行时的计算资源在 Azure 中以弹性方式受到完全管理。 下表提供了 **Get-AzureRmDataFactoryV2IntegrationRuntime** 命令返回的属性的说明：

### <a name="properties"></a>属性
下表说明该 cmdlet 针对 Azure 集成运行时返回的属性：

| 属性 | Description |
-------- | ------------- | 
| 名称 | Azure 集成运行时的名称。 |  
| State | Azure 集成运行时的状态。 | 
| Location | Azure 集成运行时的位置。 有关 Azure 集成运行时位置的详细信息，请参阅[集成运行时简介](concepts-integration-runtime.md)。 |
| DataFactoryName | Azure 集成运行时所属的数据工厂的名称。 | 
| ResourceGroupName | 数据工厂所属的资源组的名称。  |
| Description | Azure 集成运行时的说明。  |

### <a name="status"></a>状态
下表提供了 Azure 集成运行时的可能状态：

| 状态 | 注释/方案 | 
| ------ | ------------------ |
| 联机 | Azure 集成运行时处于联机状态，可供使用。 | 
| 脱机 | 由于内部错误，Azure 集成运行时处于脱机状态。 |

## <a name="self-hosted-integration-runtime"></a>自承载集成运行时
以下部分提供了 Get-AzureRmDataFactoryV2IntegrationRuntime cmdlet 返回的属性的说明。 

> [!NOTE] 
> 返回的属性和状态包含有关整个自承载集成运行时和运行时中每个节点的信息。  

### <a name="properties"></a>属性

下表提供了**每个节点**的监视属性的说明：

| 属性 | Description | 
| -------- | ----------- | 
| 名称 | 自承载集成运行时的名称及其关联的节点。 节点是装有自承载集成运行时的本地 Windows 计算机。 |  
| 状态 | 整个自承载集成运行时和每个节点的状态。 示例：联机/脱机/受限等。有关这些状态的信息，请参阅下一个部分。 | 
| 版本 | 自承载集成运行时和每个节点的版本。 自承载集成运行时的版本根据组中多数节点的版本确定。 如果自承载集成运行时设置中包含不同版本的节点，则只有与逻辑自承载集成运行时的版本号相同的节点能正常运行。 其他节点将处于受限模式，需要手动进行更新（仅当自动更新失败时）。 | 
| 可用内存 | 自承载集成运行时节点上的可用内存。 此值为近实时快照。 | 
| CPU 使用率 | 自承载集成运行时节点的 CPU 利用率。 此值为近实时快照。 |
| 网络（进/出） | 自承载集成运行时节点的网络利用率。 此值为近实时快照。 | 
| 并发作业数（运行中/上限） | 每个节点上运行的作业或任务数。 此值为近实时快照。 上限表示每个节点的最大并发作业数。 此值根据计算机大小定义而来。 在 CPU/内存/网络未充分利用，但活动即将超时的高级方案中，可提高上限来增强并发作业执行。此功能也适用于单节点自承载集成运行时。 |
| 角色 | 多节点自承载集成运行时中有两种角色 – 调度程序和辅助角色。 所有节点均为辅助角色，表示它们可用于执行作业。 只有一个调度程序节点，用于从云服务中拉取任务/作业，并将其调度到不同的辅助角色节点。 调度程序节点也是一个辅助角色节点。 |

属性的某些设置更适用于自承载集成运行时中包含两个或两个以上节点的情况（扩展方案）。 
  
### <a name="status-per-node"></a>状态（每个节点）
下表提供了自承载集成运行时节点的可能状态：

| 状态 | Description |
| ------ | ------------------ | 
| 联机 | 节点已连接到数据工厂服务。 |
| 脱机 | 节点处于脱机状态。 |
| 正在升级 | 节点正在进行自动更新。 |
| 受限制 | 由于连接问题而受限。 可能的原因包括 HTTP 端口 8050 问题、服务总线连接问题或凭据同步问题。 |
| 非活动 | 节点的配置与其他多数节点的配置不同。 |

节点在无法与其他节点连接时可能处于非活动状态。

### <a name="status-overall-self-hosted-integration-runtime"></a>状态（整个自承载集成运行时）
下表提供了自承载集成运行时的可能状态： 此状态取决于属于运行时的所有节点的状态。 

| 状态 | Description |
| ------ | ----------- | 
| 需要注册 | 尚未将任何节点注册到此自承载集成运行时。 |
| 联机 | 所有节点处于联机状态。 |
| 脱机 | 没有任何节点处于联机状态。 |
| 受限制 | 此自承载集成运行时中的所有节点并非全都处于正常状态。 此状态是一种警告，表示某些节点可能已关闭。 此状态的可能原因是调度程序/辅助角色节点上出现凭据同步问题。 |

使用 **Get-AzureRmDataFactoryV2IntegrationRuntimeMetric** cmdlet 提取包含详细自承载集成运行时属性，以及执行该 cmdlet 期间这些属性的快照值的 JSON 有效负载。

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
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
Azure-SSIS 集成运行时是完全托管的 Azure 虚拟机（或节点）群集，专用于运行 SSIS 包。 它不运行 Azure 数据工厂的其他任何活动。 预配后，可以查询其属性，并监视其总体/特定于节点的状态。

### <a name="properties"></a>属性

| 属性/状态 | Description |
| --------------- | ----------- |
| CreateTime | Azure-SSIS 集成运行时的创建时间（UTC 时间）。 |
| Nodes | Azure-SSIS 集成运行时的已分配/可用节点、特定于节点的状态 (starting/available/recycling/unavailable) 和可采取措施的错误。 |
| OtherErrors | Azure-SSIS 集成运行时中发生的非特定于节点且可采取措施的错误。 |
| LastOperation | 上次对 Azure-SSIS 集成运行时执行的启动/停止操作的结果，以及可采取措施的错误（如果操作失败）。 |
| 省/直辖市/自治区 | Azure-SSIS 集成运行时的总体状态 (initial/starting/started/stopping/stopped)。 |
| 位置 | Azure-SSIS 集成运行时的位置。 |
| NodeSize | Azure-SSIS 集成运行时的每个节点的大小。 |
| NodeCount | Azure-SSIS 集成运行时中的节点数目。 |
| MaxParallelExecutionsPerNode | Azure-SSIS 集成运行时中每个节点的并行执行数。 |
| CatalogServerEndpoint | 用于承载 SSISDB 的现有 Azure SQL 数据库/托管实例（预览版）服务器的终结点。 |
| CatalogAdminUserName | 现有 Azure SQL 数据库/托管实例（预览版）服务器的管理员用户名。 数据工厂服务使用此信息来代你准备和管理 SSISDB。 |
| CatalogAdminPassword | 现有 Azure SQL 数据库/托管实例（预览版）服务器的管理员密码。 |
| CatalogPricingTier | 现有 Azure SQL 数据库服务器托管的 SSISDB 的定价层。  不适用于 Azure SQL 托管实例（预览版）承载 SSISDB。 |
| VNetId | Azure-SSIS 集成运行时要加入的虚拟网络资源 ID。 |
| 子网 | Azure-SSIS 集成运行时要加入的子网名称。 |
| ID | Azure-SSIS 集成运行时的资源 ID。 |
| Type | Azure-SSIS 集成运行时的类型 (Managed/Self-Hosted)。 |
| ResourceGroupName | 在其中创建了数据工厂和 Azure-SSIS 集成运行时的 Azure 资源组的名称。 |
| DataFactoryName | Azure 数据工厂的名称。 |
| 名称 | Azure-SSIS 集成运行时的名称。 |
| Description | Azure-SSIS 集成运行时的说明。 |

  
### <a name="status-per-node"></a>状态（每个节点）

| 状态 | Description |
| ------ | ----------- | 
| 正在启动 | 正在准备此节点。 |
| 可用 | 此节点已准备就绪，可在其中部署/执行 SSIS 包。 |
| Recycling | 正在修复/重启此节点。 |
| 不可用 | 此节点未准备就绪，不能在其中部署/执行 SSIS 包，并出现了可采取措施解决的错误/问题。 |

### <a name="status-overall-azure-ssis-integration-runtime"></a>状态（整个 Azure-SSIS 集成运行时）

| 总体状态 | Description | 
| -------------- | ----------- | 
| Initial | 尚未分配/准备 Azure-SSIS 集成运行时的节点。 | 
| 正在启动 | 正在分配/准备 Azure-SSIS 集成运行时的节点，计费已开始。 |
| 已启动 | 已分配/准备 Azure-SSIS 集成运行时的节点，并可以在其中部署/执行 SSIS 包。 |
| 正在停止  | 正在释放 Azure-SSIS 集成运行时的节点。 |
| 已停止 | 已释放 Azure-SSIS 集成运行时的节点，计费已停止。 |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>在 Azure 门户中监视 Azure-SSIS 集成运行时

以下屏幕截图显示了如何选择要监视的 Azure-SSIS IR，并提供显示的信息的示例。

![选择要监视的 Azure-SSIS 集成运行时](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![查看有关 Azure-SSIS 集成运行时的信息](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>使用 PowerShell 监视 Azure-SSIS 集成运行时

使用类似以下示例的脚本来检查 Azure-SSIS IR 的状态。

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>有关 Azure-SSIS 集成运行时的详细信息

请参阅以下文章了解有关 Azure-SSIS 集成运行时的详细信息：

- [Azure-SSIS 集成运行时](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 此文提供有关集成运行时（包括 Azure-SSIS IR）的一般概念性信息。 
- [教程：将 SSIS 包部署到 Azure](tutorial-create-azure-ssis-runtime-portal.md)。 此文提供有关创建 Azure-SSIS IR，并使用 Azure SQL 数据库来承载 SSIS 目录的分步说明。 
- [如何创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。 此文延伸了本教程的内容，提供了有关使用 Azure SQL 托管实例（预览版）以及将 IR 加入虚拟网络的说明。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)。 此文介绍如何停止、启动或删除 Azure-SSIS IR。 此外，介绍如何通过在 Azure-SSIS IR 中添加更多节点来扩展 IR。 
- [将 Azure-SSIS IR 加入虚拟网络](join-azure-ssis-integration-runtime-virtual-network.md)。 此文提供有关将 Azure-SSIS IR 加入 Azure 虚拟网络的概念性信息。 此外，还介绍可以执行哪些步骤来使用 Azure 门户配置虚拟网络，以便 Azure-SSIS IR 能够加入虚拟网络。 

## <a name="next-steps"></a>后续步骤
参阅以下文章，了解不同的管道监视方法： 

- [快速入门：创建数据工厂](quickstart-create-data-factory-dot-net.md)。
- [使用 Azure Monitor 监视数据工厂管道](monitor-using-azure-monitor.md)
