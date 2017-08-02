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
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: 25e651203f804fbf32d632b5f83145a3f3f72a7f
ms.contentlocale: zh-cn
ms.lasthandoff: 07/14/2017

---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>将 Azure SQL 数据库备份存储 10 年之久
出于法规要求、符合性或其他商业目的，许多应用程序要求保留 Azure SQL 数据库的[自动备份](sql-database-automated-backups.md)功能提供的过去 7-35 天的数据库备份。 使用长期备份保留功能，可将 SQL 数据库备份存储在 Azure 恢复服务保管库中长达 10 年。 每个保管库最多可存储 1000 个数据库。 然后可选择保管库中的任何备份，并将其还原为新数据库。

> [!IMPORTANT]
> 长期备份保留期当前处于预览状态，在以下区域中提供：澳大利亚东部、澳大利亚东南部、巴西南部、美国中部、东亚、美国东部、美国东部 2、印度中部、印度南部、日本东部、日本西部、美国中北部、北欧、美国中南部、东南亚、西欧和美国西部。
>

> [!NOTE]
> 24 小时内，每个保管库最多可启用 200 个数据库。 建议对每个服务器使用单独的保管库，将此限制的影响降至最低。 
> 

## <a name="how-sql-database-long-term-backup-retention-works"></a>SQL 数据库长期备份保留的工作原理

通过长期备份保留，可将 SQL 数据库服务器与 Azure 恢复服务保管库相关联。 

* 必须在创建 SQL Server 的 Azure 订阅中，在相同地理区域和资源组内创建保管库。 
* 然后为任何数据库配置保留策略。 使用该策略时，会将每周完整数据库备份复制到恢复服务保管库，并在指定的保留期（至多 10 年）内保留此数据。 
* 此后，可从任意这些备份中将数据库还原到订阅内任意服务器中的新数据库。 Azure 存储从现有备份创建副本，且副本不会对现有数据库的性能造成影响。

> [!TIP]
> 有关操作指南，请参阅[从 Azure SQL 数据库长期备份保留进行配置和恢复](sql-database-long-term-backup-retention-configure.md)。

## <a name="enable-long-term-backup-retention"></a>启用长期备份保留

为数据库配置长期备份保留：

1. 在与 SQL 数据库服务器相同的区域、订阅和资源组中创建 Azure 恢复服务保管库。 
2. 将服务器注册到保管库。
3. 创建 Azure 恢复服务保护策略。
4. 向需长期备份保留的数据库应用保护策略。

若要从 Azure 恢复服务保管库中自动备份的长期备份保留配置、管理和还原数据库，请执行以下任一操作：

* 使用 Azure 门户：单击“长期备份保留”，选择一个数据库，然后单击“配置”。 

   ![选择一个数据库进行长期备份保留](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

* 使用 PowerShell：请转到[从 Azure SQL 数据库长期备份保留进行配置和恢复](sql-database-long-term-backup-retention-configure.md)。

## <a name="restore-a-database-thats-stored-with-the-long-term-backup-retention-feature"></a>恢复使用长期备份保留功能存储的数据库

从长期备份保留备份恢复：

1. 列出存储备份的保管库。
2. 列出映射到逻辑服务器的容器。
3. 列出保管库中映射到数据库的数据源。
4. 列出可还原的恢复点。
5. 将数据库从恢复点还原到订阅中的目标服务器。

若要从 Azure 恢复服务保管库中自动备份的长期备份保留配置、管理和还原数据库，请执行以下任一操作：

* 使用 Azure 门户：请转到[使用 Azure 门户管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。 

* 使用 PowerShell：请转到[使用 PowerShell 管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="get-pricing-for-long-term-backup-retention"></a>获取长期备份保留的定价

SQL 数据库的长期备份保留按 [Azure 备份服务定价费率](https://azure.microsoft.com/pricing/details/backup/)收费。

将 SQL 数据库服务器注册到保管库后，将对保管库中存储的每周备份所使用的存储总量进行收费。

## <a name="view-available-backups-that-are-stored-in-long-term-backup-retention"></a>查看长期备份保留中存储的可用备份

若要使用 Azure 门户从 Azure 恢复服务保管库中自动备份的长期备份保留配置、管理和还原数据库，请执行一下任一操作：

* 使用 Azure 门户：请转到[使用 Azure 门户管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。 

* 使用 PowerShell：请转到[使用 PowerShell 管理长期备份保留](sql-database-long-term-backup-retention-configure.md)。

## <a name="disable-long-term-retention"></a>禁用长期保留

恢复服务将根据提供的保留策略自动清理备份。 

若要停止向保管库发送特定数据库的备份，请删除该数据库的保留策略。
  
```
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName 'RG1' -ServerName 'Server1' -DatabaseName 'DB1' -State 'Disabled' -ResourceId $policy.Id
```

> [!NOTE]
> 不会影响保管库中已有备份。 恢复服务将在其保留期到期时将其自动删除。

## <a name="long-term-backup-retention-faq"></a>长期备份保留常见问题解答

可否手动删除保管库中的特定备份？

目前不可以。 保管库会在保留期到期后自动清理备份。

可否将服务器注册为存储备份到多个存储库？

不可以，目前一次只能将备份存储到一个保管库。

保管库和服务器可否位于不同订阅中？

不可以，目前保管库和服务器必须位于相同的订阅和资源组中。

可否使用在服务器区域外创建的保管库？

不可以，保管库和服务器必须位于同一区域，以便尽量减少复制时间、节省流量费。

问：一个保管库中可存储多少个数据库？

目前每个保管库最多支持 1000 个数据库。 

每个订阅可创建多少保管库？

每个订阅最多可创建 25 个保管库。

每个保管库每天可配置多少个数据库？

每个保管库每天最多只能设置 200 个数据库。

长期备份保留可否用于弹性池？

是的。 可为池中任意数据库配置保留策略。

可否选择创建备份的时间？

不可以，SQL 数据库控制备份计划，以尽量减少对数据库性能的影响。

**我为数据库启用了透明数据加密。能否将其用于保管库？** 

可以，保管库支持透明数据加密。 即使原始数据库不复存在，也可从保管库还原该数据库。

如果订阅被挂起，保管库中的备份会出现什么情况？ 

如果订阅被挂起，将保留现有数据库和备份。 新的备份不会复制到保管库。 重新激活订阅后，该服务会继续将备份复制到保管库。 使用订阅挂起前复制到保管库的备份，还原操作可访问保管库。 

可否访问 SQL 数据库备份文件，以便下载文件或将其还原到 SQL Server？

目前不可以。

SQL 保留策略中可否存在多个计划（日计划、周计划、月计划、年计划）？

不可以，目前多个计划仅适用于虚拟机备份。

如果某个数据库是活动异地复制辅助数据库，而我们在该数据库上设置了长期备份保留，会发生什么情况？

因为我们不对副本进行备份，因此目前没有针对辅助数据库上的长期备份保留的选项。 但是，对用户而言，在活动异地复制辅助数据库上设置长期备份保留是重要的，原因如下：
* 发生故障转移时，该辅助数据库会成为主数据库，我们会进行完整备份，随后会将这个完整的备份上传到保管库。
* 在辅助数据库上设置长期备份保留对客户来说没有额外的费用。

## <a name="next-steps"></a>后续步骤
数据库备份可保护数据免遭意外损坏或删除，因此数据库备份是任何业务连续性和灾难恢复策略不可或缺的组成部分。 若要了解其他 SQL 数据库业务连续性解决方案，请参阅[业务连续性概述](sql-database-business-continuity.md)。

