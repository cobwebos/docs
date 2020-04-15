---
title: 长期备份保留
description: 了解 Azure SQL 数据库如何通过长期保留策略支持存储完整数据库备份长达 10 年。
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
ms.openlocfilehash: d015eea21bcfa499d6751e024a882a7316b7f1a5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380764"
---
# <a name="azure-sql-database-long-term-retention"></a>Azure SQL 数据库长期保留

出于法规要求、符合性或其他商业目的，许多应用程序要求保留 Azure SQL 数据库的[自动备份](sql-database-automated-backups.md)功能提供的过去 7-35 天的数据库备份。 通过使用长期保留 （LTR） 功能，可以将指定的 SQL 数据库完整备份存储在 Azure Blob 存储中，并具有读取访问异地冗余存储长达 10 年。 然后，可以将任何备份还原为新数据库。 有关 Azure 存储冗余的详细信息，请参阅[Azure 存储冗余](../storage/common/storage-redundancy.md)。 

可以为单个数据库和池数据库启用长时间保留，并且对于 Azure SQL 数据库托管实例，在有限的公共预览中。 

> [!NOTE]
> 可以使用 SQL 代理作业来安排[仅复制数据库备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server)作为超过 35 天的 LTR 的替代方案。


## <a name="how-sql-database-long-term-retention-works"></a>SQL 数据库长期保留的工作原理

长期备份保留 (LTR) 利用[自动创建](sql-database-automated-backups.md)的完整数据库备份来启用时间点还原 (PITR)。 如果配置了 LTR 策略，这些备份将复制到不同的 Blob 进行长期存储。 副本是一个后台作业，对数据库工作负荷没有性能影响。 每个 SQL 数据库的 LTR 策略还可以指定创建 LTR 备份的频率。

要启用 LTR，可以使用四个参数的组合来定义策略：每周备份保留 （W）、每月备份保留 （M）、年度备份保留 （Y） 和一年中的周 （WeekOfYear）。 如果指定 W，则每周会将一个备份复制到长期存储。 如果指定 M，则每个月的第一个备份将复制到长期存储。 如果指定 Y，则会在 WeekOfYear 指定的周将一个备份复制到长期存储。 如果指定的 WeekOfYear 在配置策略时已过去，则第一个 LTR 备份将在下一年创建。 根据创建 LTR 备份时配置的策略参数，每个备份都将保存在长期存储中。

> [!NOTE]
> 对 LTR 策略的任何更改仅适用于将来的备份。 例如，如果修改每周备份保留 （W）、每月备份保留 （M） 或年度备份保留 （Y），则新的保留设置将仅适用于新备份。 不会修改现有备份的保留。 如果您打算在保留期到期之前删除旧的 LTR 备份，则需要[手动删除备份](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups)。
> 

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

## <a name="managed-instance-support"></a>托管实例支持

将长期备份保留与 Azure SQL 数据库托管实例一起使用具有以下限制：

- **有限的公共预览**- 此预览仅适用于 EA 和 CSP 订阅，且可用性有限。  
- [**仅限 PowerShell**](sql-database-managed-instance-long-term-backup-retention-configure.md) - 当前没有 Azure 门户支持。 必须使用 PowerShell 启用 LTR。 

要请求注册，请在支持主题 **"备份、还原和业务连续性/长期备份保留**"下创建[Azure 支持票证](https://azure.microsoft.com/support/create-ticket/)。


## <a name="configure-long-term-backup-retention"></a>配置长期备份保留

若要了解如何使用 Azure 门户或 PowerShell 配置长期保留，请参阅[管理 Azure SQL 数据库长期备份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="restore-database-from-ltr-backup"></a>从 LTR 备份还原数据库

若要从 LTR 存储还原数据库，可以根据时间戳选择一个特定备份。 数据库可以还原到原始数据库所在的订阅中的任何现有服务器。 要了解如何使用 Azure 门户或 PowerShell 从 LTR 备份还原数据库，请参阅[管理 Azure SQL 数据库的长期备份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="next-steps"></a>后续步骤

数据库备份可保护数据免遭意外损坏或删除，因此数据库备份是任何业务连续性和灾难恢复策略不可或缺的组成部分。 若要了解其他 SQL 数据库业务连续性解决方案，请参阅[业务连续性概述](sql-database-business-continuity.md)。
