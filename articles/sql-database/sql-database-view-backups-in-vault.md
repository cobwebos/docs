---
title: "查看 Azure 恢复服务保管库中的备份 | Microsoft Docs"
description: "有关如何查看 Azure 恢复服务保管库中的备份和这些备份所用的空间的快速参考"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 12/07/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6d729f89322b67fe75d2c0ad001e70b42ccedb7e
ms.openlocfilehash: 723be587128c131670c9905572d808c176362fb8


---
# <a name="view-information-about-your-database-backups-in-long-term-backup-retention"></a>在长期备份保留中查看有关数据库备份的信息

在此操作说明主题中，你会了解如何在长期备份保留中查看有关数据库备份的信息。

## <a name="view-long-term-backup-retention-information-using-the-azure-portal"></a>使用 Azure 门户查看长期备份保留信息 

1. 打开 Azure 恢复服务保管库的边栏选项卡（转到“所有资源”并从订阅的资源列表中选择它）以查看数据库备份在保管库中使用的存储量。

   ![查看具有备份的恢复服务保管库](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. 打开数据库的“SQL 数据库”边栏选项卡。

    ![新示例数据库边栏选项卡](./media/sql-database-get-started/new-sample-db-blade.png)

3. 在工具栏上，单击“还原”。

    ![还原工具栏](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. 在“还原”边栏选项卡上，单击“长期”。

5. 在 Azure 保管库备份下，单击“选择备份”以查看长期备份保留中的可用数据库备份。

    ![保管库中的备份](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

> [!TIP]
> 有关教程，请参阅[数据保护和恢复的备份和还原入门](sql-database-get-started-backup-recovery.md)
>

## <a name="next-steps"></a>后续步骤

- 若要在 Azure 恢复服务保管库中配置自动备份的长期保留，请参阅[](sql-database-configure-long-term-retention.md)
- 若要从长期备份保留中的备份还原数据库，请参阅[从长期保留恢复](sql-database-restore-from-long-term-retention.md)
- 若要了解服务生成的自动备份，请参阅[自动备份](sql-database-automated-backups.md)
- 若要了解长期备份保留，请参阅[长期备份保留](sql-database-long-term-retention.md)
- 若要了解如何从备份中还原，请参阅[从备份中还原](sql-database-recovery-using-backups.md)


<!--HONumber=Dec16_HO2-->


