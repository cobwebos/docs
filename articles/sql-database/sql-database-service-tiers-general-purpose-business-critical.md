---
title: Azure SQL 数据库 - 常规用途和业务关键 | Microsoft Docs
description: 本文讨论的常规用途和业务关键服务层中基于 vCore 的购买模型。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 02/23/2019
ms.openlocfilehash: e5426bb7c8eba9d58dbf0472360c6ce0b19c9bc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66431340"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL 数据库服务层

Azure SQL 数据库根据 SQL Server 数据库引擎体系结构，即使没有基础结构故障，为云环境，以确保 99.99%的可用性，调整。 在 Azure SQL 数据库，每个都有不同的体系结构模型中使用三个服务层。 这些服务层为：

- [常规用途](sql-database-service-tier-general-purpose.md)，这适用于最一般的工作负荷。
- [业务关键](sql-database-service-tier-business-critical.md)，这适用于具有一个可读副本的低延迟工作负荷。
- [超大规模](sql-database-service-tier-hyperscale.md)，这用于非常大的数据库 (最多 100 TB) 与多个可读副本。

本文介绍的常规用途和基于 vCore 的购买模型中的业务关键服务层的存储和备份注意事项。

> [!NOTE]
> 有关基于 vCore 的购买模型中的超大规模服务层的信息，请参阅[超大规模服务层](sql-database-service-tier-hyperscale.md)。 有关基于 vCore 购买模型与基于 DTU 购买模型的比较，请参阅 [Azure SQL 数据库购买模型和资源](sql-database-purchase-models.md)。

## <a name="data-and-log-storage"></a>数据和日志存储

以下因素会影响用于数据和日志文件的存储量：

- 数据文件 (MDF) 和日志文件 (LDF) 使用已分配的存储。
- 每个单个数据库的计算大小支持具有默认的最大大小为 32 GB 的最大数据库大小。
- 在配置所需的单个数据库大小 （MDF 文件的大小） 时，自动添加 30%更多其他存储来支持 LDF 文件。
- SQL 数据库托管实例的存储大小必须指定 32 GB 的倍数。
- 您可以选择 10 GB 与受支持的最大值之间的任何单个的数据库大小。
  - 以存储在标准或常规用途服务层，增加或减少的大小以 10 GB 为增量。
  - 以存储在高级或业务关键服务层，增加或减少大小 250 GB 为增量。
- 在常规用途服务层中，`tempdb`使用附加的 SSD，，此存储成本包含在 vCore 价格中。
- 在业务关键服务层`tempdb`与 MDF 和 LDF 文件共享附加的 SSD 和`tempdb`存储成本包含在 vCore 价格中。

> [!IMPORTANT]
> 收费为 MDF 和 LDF 文件分配的总存储。

若要监视 MDF 和 LDF 文件的当前总大小，请使用[sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)。 若要监视单个 MDF 和 LDF 文件的当前大小，请使用 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

## <a name="backups-and-storage"></a>备份和存储

用于数据库备份存储分配以支持时间点还原 (PITR) 和[长期保留 (LTR)](sql-database-long-term-retention.md) SQL 数据库功能。 此存储空间针对每个数据库单独分配，并根据数据库费用分开计费。

- **PITR**：单独的数据库备份复制到[读取访问异地冗余 (RA-GRS) 存储](../storage/common/storage-designing-ha-apps-with-ragrs.md)自动。 创建新的备份时，存储大小会动态增加。 通过每周完整备份、 每日差异备份和事务日志备份，将其复制每 5 分钟使用的存储。 存储消耗量取决于数据库和备份保留期的更改的速率。 可单独为每个数据库配置 7 到 35 天的保留期。 最小存储量等于 100%(1 x) 的数据库大小提供无需额外付费。 对于大多数数据库而言，此容量足以将备份存储 7 天。
- **LTR**：SQL 数据库提供的完整备份的长期保留期配置为最多 10 年的选项。 如果设置了 LTR 策略时，这些备份存储在 RA-GRS 存储中自动，但您可以控制备份均复制频率。 若要满足不同的符合性要求，可以选择每周、 每月和/或每年备份的不同的保持期。 你选择的配置确定多少存储空间将用于 LTR 备份。 若要估算 LTR 存储成本，可以使用 LTR 定价计算器。 有关详细信息，请参阅[SQL 数据库长期保留](sql-database-long-term-retention.md)。

## <a name="next-steps"></a>后续步骤

- 有关详细信息，有关特定计算大小和适用于常规用途和业务关键服务层中的单一数据库存储大小，请参阅[单一数据库的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 有关详细信息，有关特定计算大小和存储大小中的常规用途和业务关键服务层弹性池的可用，请参阅[弹性池的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-elastic-pools.md)。
