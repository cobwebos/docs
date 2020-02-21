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
ms.openlocfilehash: 15a2d58d2fc14c370c41d5454d62c74a5b66ad42
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77499979"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>将 Azure SQL 数据库备份存储 10 年之久

出于法规要求、符合性或其他商业目的，许多应用程序要求保留 Azure SQL 数据库的[自动备份](sql-database-automated-backups.md)功能提供的过去 7-35 天的数据库备份。 通过使用长期保留（LTR）功能，可以在 Azure Blob 存储中将指定的 SQL 数据库完整备份存储在具有读取访问权限异地冗余存储的情况下，长达10年。 然后，可以将任何备份还原为新数据库。 有关 Azure 存储冗余的详细信息，请参阅[Azure 存储冗余](../storage/common/storage-redundancy.md)。

> [!NOTE]
> 可以为单一数据库和共用数据库启用 LTR。 对于托管实例中的实例数据库，它还不可用。 可以使用 SQL 代理作业来安排[仅复制数据库备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)作为超过 35 天的 LTR 的替代方案。
> 

## <a name="how-sql-database-long-term-retention-works"></a>SQL 数据库长期保留的工作原理

长期备份保留 (LTR) 利用[自动创建](sql-database-automated-backups.md)的完整数据库备份来启用时间点还原 (PITR)。 如果配置了 LTR 策略，则会将这些备份复制到用于长期存储的不同 blob。 副本是对数据库工作负荷没有性能影响的后台作业。 每个 SQL 数据库的 LTR 策略还可以指定创建 LTR 备份的频率。

若要启用 LTR，可以使用四个参数的组合定义策略：每周备份保留（W）、每月备份保留（M）、每年备份保留（Y）和每年的某一周（WeekOfYear）。 如果指定 W，则每周会将一个备份复制到长期存储。 如果指定 M，则每个月的第一个备份将复制到长期存储。 如果指定 Y，则会在 WeekOfYear 指定的周将一个备份复制到长期存储。 如果在配置策略时指定的 WeekOfYear 为过去的时间，则将在下一年中创建第一个 LTR 备份。 每个备份都将按照创建 LTR 备份时配置的策略参数保留在长期存储中。

> [!NOTE]
> 对 LTR 策略进行的任何更改仅适用于将来的备份。 例如，如果每周备份保留（W）、每月备份保留（M）或每年备份保留期（Y）被修改，则新的保持期设置将仅适用于新的备份。 不会修改现有备份的保留期。 如果你的目的是在保留期到期之前删除旧的 LTR 备份，你将需要[手动删除备份](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups)。
> 

LTR 策略的示例：

-  W=0、M=0、Y=5、WeekOfYear=3

   每年的第三次完整备份将保留5年。
   
- W=0、M=3、Y=0

   每个月的第一个完整备份将保留3个月。

- W=12、M=0、Y=0

   每个每周完整备份将保留 12 周。

- W=6、M=12、Y=10、WeekOfYear=16

   每个每周完整备份将保留6周。 每月的第一个完整备份例外，该备份将保留 12 个月。 每年的第 16 周创建的完整备份例外，该备份将保留 10 年。 

下表说明了以下策略的长期备份的节奏和到期：

W=12 周（84 天）、M=12 个月（365 天）、Y=10 年（3650 天）、WeekOfYear=15（4 月 15 日后的周）

   ![ltr 示例](./media/sql-database-long-term-retention/ltr-example.png)



如果修改上述策略并设置 W = 0 （无每周备份），则备份副本的步调将按突出显示的日期更改为上表中所示。 保留这些备份所需的存储量将相应减少。 

> [!IMPORTANT]
> 单个 LTR 备份的时间由 Azure SQL 数据库控制。 无法手动创建 LTR 备份或控制创建备份的时间。 配置 LTR 策略后，最多可能需要7天才能在可用备份列表中显示第一个 LTR 备份。  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>异地复制和长期备份保留

如果使用活动异地复制或故障转移组作为业务连续性解决方案，则应准备最终故障转移，并在异地辅助数据库上配置相同的 LTR 策略。 如果备份不是从辅助副本生成的，则 LTR 存储成本将不会增加。 仅当辅助数据库成为主数据库时才会创建备份。 它可确保在触发故障转移并将主节点移动到次要区域时，不中断的 LTR 备份生成。 

> [!NOTE]
> 如果原始主数据库从导致故障转移的中断中恢复，则会成为新的辅助数据库。 因此，在该数据库再次变成主数据库前，备份生成将不会继续，现有 LTR 策略也不会生效。 

## <a name="configure-long-term-backup-retention"></a>配置长期备份保留

若要了解如何使用 Azure 门户或 PowerShell 配置长期保留，请参阅[管理 AZURE SQL 数据库长期备份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="restore-database-from-ltr-backup"></a>从 LTR 备份还原数据库

若要从 LTR 存储还原数据库，可以根据时间戳选择一个特定备份。 数据库可以还原到原始数据库所在的订阅中的任何现有服务器。 若要了解如何使用 Azure 门户或 PowerShell 从 LTR 备份还原数据库，请参阅[管理 AZURE SQL 数据库长期备份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="next-steps"></a>后续步骤

数据库备份可保护数据免遭意外损坏或删除，因此数据库备份是任何业务连续性和灾难恢复策略不可或缺的组成部分。 若要了解其他 SQL 数据库业务连续性解决方案，请参阅[业务连续性概述](sql-database-business-continuity.md)。
