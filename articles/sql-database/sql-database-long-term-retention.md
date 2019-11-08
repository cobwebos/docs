---
title: 将备份存储多达10年
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
ms.date: 05/18/2019
ms.openlocfilehash: 0cd4c45403d59819bf7ba729ea99de76ccf967ca
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819899"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>将 Azure SQL 数据库备份存储 10 年之久

出于法规要求、符合性或其他商业目的，许多应用程序要求保留 Azure SQL 数据库的[自动备份](sql-database-automated-backups.md)功能提供的过去 7-35 天的数据库备份。 通过使用长期保留 (LTR) 功能，可以将指定的 SQL 数据库完整备份存储在 [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) blob 存储中长达 10 年。 然后，可以将任何备份还原为新数据库。

> [!NOTE]
> 可以为单一数据库和共用数据库启用 LTR。 对于托管实例中的实例数据库，它还不可用。 可以使用 SQL 代理作业来安排[仅复制数据库备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)作为超过 35 天的 LTR 的替代方案。
> 

## <a name="how-sql-database-long-term-retention-works"></a>SQL 数据库长期保留的工作原理

长期备份保留 (LTR) 利用[自动创建](sql-database-automated-backups.md)的完整数据库备份来启用时间点还原 (PITR)。 如果配置了 LTR 策略，这些备份将复制到不同的 Blob 进行长期存储。 复制操作是一个后台作业，不会对数据库工作负荷造成性能影响。 LTR 备份将按照 LTR 策略设置的期限保留。 每个 SQL 数据库的 LTR 策略还可以指定创建 LTR 备份的频率。 若要实现该灵活性，可以使用以下四个参数的组合定义策略：每周备份保留 (W)、每月备份保留 (M)、每年备份保留 (Y) 和年中的周 (WeekOfYear)。 如果指定 W，则每周会将一个备份复制到长期存储。 如果指定 M，则会在每月的第一周将一个备份复制到长期存储。 如果指定 Y，则会在 WeekOfYear 指定的周将一个备份复制到长期存储。 每个备份将在长期存储中保留由这些参数指定的期限。 LTR 策略的任何更改将应用到将来的备份。 例如，如果配置策略时指定的 WeekOfYear 在过去，则将在明年创建第一个 LTR 备份。 

LTR 策略的示例：

-  W=0、M=0、Y=5、WeekOfYear=3

   每年的第 3 个完整备份将保留 5 年。
   
- W=0、M=3、Y=0

   每月的第一个完整备份将保留 3 个月。

- W=12、M=0、Y=0

   每个每周完整备份将保留 12 周。

- W=6、M=12、Y=10、WeekOfYear=16

   每个每周完整备份将保留 6 周。 每月的第一个完整备份例外，该备份将保留 12 个月。 每年的第 16 周创建的完整备份例外，该备份将保留 10 年。 

下表说明了以下策略的长期备份的节奏和到期：

W=12 周（84 天）、M=12 个月（365 天）、Y=10 年（3650 天）、WeekOfYear=15（4 月 15 日后的周）

   ![ltr 示例](./media/sql-database-long-term-retention/ltr-example.png)



如果修改以上策略并设置 W=0（无每周备份），则备份副本的节奏将更改，如上表中突出显示的日期所示。 保留这些备份所需的存储量将相应减少。 

> [!IMPORTANT]
> 单个 LTR 备份的时间由 Azure SQL 数据库控制。 无法手动创建 LTR 备份，或控制备份创建时间。 配置 LTR 策略后，最多可能需要 7 天才能在可用备份列表中显示第一个 LTR 备份。  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>异地复制和长期备份保留

如果使用活动异地复制或故障转移组作为业务连续性解决方案，应准备好最终故障转移，并在异地辅助数据库中配置相同的 LTR 策略。 LTR 存储成本不会增大，因为备份不是从辅助数据库生成的。 仅当辅助数据库成为主数据库时才会创建备份。 这样可以确保在触发故障转移以及在主数据库转移到次要区域时，不间断地生成 LTR 备份。 

> [!NOTE]
> 在发生导致故障转移的服务中断问题后恢复原始的主数据库时，该数据库将变成新的辅助数据库。 因此，在该数据库再次变成主数据库前，备份生成将不会继续，现有 LTR 策略也不会生效。 

## <a name="configure-long-term-backup-retention"></a>配置长期备份保留

若要了解如何使用 Azure 门户或 PowerShell 配置长期保留，请参阅[管理 Azure SQL 数据库长期备份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="restore-database-from-ltr-backup"></a>从 LTR 备份还原数据库

若要从 LTR 存储还原数据库，可以根据时间戳选择一个特定备份。 数据库可以还原到原始数据库所在的订阅中的任何现有服务器。 若要了解如何使用 Azure 门户或 PowerShell 从 LTR 备份还原数据库，请参阅[管理 Azure SQL 数据库长期备份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="next-steps"></a>后续步骤

数据库备份可保护数据免遭意外损坏或删除，因此数据库备份是任何业务连续性和灾难恢复策略不可或缺的组成部分。 若要了解其他 SQL 数据库业务连续性解决方案，请参阅[业务连续性概述](sql-database-business-continuity.md)。
