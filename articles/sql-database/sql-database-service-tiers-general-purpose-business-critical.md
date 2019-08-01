---
title: Azure SQL 数据库 - 常规用途和业务关键 | Microsoft Docs
description: 本文介绍了基于 vCore 的购买模型中的常规用途和业务关键服务层。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 02/23/2019
ms.openlocfilehash: decb4428321d5083d6ba7af134e223eb2fa5a912
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566710"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL 数据库服务层级

Azure SQL 数据库基于 SQL Server 数据库引擎体系结构, 针对云环境进行调整, 以确保 99.99% 的可用性, 即使存在基础结构故障也是如此。 在 Azure SQL 数据库中使用三个服务层, 每个层都有不同的体系结构模型。 这些服务层级为：

- [一般用途](sql-database-service-tier-general-purpose.md), 适用于大多数一般工作负荷。
- [业务关键](sql-database-service-tier-business-critical.md), 适用于具有一个可读副本的低延迟工作负荷。
- [超大规模](sql-database-service-tier-hyperscale.md), 适用于具有多个可读副本的超大型数据库 (最大为 100 TB)。

本文介绍了基于 vCore 的购买模型中常规用途和业务关键服务层的存储和备份注意事项。

> [!NOTE]
> 有关基于 vCore 的购买模型中超大规模服务层的信息, 请参阅[超大规模服务层](sql-database-service-tier-hyperscale.md)。 有关基于 vCore 购买模型与基于 DTU 购买模型的比较，请参阅 [Azure SQL 数据库购买模型和资源](sql-database-purchase-models.md)。

## <a name="data-and-log-storage"></a>数据和日志存储

以下因素会影响用于数据文件和日志文件的存储量:

- 已分配的存储由数据文件 (MDF) 和日志文件 (.LDF) 使用。
- 每个数据库计算大小都支持最大数据库大小, 默认的最大大小为 32 GB。
- 配置所需的单一数据库大小 (MDF 文件的大小) 时, 将自动添加额外的额外存储空间以支持 LDF 文件。
- SQL 数据库托管实例的存储大小必须以 32 GB 的倍数指定。
- 您可以选择 10 GB 到最大支持值之间的任意单一数据库大小。
  - 对于标准或常规用途服务层中的存储, 以 10 GB 为增量增加或减少大小。
  - 对于 "高级" 或 "业务关键" 服务层中的存储, 增加或减少 250 MB 的增量。
- 在常规用途的服务层中`tempdb` , 使用附加的 SSD, 此存储成本包括在 vCore 价格中。
- 在 "业务关键" 服务层`tempdb`中, 与 MDF 和 LDF 文件共享附加的 SSD, `tempdb`存储成本包括在 vCore 价格中。

> [!IMPORTANT]
> 需要为 MDF 和 LDF 文件分配的总存储收费。

若要监视当前的 MDF 和 LDF 文件的总大小, 请使用[sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)。 若要监视单个 MDF 和 LDF 文件的当前大小，请使用 [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)。

> [!IMPORTANT]
> 在某些情况下，可能需要收缩数据库来回收未使用的空间。 有关详细信息，请参阅[管理 Azure SQL 数据库中的文件空间](sql-database-file-space-management.md)。

## <a name="backups-and-storage"></a>备份和存储

为数据库备份分配存储, 以支持 SQL 数据库的时间点还原 (PITR) 和[长期保留 (LTR)](sql-database-long-term-retention.md)功能。 此存储空间针对每个数据库单独分配，并根据数据库费用分开计费。

- **PITR**：单个数据库备份会自动复制到[读取访问地域冗余 (GRS) 存储](../storage/common/storage-designing-ha-apps-with-ragrs.md)中。 创建新的备份时, 存储大小会动态增加。 存储由每周完整备份、每日差异备份和事务日志备份使用, 每5分钟复制一次。 存储占用量取决于数据库的更改速率和备份的保持期。 可单独为每个数据库配置 7 到 35 天的保留期。 仅提供等于 100% (1x) 的数据库大小的最小存储量, 无需额外付费。 对于大多数数据库而言，此容量足以将备份存储 7 天。
- **LTR**：通过 SQL 数据库, 你可以选择将完整备份的长期保留配置为长达10年。 如果设置了 LTR 策略, 则这些备份会自动存储在 GRS 存储中, 但你可以控制复制备份的频率。 为了满足不同的符合性要求, 你可以为每周、每月和/或每年备份选择不同的保留期。 你选择的配置确定将用于 LTR 备份的存储量。 若要估算 LTR 存储的开销, 可以使用 LTR 定价计算器。 有关详细信息, 请参阅[SQL 数据库长期保留](sql-database-long-term-retention.md)。

## <a name="next-steps"></a>后续步骤

- 有关 "常规用途" 和 "业务关键" 服务层中的单一数据库可用的特定计算大小和存储大小的详细信息, 请参阅[针对单一数据库的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-single-databases.md)。
- 有关 "常规用途" 和 "业务关键" 服务层中的弹性池可用的特定计算大小和存储大小的详细信息, 请参阅[针对弹性池的 SQL 数据库基于 vCore 的资源限制](sql-database-vcore-resource-limits-elastic-pools.md)。
