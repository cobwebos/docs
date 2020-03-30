---
title: vCore 模型概述
description: 使用 vCore 购买模型，可以单独缩放计算和存储资源、匹配本地性能，以及优化价格。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 5fd69dcd30292630862887ab5434764ba377b396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481076"
---
# <a name="vcore-model-overview"></a>vCore 模型概述

虚拟核心 (vCore) 模型提供以下几项优势：

- 更高的计算、内存、IO 和存储限制。
- 控制硬件代系，以便更好地符合工作负荷的计算和内存要求。
- [Azure 混合权益 （AHB）](sql-database-azure-hybrid-benefit.md)和[预留实例 （RI）](sql-database-reserved-capacity.md)的定价折扣。
- 为驱动计算的硬件细节提供更高的透明度；有助于规划从本地部署的迁移。

## <a name="service-tiers"></a>服务层

vCore 模型中的服务层级选项包括“常规用途”、“业务关键”和“超大规模”。 服务层级通常定义存储体系结构、空间和 IO 限制，以及与可用性和灾难恢复相关的业务连续性选项。

||**一般用途**|**业务关键型**|**超大规模**|
|---|---|---|---|
|最适用于|大多数业务工作负荷。 提供预算导向的、均衡且可缩放的计算和存储选项。 |它使用多个独立副本为商业应用程序提供最高级别的故障恢复能力，为每个数据库副本提供最高的 I/O 性能。|具有很高的可缩放存储和读取缩放要求的大多数业务工作负荷。  允许配置多个独立的数据库副本，提供更高的故障恢复能力。 |
|存储|使用远程存储。<br/>**单一数据库和弹性池预配计算**：<br/>5 GB – 4 TB<br/>**无服务器计算**：<br/>5 GB - 3 TB<br/>**托管实例**： 32 GB - 8 TB |使用本地 SSD 存储。<br/>**单一数据库和弹性池预配计算**：<br/>5 GB – 4 TB<br/>**托管实例**：<br/>32 GB - 4 TB |可以根据需要灵活地自动扩展存储。 最多支持 100 TB 存储空间。 使用本地 SSD 存储作为本地缓冲池缓存和本地数据存储。 使用 Azure 远程存储作为最终的长期数据存储。 |
|IOPS 和吞吐量（近似值）|**单个数据库和弹性池**：请参阅[单个数据库](../sql-database/sql-database-vcore-resource-limits-single-databases.md)和[弹性池](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)的资源限制。<br/>**托管实例**：请参阅[概述 Azure SQL 数据库托管实例资源限制](../sql-database/sql-database-managed-instance-resource-limits.md#service-tier-characteristics)。|请查看[单一数据库](../sql-database/sql-database-vcore-resource-limits-single-databases.md)和[弹性池](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)的资源限制。|超大规模是具有多个级别缓存的多层体系结构。 有效的 IOPS 和吞吐量将取决于工作负载。|
|可用性|1 个副本，无读取缩放副本|3 个副本，1 个[读取缩放副本](sql-database-read-scale-out.md)，<br/>区域冗余高可用性 (HA)|1 个读写副本加 0-4 个[读取缩放副本](sql-database-read-scale-out.md)|
|备份|[读取访问异地冗余存储 (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md)，7-35 天（默认为 7 天）|[RA-GRS，](../storage/common/storage-designing-ha-apps-with-ragrs.md)7-35 天 （默认为 7 天）|Azure 远程存储中基于快照的备份。 还原使用这些快照进行快速恢复。 备份瞬间完成，不会影响计算 I/O 性能。 还原速度很快，不基于数据操作的大小（需要几分钟，而不是几小时或几天）。|
|内存中|不支持|支持|不支持|
|||


### <a name="choosing-a-service-tier"></a>选择服务层级

有关为特定工作负荷选择服务层级的信息，请参阅以下文章：

- [何时选择“常规用途”服务层级](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [何时选择“业务关键”服务层级](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [何时选择“超大规模”服务层级](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>计算层级

vCore 模型中的计算层级选项包括预配计算层级和无服务器计算层级。


### <a name="provisioned-compute"></a>预配计算

预配计算层级提供特定数量的计算资源，这些资源是独立于工作负荷活动持续预配的，针对预配的计算资源量按固定的小时价格计费。


### <a name="serverless-compute"></a>无服务器计算

[无服务器计算层级](sql-database-serverless.md)根据工作负荷活动自动缩放计算资源，针对使用的计算资源量按秒计费。



## <a name="hardware-generations"></a>硬件代系

vCore 模型中的硬件生成选项包括第 4/5 代、M 系列（预览版）和 Fsv2 系列（预览）。 硬件代系通常定义计算和内存限制，以及影响工作负荷性能的其他特征。

### <a name="gen4gen5"></a>第 4 代/第 5 代

- 第 4 代/第 5 代硬件提供均衡的计算和内存资源，适合用于内存和 vCore 要求不太高，或者不需要较快的单一 vCore 的大部分数据库工作负荷，而 Fsv2 系列或 M 系列可以满足这些要求。

有关第 4 代/第 5 代的可用区域，请参阅[第 4 代/第 5 代可用性](#gen4gen5-1)。

### <a name="fsv2-seriespreview"></a>Fsv2 系列（预览版）

- Fsv2 系列是一种计算优化的硬件选项，可提供低 CPU 延迟和高时钟速度，适用于 CPU 要求最高的工作负载。
- 根据工作负载，Fsv2 系列可以比 Gen5 提供更多的 CPU 性能，72 vCore 大小可以提供比 Gen5 上的 80 vCore 更多的 CPU 性能。 
- 与其他硬件相比，Fsv2 每个 vCore 提供的内存和 tempdb 更少，因此对这些限制敏感的工作负载可能希望改为 Gen5 或 M 系列。  

Fsv2 系列仅在通用层中支持。  有关 Fsv2 系列可用的区域，请参阅[Fsv2 系列可用性](#fsv2-series)。


### <a name="m-seriespreview"></a>M 系列（预览版）

- M 系列是一种内存优化的硬件选项，适用于需要比 Gen5 更高的内存和更高的计算限制的工作负载。
- M 系列提供每个 vCore 29 GB 和 128 vCore，这使得相对于 Gen5 的内存限制增加 8 倍到近 4 TB。

M 系列仅在业务关键型层中支持，不支持区域冗余。

要为订阅和地区启用 M 系列硬件，必须打开支持请求。 订阅必须是付费产品/服务类型，包括即用即付或企业协议 （EA）。  如果支持请求获得批准，则 M 系列的选择和预配体验遵循与其他硬件代相同的模式。 有关 M 系列可用的区域，请参阅[M 系列可用性](#m-series)。


### <a name="compute-and-memory-specifications"></a>计算和内存规格


|硬件代次  |计算  |内存  |
|:---------|:---------|:---------|
|Gen4     |- Intel E5-2673 v3 (Haswell) 2.4 GHz 处理器<br>- 最多预配 24 个 vCore（1 个 vCore = 1 个物理核心）  |- 每个 vCore 7 GB<br>- 最多预配 168 GB|
|Gen5     |**预配计算**<br>- 英特尔 E5-2673 v4 （Broadwell） 2.3-GHz 和英特尔 SP-8160 （天湖）* 处理器<br>- 最多预配 80 个 vCore（1 个 vCore = 1 个超线程）<br><br>**无服务器计算**<br>- 英特尔 E5-2673 v4 （Broadwell） 2.3-GHz 和英特尔 SP-8160 （天湖）* 处理器<br>- 自动扩展为 16 个 vCore（1 个 vCore = 1 个超线程）|**预配计算**<br>- 每个 vCore 5.1 GB<br>- 最多预配 408 GB<br><br>**无服务器计算**<br>- 自动扩展为每个vCore 24 GB<br>- 自动扩展为最大 48 GB|
|Fsv2 系列     |- 英特尔 Xeon 白金 8168 （天湖） 处理器<br>- 具有持续的所有核心涡轮时钟速度为 3.4 GHz 和最大单芯涡轮时钟速度为 3.7 GHz。<br>- 预配 72 vCore（1 个 vCore = 1 个超线程）|- 每个 vCore 1.9 GB<br>- 预配 136 GB|
|M 系列     |- 英特尔至强 E7-8890 v3 2.5 GHz 处理器<br>- 预配 128 vCore（1 个 vCore = 1 个超线程）|- 每个 vCore 29 GB<br>- 拨备3.7 TB|

\*在[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)动态管理视图中，使用英特尔 SP-8160（Skylake） 处理器的 Gen5 数据库的硬件生成显示为 Gen6。 无论处理器类型（Broadwell 或 Skylake），所有 Gen5 数据库的资源限制都是相同的。

有关资源限制的详细信息，请参阅[单一数据库的资源限制 (vCore)](sql-database-vcore-resource-limits-single-databases.md) 或[弹性池的资源限制 (vCore)](sql-database-vcore-resource-limits-elastic-pools.md)。

### <a name="selecting-a-hardware-generation"></a>选择硬件代系

在 Azure 门户中，可以在创建 SQL 数据库或池时为其选择硬件代系，或者更改现有 SQL 数据库或池的硬件代系。

**创建 SQL 数据库或池时选择硬件代系**

有关详细信息，请参阅[创建 SQL 数据库](sql-database-single-database-get-started.md)。

在“基本信息”选项卡上，选择“计算 + 存储”部分中的“配置数据库”链接，然后选择“更改配置”链接：****************

  ![配置数据库](media/sql-database-service-tiers-vcore/configure-sql-database.png)

选择所需的硬件代系：

  ![选择硬件](media/sql-database-service-tiers-vcore/select-hardware.png)


**更改现有 SQL 数据库或池的硬件代系**

对于数据库，请在“概述”页上选择“定价层”链接：****

  ![更改硬件](media/sql-database-service-tiers-vcore/change-hardware.png)

对于池，请在“概述”页上选择“配置”。****

遵循相应的步骤更改配置，然后根据前面的步骤所述选择硬件代系。

**在创建托管实例时选择硬件生成**

有关详细信息，请参阅[创建托管实例](sql-database-managed-instance-get-started.md)。

在 **"基本"** 选项卡上，在 **"计算 + 存储**"部分中选择 **"配置数据库**"链接，然后选择所需的硬件生成：

  ![配置托管实例](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**更改现有托管实例的硬件生成**

# <a name="portal"></a>[门户](#tab/azure-portal)

从托管实例页面，选择"设置"部分下的 **"定价层**"链接

![更改托管实例硬件](media/sql-database-service-tiers-vcore/change-managed-instance-hardware.png)

在 **"定价层"** 页上，您将能够更改硬件生成，如前面的步骤所述。

# <a name="powershell"></a>[电源外壳](#tab/azure-powershell)

使用以下 PowerShell 脚本：

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

有关详细信息，请选中["设置-AzSqlInstance"](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)命令。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用以下 CLI 命令：

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

有关详细信息，请查看[az sql mi 更新](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)命令。

---

### <a name="hardware-availability"></a>硬件可用性

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a> 第 4 代/第 5 代

Gen4 硬件[正在逐步淘汰](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)，不再可用于新部署。 所有新数据库都必须部署在 Gen5 硬件上。

第 5 代在全球大多数地区都有售。

#### <a name="fsv2-series"></a>Fsv2 系列

Fsv2 系列在以下区域提供：澳大利亚中部、澳大利亚中部 2、澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、东亚、东美国、法国中部、印度中部、印度西部、韩国中部、韩国南部、北部欧洲、南非北部、东南亚、英国南部、英国西部、西欧、西国2。


#### <a name="m-series"></a>M 系列

M 系列在以下区域提供：美国东部、北欧、西欧、美国西部 2。
M 系列在其他地区的可用性也可能有限。 您可以请求与此处列出的区域不同的区域，但可能无法在不同区域中实现。

要在订阅中启用 M 系列可用性，必须通过[提交新的支持请求](#create-a-support-request-to-enable-m-series)来请求访问。


##### <a name="create-a-support-request-to-enable-m-series"></a>创建支持请求以启用 M 系列： 

1. 在门户中选择 **"帮助 + 支持**"。
2. 选择 **"新建支持请求**"。

在 **"基础知识"** 页上，提供以下内容：

1. 对于“问题类型”，选择“服务和订阅限制(配额)”********。
2. 对于**订阅**= 选择订阅以启用 M 系列。
3. 对于**配额类型**，选择**SQL 数据库**。
4. 选择 **"下一步**"转到 **"详细信息"** 页。

在 **"详细信息"** 页上，提供以下内容：

1. 在 **"问题详细信息**"部分中，选择"**提供详细信息**"链接。 
2. 对于**SQL 数据库配额类型**，请选择**M 系列**。
3. 对于**区域**，选择要启用 M 系列的区域。
    有关 M 系列可用的区域，请参阅[M 系列可用性](#m-series)。

已批准的支持请求通常在 5 个工作日内完成。


## <a name="next-steps"></a>后续步骤

- 若要创建 SQL 数据库，请参阅[使用 Azure 门户创建 SQL 数据库](sql-database-single-database-get-started.md)。
- 有关适用于单一数据库的特定计算大小和存储大小选项，请参阅[适用于单一数据库的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 有关适用于弹性池的特定计算大小和存储大小选项，请参阅[适用于弹性池的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-elastic-pools.md)。
- 有关定价详细信息，请参阅 [Azure SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/single/)。
