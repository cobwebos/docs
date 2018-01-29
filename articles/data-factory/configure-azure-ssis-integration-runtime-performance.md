---
title: "配置高性能 Azure-SSIS Integration Runtime | Microsoft Docs"
description: "了解如何配置高性能 Azure-SSIS Integration Runtime 的属性"
services: data-factory
ms.date: 01/10/2018
ms.topic: article
ms.service: data-factory
ms.workload: data-services
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.openlocfilehash: 7d0e75ad85731b10f9a993c2fa62f30c0142ed05
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>配置高性能 Azure-SSIS Integration Runtime

本文介绍如何配置高性能 Azure-SSIS Integration Runtime (IR)。 通过 Azure-SSIS IR 可以在 Azure 中部署和运行 SQL Server Integration Services (SSIS) 包。 有关 Azure-SSIS IR 的详细信息，请参阅 [Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) 一文。 有关在 Azure 上部署和运行 SSIS 包的详细信息，请参阅[将 SQL Server Integration Services 工作负荷直接迁移到云](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)。

> [!IMPORTANT]
> 本文包含 SSIS 开发团队成员进行的内部测试所得出的性能结果和观察数据。 结果可能不同。 在完成配置设置（会影响成本和性能）前进行自我测试。

## <a name="properties-to-configure"></a>要配置的属性

配置脚本的以下部分显示创建 Azure-SSIS Integration Runtime 时可以配置的属性。 有关完整的 PowerShell 脚本和说明，请参阅[将 SQL Server Integration Services 包部署到 Azure](tutorial-deploy-ssis-packages-azure.md)。

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# In public preview, only EastUS, NorthEurope, and WestEurope are supported.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>"
```

## <a name="azuressislocation"></a>AzureSSISLocation
AzureSSISLocation 是 Integration Runtime 辅助角色节点的位置。 该辅助角色节点始终连接到 Azure SQL 数据库上的 SSIS 目录数据库 (SSISDB)。 将 AzureSSISLocation 位置设置为与托管 SSISDB 的 SQL 数据库服务器相同，确保 Integration Runtime 尽可能有效运行。

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Azure 数据工厂 V2 的公共预览版（包括 Azure-SSIS IR）支持以下选项：
-   Standard\_A4\_v2
-   Standard\_A8\_v2
-   Standard\_D1\_v2
-   Standard\_D2\_v2
-   Standard\_D3\_v2
-   Standard\_D4\_v2

在 SSIS 工程团队进行的非官方内部测试中，D 系列表现得比 A 系列更加适合 SSIS 包执行。

-   D 系列的性价比高于 A 系列。
-   相同价位的 D 系列的吞吐量高于 A 系列。

### <a name="configure-for-execution-speed"></a>配置执行速度
如果没有很多要运行的包并且需要包快速运行，则使用下表中的信息选择一个适合方案的虚拟机类型。

此数据代表单个辅助角色节点上的单个包执行。 此包从 Azure Blob 存储中加载 1 千万个包含名和姓列的记录，生成全名列并且将全名超过 20 个字符的记录写到 Azure Blob 存储。

![SSIS Integration Runtime 包执行速度](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speed.png)

### <a name="configure-for-overall-throughput"></a>配置整体吞吐量

如果有大量包要运行且最关注整体吞吐量，则使用下表中的信息选择适合方案的虚拟机类型。

![SSIS Integration Runtime 最大整体吞吐量](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughput.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

AzureSSISNodeNumber 调整 Integration Runtime 的可伸缩性。 Integration Runtime 的吞吐量与 AzureSSISNodeNumber 成正比。 首先将 AzureSSISNodeNumber 设为一个较小值，监视 Integration Runtime 的吞吐量，然后针对方案调整值。 若要重新配置辅助角色节点数，请参阅[管理 Azure-SSIS Integration Runtime](manage-azure-ssis-integration-runtime.md)。

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

已经在使用强大的辅助角色节点运行包时，增加 AzureSSISMaxParallelExecutionsPerNode 可能增加 Integration Runtime 的整体吞吐量。 对于 Standard_D1_v2 节点，支持每个节点 1-4 个并行执行。 对于所有其他类型的节点，支持每个节点 1-8 个并行执行。
可以基于包的成本和辅助角色节点的以下配置估计合适的值。 有关详细信息，请参阅[常规用途虚拟机大小](../virtual-machines/windows/sizes-general.md)。

| 大小             | vCPU | 内存：GiB | 临时存储 (SSD) GiB | 临时存储的最大吞吐量：IOPS/读取 MBps/写入 MBps | 最大的数据磁盘/吞吐量：IOPS | 最大 NIC 数/预期网络性能 (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3.5         | 50                     | 3000/46/23                                             | 2/2x500                         | 2 / 750                                        |
| Standard\_D2\_v2 | #N/A    | 7           | 100                    | 6000/93/46                                             | 4/4x500                         | 2 / 1500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000/187/93                                           | 8/8x500                         | 4 / 3000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000/375/187                                          | 16/16x500                       | 8 / 6000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000/80/40                                             | 8/8x500                         | 4 / 1000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000/160/80                                            | 16/16x500                       | 8 / 2000                                       |

以下是为 AzureSSISMaxParallelExecutionsPerNode 属性设置正确值的指南： 

1. 首先将它设置为一个较小值。
2. 少量增加该值，以检查是否提升整体吞吐量。
3. 在整体吞吐量达到最大值时停止增加值。

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

SSISDBPricingTier 是Azure SQL 数据库上 SSIS 目录数据库 (SSISDB) 的定价层。 此设置影响 IR 实例中的最大辅助角色节点数、包执行排队速度以及执行日志加载速度。

-   如果不关注包执行排队和执行日志加载速度，可以选择最低的数据库定价层。 使用基本定价的 Azure SQL 数据库在 Integration Runtime 实例中支持 8 个辅助角色节点。

-   如果辅助角色节点计数大于 8 或者核心计数大于 50，则选择一个比基本版更强大的数据库。 否则，数据库会成为 Integration Runtime 实例的瓶颈，且整体性能会受到负面影响。

还可以基于 Azure 门户上提供的[数据库事务单元](../sql-database/sql-database-what-is-a-dtu.md) (DTU) 使用情况信息调整数据库定价层。

## <a name="design-for-high-performance"></a>高性能设计
设计要在 Azure 上运行的 SSIS 包不同于设计本地执行的包。 不是将多个独立任务组合在同一包中，而是将它们分离到多个包中，从而提升在 Azure-SSIS IR 中执行的效率。 为每个包创建包执行，使它们不用等待彼此完成。 此方法受益于 Azure-SSIS Integration Runtime 的可伸缩性并提升整体吞吐量。

## <a name="next-steps"></a>后续步骤
详细了解 Azure-SSIS Integration Runtime。 请参阅 [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime)。