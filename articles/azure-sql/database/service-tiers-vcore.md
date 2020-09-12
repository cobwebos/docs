---
title: vCore 购买模型概述
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: 借助 vCore 购买模型，可单独缩放计算和存储资源、匹配本地性能，并优化 Azure SQL 数据库和 Azure SQL 托管实例的价格。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 08/14/2020
ms.openlocfilehash: 19f811c636d5cf3ffb3eef9904c7ba4f7d456b5f
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462201"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>vCore 模型概述 - Azure SQL 数据库和 Azure SQL 托管实例 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL 数据库和 Azure SQL 托管实例使用的虚拟核心 (vCore) 购买模型具有下面几个优势：

- 更高的计算、内存、I/O 和存储限制。
- 控制硬件代系，以便更好地符合工作负荷的计算和内存要求。
- [Azure 混合权益 (AHB) ](../azure-hybrid-benefit.md)和预留实例的定价折扣[ (RI) ](reserved-capacity-overview.md)。
- 为驱动计算的硬件细节提供更高的透明度，这有助于规划从本地部署进行的迁移。

## <a name="service-tiers"></a>服务层

vCore 模型中的服务层级选项包括“常规用途”、“业务关键”和“超大规模”。 服务层级通常定义存储体系结构、空间和 I/O 限制，以及与可用性和灾难恢复相关的业务连续性选项。

|-|**常规用途**|**业务关键**|**超大规模**|
|---|---|---|---|
|最适用于|大多数业务工作负荷。 提供预算导向的、均衡且可缩放的计算和存储选项。 |它使用多个独立副本为商业应用程序提供最高级别的故障恢复能力，为每个数据库副本提供最高的 I/O 性能。|具有很高的可缩放存储和读取缩放要求的大多数业务工作负荷。  允许配置多个独立的数据库副本，提供更高的故障恢复能力。 |
|存储|使用远程存储。<br/>**SQL 数据库预配计算**：<br/>5 GB – 4 TB<br/>**无服务器计算**<br/>5 GB - 3 TB<br/>**SQL 托管实例**：32 GB - 8 TB |使用本地 SSD 存储。<br/>**SQL 数据库预配计算**：<br/>5 GB – 4 TB<br/>**SQL 托管实例**：<br/>32 GB - 4 TB |可以根据需要灵活地自动扩展存储。 最多支持 100 TB 存储空间。 使用本地 SSD 存储作为本地缓冲池缓存和本地数据存储。 使用 Azure 远程存储作为最终的长期数据存储。 |
|IOPS 和吞吐量（近似值）|**SQL 数据库**：请查看[单一数据库](resource-limits-vcore-single-databases.md)和[弹性池](resource-limits-vcore-elastic-pools.md)的资源限制。<br/>**SQL 托管实例**：请参阅 [Azure SQL 托管实例资源限制概述](../managed-instance/resource-limits.md#service-tier-characteristics)。|请查看[单一数据库](resource-limits-vcore-single-databases.md)和[弹性池](resource-limits-vcore-elastic-pools.md)的资源限制。|超大规模是具有多个级别缓存的多层体系结构。 有效的 IOPS 和吞吐量将取决于工作负载。|
|可用性|1 个副本，无读取缩放副本|3个副本，1 [个读取缩放副本](read-scale-out.md)，<br/>区域冗余高可用性 (HA)|1 个读写副本加 0-4 个[读取缩放副本](read-scale-out.md)|
|备份|[读取访问异地冗余存储 (RA-GRS)](../../storage/common/geo-redundant-design.md)，7-35 天（默认为 7 天）|[RA-GRS](../..//storage/common/geo-redundant-design.md)，7-35 天（默认为 7 天）|Azure 远程存储中基于快照的备份。 还原使用这些快照进行快速恢复。 备份瞬间完成，不会影响计算 I/O 性能。 还原速度很快，不基于数据操作的大小（需要几分钟，而不是几小时或几天）。|
|内存中|不支持|支持|不支持|
|||


### <a name="choosing-a-service-tier"></a>选择服务层级

有关为特定工作负荷选择服务层级的信息，请参阅以下文章：

- [何时选择“常规用途”服务层级](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [何时选择“业务关键”服务层级](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [何时选择“超大规模”服务层级](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>计算层级

vCore 模型中的计算层级选项包括预配计算层级和无服务器计算层级。


### <a name="provisioned-compute"></a>预配计算

预配计算层级提供特定数量的计算资源，这些资源是独立于工作负荷活动持续预配的，针对预配的计算资源量按固定的小时价格计费。


### <a name="serverless-compute"></a>无服务器计算

[无服务器计算层级](serverless-tier-overview.md)根据工作负荷活动自动缩放计算资源，针对使用的计算资源量按秒计费。



## <a name="hardware-generations"></a>硬件代系

VCore 模型中的硬件生成选项包括 Gen 4/5、M 系列和 Fsv2 系列。 硬件代系通常定义计算和内存限制，以及影响工作负荷性能的其他特征。

### <a name="gen4gen5"></a>第 4 代/第 5 代

- 第 4 代/第 5 代硬件提供均衡的计算和内存资源，适合用于内存和 vCore 要求不太高，或者不需要较快的单一 vCore 的大部分数据库工作负荷，而 Fsv2 系列或 M 系列可以满足这些要求。

有关第 4 代/第 5 代的可用区域，请参阅[第 4 代/第 5 代可用性](#gen4gen5-1)。

### <a name="fsv2-series"></a>Fsv2 系列

- Fsv2 系列是一种计算优化硬件选项，可为大多数 CPU 要求的工作负荷提供低 CPU 延迟和较高的时钟速度。
- 根据工作负荷的不同，Fsv2 系列可以为每个 vCore 提供比 Gen5 更多的 CPU 性能，72 vCore 大小可提供更多的 CPU 性能，而不是 Vcore 上 80 Gen5。 
- Fsv2 对每个 vCore 提供的内存和 tempdb 比其他硬件更少，因此，对这些限制敏感的工作负荷可能需要考虑 Gen5 或 M 系列。  

仅常规用途层支持 Fsv2 系列。 对于 Fsv2 系列可用的区域，请参阅 [Fsv2 系列的可用性](#fsv2-series-1)。


### <a name="m-series"></a>M 系列

- M 系列是内存优化硬件选项，适用于需要更多内存和更高计算限制的工作负荷，而不是由 Gen5 提供。
- M 系列提供每个 vCore 29 GB 和最多128个 Vcore，这会将相对于 Gen5 的内存限制增加到将近 4 TB。

M 系列仅在业务关键层中受支持，不支持区域冗余。  订阅必须是付费产品/服务类型，包括即用即付或企业协议 (EA) 。 对于 M 系列可用的区域，请参阅 [m 系列可用性](#m-series-1)。

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="compute-and-memory-specifications"></a>计算和内存规格


|硬件代次  |计算  |内存  |
|:---------|:---------|:---------|
|Gen4     |-Intel® E5-2673 v3 (Haswell) 2.4 GHz 处理器<br>- 最多预配 24 个 vCore（1 个 vCore = 1 个物理核心）  |- 每个 vCore 7 GB<br>- 最多预配 168 GB|
|Gen5     |**预配计算**<br>-Intel® E5-2673 v4 (Broadwell) 2.3-GHz、Intel® SP-8160 (Skylake) \* ，以及 intel® 8272CL (2.5 GHz \* 处理器<br>- 最多预配 80 个 vCore（1 个 vCore = 1 个超线程）<br><br>**无服务器计算**<br>-Intel® E5-2673 v4 (Broadwell) 2.3-GHz 和 Intel® SP-8160 (Skylake) * 处理器<br>-自动向上缩放到 40 Vcore (1 vCore = 1 超线程) |**预配计算**<br>- 每个 vCore 5.1 GB<br>- 最多预配 408 GB<br><br>**无服务器计算**<br>- 自动扩展为每个vCore 24 GB<br>-自动缩放最大为 120 GB|
|Fsv2 系列     |-Intel® 8168 (Skylake) 处理器<br>-将所有核心 turbo 时钟速度保持为 3.4 GHz，最大单一核心 turbo 时钟速度为 3.7 GHz。<br>-预配多达 72 Vcore (1 vCore = 1 超线程) |-1.9 GB/vCore<br>-预配高达 136 GB|
|M 系列     |-Intel® E7-8890 v3 2.5 GHz 和 Intel® 8280M 2.7 GHz (级联 Lake) 处理器<br>-预配多达 128 Vcore (1 vCore = 1 超线程) |-每个 vCore 29 GB<br>-预配高达 3.7 TB|

\* 在 Skylake 动态管理视图 [dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 中，使用 INTEL® SP-8160 () 处理器的数据库的硬件生成显示为 Gen6，而使用 INTEL® 8272CL (Cascade Lake) 的数据库的硬件生成则显示为 Gen7。 无论处理器类型 (Broadwell、Skylake 或 Cascade Lake) ，所有 Gen5 数据库的资源限制都是相同的。

有关资源限制的详细信息，请参阅[单一数据库的资源限制 (vCore)](resource-limits-vcore-single-databases.md) 或[弹性池的资源限制 (vCore)](resource-limits-vcore-elastic-pools.md)。

### <a name="selecting-a-hardware-generation"></a>选择硬件代系

在 Azure 门户中，可在 SQL 数据库中创建数据库或池时为其选择硬件代系，或者更改现有 SQL 数据库或池的硬件代系。

**创建 SQL 数据库或池时选择硬件代系**

有关详细信息，请参阅[创建 SQL 数据库](single-database-create-quickstart.md)。

在“基本信息”选项卡上，选择“计算 + 存储”部分中的“配置数据库”链接，然后选择“更改配置”链接：   

  ![配置数据库](./media/service-tiers-vcore/configure-sql-database.png)

选择所需的硬件代系：

  ![选择硬件](./media/service-tiers-vcore/select-hardware.png)


**更改现有 SQL 数据库或池的硬件代系**

对于数据库，请在“概述”页上选择“定价层”链接：

  ![更改硬件](./media/service-tiers-vcore/change-hardware.png)

对于池，请在“概述”页上选择“配置”。

遵循相应的步骤更改配置，然后根据前面的步骤所述选择硬件代系。

**创建 SQL 托管实例时选择硬件代系**

有关详细信息，请参阅[创建 SQL 托管实例](../managed-instance/instance-create-quickstart.md)。

在“基本信息”选项卡上，选择“计算 + 存储”部分中的“配置数据库”链接，然后选择所需的硬件代系：  

  ![配置 SQL 托管实例](./media/service-tiers-vcore/configure-managed-instance.png)
  
**更改现有 SQL 托管实例的硬件代系**

# <a name="the-azure-portal"></a>[Azure 门户](#tab/azure-portal)

在“SQL 托管实例”页面上，选择“设置”部分下的“定价层”链接

![更改 SQL 托管实例硬件](./media/service-tiers-vcore/change-managed-instance-hardware.png)

在“定价层”页上，可以按前面步骤中所述更改硬件代系。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用以下 PowerShell 脚本：

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

有关更多详细信息，请查看 [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance) 命令。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用以下 CLI 命令：

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

有关更多详细信息，请查看 [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update) 命令。

---

### <a name="hardware-availability"></a>硬件可用性

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a> 第 4 代/第 5 代

Gen4 硬件正在 [逐步推出](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) ，在新部署中不再可用。 所有新的数据库都必须部署在第 5 代硬件上。

Gen5 在世界各地的大多数区域中都可用。

#### <a name="fsv2-series"></a>Fsv2 系列

Fsv2 系列在以下区域中提供：澳大利亚中部、澳大利亚中部2、澳大利亚东部、澳大利亚东南部、巴西南部、加拿大中部、东亚、美国东部、法国中部、印度中部、韩国中部、韩国南部、北欧、南非北部、东南亚、英国南部、英国西部、西欧、美国西部2。


#### <a name="m-series"></a>M 系列

M 系列在以下区域提供：美国东部、北欧、西欧、美国西部2。
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

## <a name="next-steps"></a>后续步骤

如要入门，请参阅： 
- [使用 Azure 门户创建 SQL 数据库](single-database-create-quickstart.md)
- [使用 Azure 门户创建 SQL 托管实例](../managed-instance/instance-create-quickstart.md)

有关定价详细信息，请参阅 [Azure SQL 数据库定价页](https://azure.microsoft.com/pricing/details/sql-database/single/)。

若要详细了解“常规用途”和“业务关键”服务层级中提供的特定计算大小和存储大小，请参阅：

- [Azure SQL 数据库基于 vCore 的资源限制](resource-limits-vcore-single-databases.md)。
- [共用 Azure SQL 数据库基于 vCore 的资源限制](resource-limits-vcore-elastic-pools.md)。
- [Azure SQL 托管实例基于 vCore 的资源限制](../managed-instance/resource-limits.md)。 

