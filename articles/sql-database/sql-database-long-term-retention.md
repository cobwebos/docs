---
title: "将 Azure SQL 数据库备份存储 10 年之久 | Microsoft Docs"
description: "了解 Azure SQL 数据库如何支持将备份存储长达 10 年。"
keywords: 
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: 66fdb8b8-5903-4d3a-802e-af08d204566e
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/22/2016
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: e47c10c2c979622fba254757d3f7b9f19d3240f8
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="storing-azure-sql-database-backups-for-up-to-10-years"></a>将 Azure SQL 数据库备份存储 10 年之久
出于法规要求、符合性或其他商业目的，许多应用程序要求保留 SQL 数据库的[自动备份](sql-database-automated-backups.md)功能所提供过去 7-35 天的自动完整数据库备份。 使用**长期备份保留**功能，可将 Azure SQL 数据库备份存储在 Azure 恢复服务保管库中至多 10 年。 每个保管库可存储至多 1000 个数据库。 可选择保管库中的任何备份，并将其还原为新数据库。

> [!IMPORTANT]
> 长期备份保留期当前处于预览状态，在以下区域中提供：澳大利亚东部、澳大利亚东南部、巴西南部、美国中部、亚洲东部、美国东部、美国东部 2、印度中部、印度南部、日本东部、日本西部、美国中北部、欧洲北部、美国中南部、亚洲东南部、欧洲西部和美国西部。
>

> [!NOTE]
> 24 小时内，每个保管库最多可启用 200 个数据库。 因此，建议对每个服务器使用单独的保管库，将此限制的影响降至最低。 
> 



 
## <a name="how-does-sql-database-long-term-backup-retention-work"></a>SQL 数据库长期备份保留的工作原理是什么？

通过备份的长期备份保留，可将 Azure SQL 数据库服务器与 Azure 恢复服务保管库相关联。 

* 必须在创建 SQL Server 的 Azure 订阅中，在相同地理区域和资源组内创建保管库。 
* 然后为任何数据库配置保留策略。 使用该策略时，会将每周完整数据库备份复制到恢复服务保管库，并在指定的保留期（至多 10 年）内保留此数据。 
* 此后，可从其中任意备份还原到订阅中任意服务器内的新数据库。 由 Azure 存储从现有备份进行复制，不会对现有数据库的性能造成影响。

> [!TIP]
> 有关操作指南，请参阅[从 Azure SQL 数据库长期备份保留进行配置和恢复](sql-database-long-term-backup-retention-configure.md)

## <a name="how-do-i-enable-long-term-backup-retention"></a>如何启用长期备份保留？

为数据库配置长期备份保留：

1. 在与 SQL 数据库服务器相同的区域、订阅和资源组中创建 Azure 恢复服务保管库。 
2. 将服务器注册到保管库
3. 创建 Azure 恢复服务保护策略
4. 向需要长期备份保留的数据库应用保护策略

### <a name="azure-portal"></a>Azure 门户

若要使用 Azure 门户在 Azure 恢复服务保管库中配置、管理和从自动备份的长期备份保留进行还原，请单击“长期备份保留”，选择数据库，然后单击“配置”。 

   ![选择要进行长期备份保留的数据库](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

若要使用 PowerShell 在 Azure 恢复服务保管库中配置、管理和从自动备份的长期备份保留进行还原，请参阅[从 Azure SQL 数据库长期备份保留进行配置和还原](sql-database-long-term-backup-retention-configure.md)。

## <a name="how-do-i-restore-a-database-stored-with-the-long-term-backup-retention-feature"></a>如何还原通过长期备份保留功能存储的数据库？

从长期备份保留备份恢复：

1. 列出存储备份的保管库
2. 列出映射到逻辑服务器的容器
3. 列出映射到数据库的保管库中的数据源
4. 列出可还原的恢复点
5. 从恢复点还原到订阅中的目标服务器

若要使用 Azure 门户在 Azure 恢复服务保管库中配置、管理和从自动备份的长期备份保留进行还原，请参阅[使用 Azure 门户管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。 若要使用 PowerShell 在 Azure 恢复服务保管库中配置、管理和从自动备份的长期备份保留进行还原，请参阅[使用 PowerShell 管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="how-much-does-long-term-backup-retention-cost"></a>长期备份保留的价格是多少？

Azure SQL 数据库的长期备份保留按 [Azure 备份服务定价费率](https://azure.microsoft.com/pricing/details/backup/)收费。

将 Azure SQL 数据库服务器注册到保管库后，将对保管库中存储的每周备份所使用的存储空间总量进行收费。

## <a name="view-available-backups-stored-in-long-term-backup-retention"></a>查看长期备份保留中存储的可用备份

若要使用 Azure 门户在 Azure 恢复服务保管库中配置、管理和从自动备份的长期备份保留进行还原，请参阅[使用 Azure 门户管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。 若要使用 PowerShell 在 Azure 恢复服务保管库中配置、管理和从自动备份的长期备份保留进行还原，请参阅[使用 PowerShell 管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="disabling-long-term-retention"></a>禁用长期保留

恢复服务将根据提供的保留策略自动清理备份。 

* 若要停止向保管库发送特定数据库的备份，请删除该数据库的保留策略。
  
    ```
    Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
    ```

> [!NOTE]
> 保管库中已有的备份不会受到影响。 恢复服务将在其保留期到期时将其自动删除。

## <a name="long-term-backup-retention-faq"></a>长期备份保留常见问题：

1. 问：可否手动删除保管库中的特定备份？

    答：目前不可以。 保管库会在保留期到期后自动清理备份。
2. 问：可否将服务器注册为将备份存储到多个存储库？

    答：不可以，目前，在同一时间只能将备份存储到一个保管库。
3. 问：保管库和服务器可否位于不同订阅中？

    答：不可以，目前，保管库和服务器必须位于相同的订阅和资源组中。
4. 问：可否使用在服务器所在区域之外创建的保管库？

    答：不可以，保管库和服务器必须位于同一区域，以便尽量减少复制时间、节省流量费。
5. 问：一个保管库中可存储多少个数据库？

    答：目前，每个保管库最多支持 1000 个数据库。 
6. 问： 每个订阅可创建多少保管库？

    A. 每个订阅最多可创建 25 个保管库。
7. 问： 每个保管库每天可配置多少个数据库？

    A. 每个保管库每天只能设置 200 个数据库。
8. 问：长期备份保留可否用于弹性池？

    答：是的。 可为池中任意数据库配置保留策略。
9. 问：可否选择创建备份的时间？

    答：不可以，由 SQL 数据库控制备份计划，以尽量减少对数据库性能的影响。
10. 问：我为数据库启用了 TDE。 能否将 TDE 用于保管库？ 

    A. 可以，支持 TDE。 即使原始数据库不复存在，也可从保管库还原该数据库。
11. 问： 如果订阅被挂起，保管库中的备份会出现什么情况？ 

    A. 如果订阅被挂起，将保留现有数据库和备份。 新的备份不会被复制到保管库。 重新激活订阅后，该服务会继续将备份复制到保管库。 使用订阅挂起前复制到保管库的备份的还原操作可访问保管库。 
12. 问：可否访问 SQL 数据库备份文件，以便下载/还原到 SQL Server？

    答：目前不可以。
13. 问：SQL 保留策略中可否存在多个计划（日计划、周计划、月计划、年计划）？

    答：不可以，目前这仅适用于虚拟机备份。
14. 问： 如果某个数据库是活动异地复制辅助数据库，而我们在该数据库上设置了长期备份保留，则会发生什么情况？

    答：目前我们不对副本进行备份，因此对于辅助数据库上的长期备份保留，不提供任何选项。 但是，客户必须在活动异地复制辅助数据库上设置长期备份保留，原因如下：
    - 发生故障转移时，该辅助数据库会成为主数据库，我们会进行完整的备份，然后将这个完整的备份上传到保管库。
    - 在辅助数据库上设置长期备份保留对客户来说没有额外的费用。



## <a name="next-steps"></a>后续步骤
数据库备份是任何业务连续性和灾难恢复策略的基本组成部分，因为数据库备份可以保护数据免遭意外损坏或删除。 若要了解其他 Azure SQL 数据库业务连续性解决方案，请参阅[业务连续性概述](sql-database-business-continuity.md)。


