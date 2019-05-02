---
title: 将 Azure SQL 数据库备份存储 10 年之久 | Microsoft Docs
description: 了解 Azure SQL 数据库如何支持将完整数据库备份存储长达 10 年。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/23/2019
ms.openlocfilehash: 0f764ebbad53185f46c7166011e05493ed261d6a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696645"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>将 Azure SQL 数据库备份存储 10 年之久

出于法规要求、符合性或其他商业目的，许多应用程序要求保留 Azure SQL 数据库的[自动备份](sql-database-automated-backups.md)功能提供的过去 7-35 天的数据库备份。 通过使用长期保留 (LTR) 功能，可以将指定的 SQL 数据库完整备份存储在 [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) blob 存储中长达 10 年。 然后，可以将任何备份还原为新数据库。

> [!NOTE]
> 可以为单一数据库和共用数据库启用 LTR。 对于托管实例中的实例数据库，它还不可用。 可以使用 SQL 代理作业来安排[仅复制数据库备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)作为超过 35 天的 LTR 的替代方案。
> 

## <a name="how-sql-database-long-term-retention-works"></a>SQL 数据库长期保留的工作原理

长期备份保留 (LTR) 利用[自动创建](sql-database-automated-backups.md)的完整数据库备份来启用时间点还原 (PITR)。 如果配置了 LTR 策略，这些备份复制到不同 blob 以进行长期存储。 复制操作是对数据库工作负荷不会影响性能的后台作业。 LTR 备份将保留一段时间通过 LTR 策略设置。 每个 SQL 数据库 LTR 策略还可以指定创建 LTR 备份的频率。 若要启用这种灵活性可以定义策略使用的四个参数组合： 每周备份保留 (W)，每月备份保留 (M)、 每年备份保留 (Y) 和年 (WeekOfYear) 的一周。 如果指定 W，则每周会将一个备份复制到长期存储。 如果指定 M，则会在每月的第一周将一个备份复制到长期存储。 如果指定 Y，则会在 WeekOfYear 指定的周将一个备份复制到长期存储。 每个备份将在长期存储中保留由这些参数指定的期限。 LTR 策略的任何更改都适用于将来的备份。 例如，如果指定的 WeekOfYear 配置的策略是在过去，第一次的 LTR 备份将创建下一年。 

将 LTR 策略的示例：

-  W=0、M=0、Y=5、WeekOfYear=3

   将保留五年的每年的第三个完整备份。
   
- W=0、M=3、Y=0

   系统会保留三个月的每个月的第一个完整备份。

- W=12、M=0、Y=0

   每个每周完整备份将保留 12 周。

- W=6、M=12、Y=10、WeekOfYear=16

   系统会保留六周内的每个每周完整备份。 每月的第一个完整备份例外，该备份将保留 12 个月。 每年的第 16 周创建的完整备份例外，该备份将保留 10 年。 

下表说明了以下策略的长期备份的节奏和到期：

W=12 周（84 天）、M=12 个月（365 天）、Y=10 年（3650 天）、WeekOfYear=15（4 月 15 日后的周）

   ![ltr 示例](./media/sql-database-long-term-retention/ltr-example.png)



如果您修改上述策略并设置 W = 0 （无每周备份），频率的备份副本将更改为显示突出显示的日期按上表中。 保留这些备份所需的存储量将相应减少。 

> [!NOTE]
> 由 Azure SQL 数据库控制单个 LTR 备份的时间。 无法手动创建 LTR 备份或控制的备份创建时间。
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>异地复制和长期备份保留

如果使用活动异地复制或故障转移组作为你的业务连续性解决方案，应准备最终故障转移和异地辅助数据库上配置相同的 LTR 策略。 从辅助数据库不生成备份时，将不会增加 LTR 存储成本。 仅当辅助数据库成为主数据库时才会创建备份。 触发故障转移和主要副本会移到次要区域时，它可以确保非中断新一代 LTR 备份。 

> [!NOTE]
> 当原始主数据库恢复在中断后导致了故障转移时，它将成为新的辅助数据库。 因此，在该数据库再次变成主数据库前，备份生成将不会继续，现有 LTR 策略也不会生效。 

## <a name="configure-long-term-backup-retention"></a>配置长期备份保留

若要了解如何使用 Azure 门户或 PowerShell 配置长期保留，请参阅[管理 Azure SQL 数据库长期备份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="restore-database-from-ltr-backup"></a>从 LTR 备份还原数据库

若要从 LTR 存储还原数据库，可以根据时间戳选择一个特定备份。 数据库可以还原到原始数据库所在的订阅中的任何现有服务器。 若要了解如何将数据库还原从 LTR 备份，使用 Azure 门户或 PowerShell，请参阅[管理 Azure SQL 数据库长期备份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="next-steps"></a>后续步骤

数据库备份可保护数据免遭意外损坏或删除，因此数据库备份是任何业务连续性和灾难恢复策略不可或缺的组成部分。 若要了解其他 SQL 数据库业务连续性解决方案，请参阅[业务连续性概述](sql-database-business-continuity.md)。
