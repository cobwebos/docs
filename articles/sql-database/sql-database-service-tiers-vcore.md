---
title: vCore 模型概述
description: VCore 购买模式使你可以独立缩放计算和存储资源、匹配本地性能和优化价格。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: d57f1e87c503a86a522fdb3004b021fbcb5c6ff1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351410"
---
# <a name="vcore-model-overview"></a>vCore 模型概述

虚拟核心（vCore）模型具有以下几个优点：

- 更高的计算、内存、IO 和存储限制。
- 控制硬件的生成，以便更好地匹配工作负载的计算和内存要求。
- [Azure 混合权益（AHB）](sql-database-azure-hybrid-benefit.md)和[预订实例（RI）](sql-database-reserved-capacity.md)的定价折扣。
- 提高计算能力的硬件细节的透明度;有助于规划从本地部署迁移。

## <a name="service-tiers"></a>服务层

VCore 模型中的服务层选项包括常规用途、业务关键和超大规模。 服务层通常定义与可用性和灾难恢复相关的存储体系结构、空间和 IO 限制以及业务连续性选项。

||**常规用途**|**业务关键**|**超大规模**|
|---|---|---|---|
|最适用情况|大多数业务工作负荷。 提供面向预算、平衡和可缩放的计算和存储选项。 |通过使用多个独立副本，为业务应用程序提供故障的最高复原能力，并为每个数据库副本提供最高的 i/o 性能。|具有高度可缩放的存储和读取缩放要求的大多数业务工作负荷。  允许配置多个独立的数据库副本，从而提供更高的故障恢复能力。 |
|存储空间|使用远程存储。<br/>**单一数据库和弹性池预配计算**：<br/>5 GB – 4 TB<br/>**无服务器计算**：<br/>5 GB-3 TB<br/>**托管实例**： 32 GB-8 TB |使用本地 SSD 存储。<br/>**单一数据库和弹性池预配计算**：<br/>5 GB – 4 TB<br/>**托管实例**：<br/>32 GB - 4 TB |根据需要灵活地自动增长存储。 最多支持 100 TB 的存储空间。 将本地 SSD 存储用于本地缓冲池缓存和本地数据存储。 使用 Azure 远程存储作为最终长期数据存储。 |
|I/o 吞吐量（近似）|**单一数据库和弹性池**：每个 VCORE 500 iops，最大 iops 为40000。<br/>**托管实例**：依赖于[文件的大小](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)。|每 vCore 5000 IOPS，最大 IOPS 为320000|超大规模是具有多个级别的缓存的多层体系结构。 有效 IOPs 将取决于工作负荷。|
|可用性|1个副本，无读取缩放副本|3 个副本，1 个[读取缩放副本](sql-database-read-scale-out.md)，<br/>区域冗余高可用性（HA）|1个读写副本，加上 0-4[个读取缩放副本](sql-database-read-scale-out.md)|
|备份|[读取访问异地冗余存储（GRS）](../storage/common/storage-designing-ha-apps-with-ragrs.md)，7-35 天（默认为7天）|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md)，7-35 天（默认为 7 天）|Azure 远程存储中基于快照的备份。 还原使用这些快照进行快速恢复。 备份是即时的，不会影响计算 i/o 性能。 还原速度很快，并且不是数据大小的操作（花费几分钟而不是几小时或几天）。|
|内存中|不支持|受支持|不支持|
|||


### <a name="choosing-a-service-tier"></a>选择服务层级

有关为特定工作负荷选择服务层的信息，请参阅以下文章：

- [何时选择 "常规用途" 服务层](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [何时选择业务关键的服务层](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [选择超大规模服务层的时间](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>计算层

VCore 模型中的计算层选项包括预配的和无服务器的计算层。


### <a name="provisioned-compute"></a>已预配计算

预配的计算层提供了一种特定数量的计算资源，这些资源的配置与工作负荷活动无关，并按固定价格（每小时）预配计算数量。


### <a name="serverless-compute"></a>无服务器计算

[无服务器计算层](sql-database-serverless.md)根据工作负荷活动自动缩放计算资源，并按每秒使用的计算量计费。



## <a name="hardware-generations"></a>硬件代

VCore 模型中的硬件生成选项包括 Gen 4/5、M 系列（预览版）和 Fsv2 （预览版）。 硬件生成通常定义计算和内存限制以及影响工作负荷性能的其他特征。

### <a name="gen4gen5"></a>Gen4/Gen5

- Gen4/Gen5 硬件提供均衡的计算和内存资源，适用于不具有更高内存、更高 vCore 或更快的单一 vCore 要求的大多数数据库工作负荷，如 Fsv2 系列或 M 系列所提供。

对于 Gen4/Gen5 可用的区域，请参阅[Gen4/Gen5 可用性](#gen4gen5-1)。

### <a name="fsv2-seriespreview"></a>Fsv2 系列（预览）

- Fsv2 系列是一种计算优化硬件选项，可为大多数 CPU 要求的工作负荷提供低 CPU 延迟和较高的时钟速度。
- 根据工作负荷的不同，Fsv2 系列可以为每个 vCore 提供比 Gen5 更多的 CPU 性能，72 vCore 大小可提供更多的 CPU 性能，而不是 Vcore 上 80 Gen5。 
- Fsv2 对每个 vCore 提供的内存和 tempdb 比其他硬件更少，因此，对这些限制敏感的工作负荷可能需要考虑 Gen5 或 M 系列。  

对于 Fsv2 系列可用的区域，请参阅[Fsv2 系列的可用性](#fsv2-series)。


### <a name="m-seriespreview"></a>M 系列（预览）

- M 系列是内存优化硬件选项，适用于需要更多内存和更高计算限制的工作负荷，而不是由 Gen5 提供。
- M 系列提供每 vCore 29 GB 和 128 Vcore，这会将相对于 Gen5 的内存限制增加到将近 4 TB。

若要为订阅和区域启用 M 系列硬件，必须打开支持请求。 如果支持请求获得批准，M 系列的选择和预配体验将遵循与其他硬件代相同的模式。 对于 M 系列可用的区域，请参阅[m 系列可用性](#m-series)。


### <a name="compute-and-memory-specifications"></a>计算和内存规格


|硬件代次  |计算  |内存  |
|:---------|:---------|:---------|
|Gen4     |-Intel E5-2673 v3 （Haswell） 2.4 GHz 处理器<br>-预配多达24个 Vcore （1 vCore = 1 个物理内核）  |-每个 vCore 7 GB<br>-预配高达 168 GB|
|Gen5     |**预配计算**<br>-Intel E5-2673 v4 （Broadwell） 2.3-GHz 和 Intel SP-8160 （Skylake）处理器<br>-预配多达 80 Vcore （1 vCore = 1 个超线程）<br><br>**无服务器计算**<br>-Intel E5-2673 v4 （Broadwell） 2.3-GHz 和 Intel SP-8160 （Skylake）处理器<br>-自动缩放多达16个 Vcore （1 vCore = 1 个超线程）|**预配计算**<br>-5.1 GB/vCore<br>-预配高达 408 GB<br><br>**无服务器计算**<br>-每 vCore 自动扩展到 24 GB<br>-自动缩放最大为 48 GB|
|Fsv2 系列     |-Intel 至强白金8168（SkyLake）处理器<br>-将所有核心 turbo 时钟速度保持为 3.4 GHz，最大单一核心 turbo 时钟速度为 3.7 GHz。<br>-设置 72 Vcore （1 vCore = 1 个超线程）|-1.9 GB/vCore<br>-预配 136 GB|
|M 系列     |-Intel E7-8890 v3 2.5 GHz 处理器<br>-设置 128 Vcore （1 vCore = 1 个超线程）|-每个 vCore 29 GB<br>-预配 3.7 TB|


有关资源限制的详细信息，请参阅[单一数据库的资源限制（vCore）](sql-database-vcore-resource-limits-single-databases.md)或[弹性池的资源限制（vCore）](sql-database-vcore-resource-limits-elastic-pools.md)。

### <a name="selecting-a-hardware-generation"></a>选择硬件生成

在 Azure 门户中，可以选择在创建时为 SQL 数据库或池生成硬件，也可以更改现有 SQL 数据库或池的硬件生成。

**创建 SQL 数据库或池时选择硬件生成**

有关详细信息，请参阅[创建 SQL 数据库](sql-database-single-database-get-started.md)。

在 "**基本**信息" 选项卡上，选择 "**计算 + 存储**" 部分中的 "**配置数据库**" 链接，然后选择 "**更改配置**" 链接：

  ![配置数据库](media/sql-database-service-tiers-vcore/configure-sql-database.png)

选择所需的硬件生成：

  ![选择硬件](media/sql-database-service-tiers-vcore/select-hardware.png)


**更改现有 SQL 数据库或池的硬件生成**

对于数据库，在 "概述" 页上，选择 "**定价层**" 链接：

  ![更改硬件](media/sql-database-service-tiers-vcore/change-hardware.png)

对于池，请在 "概述" 页上选择 "**配置**"。

按照上述步骤更改配置，并按前面的步骤中所述选择硬件生成。

**创建托管实例时选择硬件生成**

有关详细信息，请参阅[创建托管实例](sql-database-managed-instance-get-started.md)。

在 "**基本**信息" 选项卡上，选择 "**计算 + 存储**" 部分中的 "**配置数据库**" 链接，然后选择 "所需的硬件生成"：

  ![配置托管实例](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**更改现有托管实例的硬件生成**

使用以下 PowerShell 脚本：

```powershell-interactive
$subscriptionId = "**************"
Select-AzSubscription -Subscription $subscriptionId

$instanceName = "********"
$resourceGroup = "****"

# THIS IS IMPORTANT PARAMETER:
$sku = @{name = "GP_Gen5" }

# NOTE: These properties are not necessary, but it would be good to set them to the current values:
# You might want to change vCores or storage with hardware generation
# $admin_login = "******"
# $admin_pass = "******"
# $location = "***** # for example: ""northeurope"
# $vCores = 8
# $maxStorage = 1024
# $license = "BasePrice"
# $subnetId = "/subscriptions/****/subnets/*******"

## NOTE: Uncomment some of the properties below if you have set them.
$properties = New-Object System.Object
# $properties | Add-Member -type NoteProperty -name subnetId -Value $subnetId
# $properties | Add-Member -type NoteProperty -name administratorLogin -Value $admin_login
# $properties | Add-Member -type NoteProperty -name administratorLoginPassword -Value $admin_pass
# $properties | Add-Member -type NoteProperty -name vCores -Value $vCores
# $properties | Add-Member -type NoteProperty -name storageSizeInGB -Value $maxStorage
# $properties | Add-Member -type NoteProperty -name licenseType -Value $license

Set-AzResource -Properties $properties -ResourceName $instanceName -ResourceType "Microsoft.SQL/managedInstances" -Sku $sku -ResourceGroupName $resourceGroup -Force -ApiVersion "2015-05-01-preview"
```

请确保输入托管实例的订阅 id、名称和资源组。

### <a name="hardware-availability"></a>硬件可用性

#### <a name="gen4gen5-1"></a>Gen4/Gen5

澳大利亚东部或巴西南部区域不再支持新的 Gen4 数据库。 

Gen5 在世界各地的大多数区域中都可用。

#### <a name="fsv2-series"></a>Fsv2 系列

Fsv2 系列在以下区域中提供：澳大利亚中部、澳大利亚中部2、澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、东亚、美国东部、法国中部、印度中部、印度西部、韩国中部、韩国南部、北部欧洲、南非北部、东南亚、英国南部、英国西部、西欧、美国西部2。


#### <a name="m-series"></a>M 系列

M 系列在以下区域提供：美国东部、北欧、西欧、美国西部2。
在其他区域，M 系列也可能具有有限的可用性。 你可以请求与此处列出的区域不同的区域，但可能无法在不同的区域执行请求。

若要在订阅中启用 M 系列的可用性，必须通过[归档新的支持请求](#create-a-support-request-to-enable-m-series)来请求访问权限。


##### <a name="create-a-support-request-to-enable-m-series"></a>创建支持请求以启用 M 系列： 

1. 选择门户中的 "**帮助 + 支持**"。
2. 选择“新建支持请求”。

在 "**基本**信息" 页上，提供以下内容：

1. 对于“问题类型”，选择“服务和订阅限制(配额)”。
2. 对于**订阅**= 选择订阅以启用 M 系列。
3. 对于 "**配额类型**"，请选择 " **SQL 数据库**"。
4. 选择 "**下一步**" 以中转到**详细信息**页。

在**详细信息**页上，提供以下内容：

5. 在 "**问题详细信息**" 部分中，选择 "**提供详细信息**" 链接。 
6. 对于**SQL 数据库配额类型**，请选择**M 系列**。
7. 对于 "**区域**"，选择区域以启用 M 系列。
    对于 M 系列可用的区域，请参阅[m 系列可用性](#m-series)。

批准的支持请求通常在5个工作日内完成。


## <a name="next-steps"></a>后续步骤

- 若要创建 SQL 数据库，请参阅[使用 Azure 门户创建 sql 数据库](sql-database-single-database-get-started.md)。
- 有关适用于单一数据库的特定计算大小和存储大小选项，请参阅[针对单一数据库的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 有关弹性池可用的特定计算大小和存储大小选项，请参阅适用于[弹性池的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-elastic-pools.md)。
- 有关定价的详细信息，请参阅[AZURE SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/single/)。
