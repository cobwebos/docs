---
title: "Azure 门户：管理长期数据库备份保留 | Microsoft Docs"
description: "有关如何使用 Azure 门户在 Azure 恢复服务保管库中配置、管理和还原自动 Azure SQL 数据库备份的长期保留的快速参考"
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
ms.date: 12/22/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: fa4cf56c10f885a2fd4b62e08a369a51d1280ab3
ms.lasthandoff: 03/25/2017


---
# <a name="configure-manage-and-restore-from-long-term-retention-of-database-backups-in-an-azure-recovery-services-vault-using-the-azure-portal"></a>使用 Azure 门户在 Azure 恢复服务保管库中配置、管理和还原数据库备份的长期保留

在本主题中，你可以了解如何使用 Azure 门户在 Azure 恢复服务保管库中配置、管理和还原自动备份的长期保留。 你还可以使用 [PowerShell](sql-database-manage-long-term-backup-retention-powershell.md) 执行这些任务。

有关长期备份保留的详细信息，请参阅[长期备份保留](sql-database-long-term-retention.md)。

> [!TIP]
> 有关教程，请参阅[使用 Azure 门户进行数据保护和恢复的备份和还原指南](sql-database-get-started-backup-recovery-portal.md)。
>

## <a name="configure-long-term-retention-using-the-azure-portal"></a>使用 Azure 门户配置长期保留

1. 打开服务器的“SQL Server”边栏选项卡。

    ![SQL 服务器边栏选项卡](./media/sql-database-get-started/sql-server-blade.png)

2. 单击“长期备份保留”。

   ![长期备份保留链接](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. 在“长期备份保留”边栏选项卡上，查看并接受预览条款（除非已执行此操作或此功能已不处于预览中）。

   ![接受预览条款](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. 若要为数据库配置长期备份保留，请在网格中选择该数据库，然后在工具栏上单击“配置”。

   ![选择要进行长期备份保留的数据库](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. 在“配置”边栏选项卡上，单击“恢复服务保管库”下的“配置所需设置”。

   ![配置保管库链接](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. 在“恢复服务保管库”边栏选项卡上，选择现有保管库（如果有）。 如果找不到订阅的恢复服务保管库，则单击以退出此流程并创建恢复服务保管库。

   ![创建新的保管库链接](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. 在“恢复服务保管库”边栏选项卡上，单击“添加”。

   ![添加新的保管库链接](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. 在“恢复服务保险库”边栏选项卡上，为新的恢复服务保管库提供有效的名称。

   ![新保管库名称](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. 选择订阅和资源组，然后选择保管库的位置。 完成后，单击“创建”。

   ![创建新的保管库](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > 保管库必须位于与 Azure SQL 逻辑服务器相同的区域，并且必须使用与逻辑服务器相同的资源组。
   >

10. 创建新保管库后，执行必要的步骤以返回到“恢复服务保管库”边栏选项卡。

11. 在“恢复服务保管库”边栏选项卡上，单击保管库，然后单击“选择”。

   ![选择现有保管库](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. 在“配置”边栏选项卡上，为新保留策略提供有效的名称，根据需要修改默认保留策略，然后单击“确定”。

   ![定义保留策略](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

13. 在“长期备份保留”边栏选项卡上，单击“保存”，然后单击“确定”，将长期备份保留策略应用于所选数据库。

   ![定义保留策略](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. 单击“保存”以使用此新策略对你配置的 Azure 恢复服务保管库启用长期备份保留。

   ![定义保留策略](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)


## <a name="restore-from-long-term-backup-retention-using-the-azure-portal"></a>使用 Azure 门户从长期备份保留存储中还原

1. 在“Azure 保管库备份”边栏选项卡上，单击要还原的备份，然后单击“选择”。

    ![选择保管库中的备份](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. 在“数据库名称”文本框中，为已还原的数据库提供名称。

    ![新数据库名称](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. 单击“确定”将数据库从保管库中的备份还原到新数据库。

4. 在工具栏上，单击通知图标以查看还原作业的状态。

    ![从保管库还原作业进度](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. 完成还原作业后，打开“SQL 数据库”边栏选项卡以查看新还原的数据库。

    ![保管库中已还原的数据库](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

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
> 有关教程，请参阅[数据保护和恢复的备份和还原入门](sql-database-get-started-backup-recovery-portal.md)

## <a name="next-steps"></a>后续步骤

- 若要使用 PowerShell 管理长期备份保留中的备份，请参阅[使用 PowerShell 管理长期备份保留](sql-database-manage-long-term-backup-retention-powershell.md)
- 若要了解服务生成的自动备份，请参阅[自动备份](sql-database-automated-backups.md)
- 若要了解长期备份保留，请参阅[长期备份保留](sql-database-long-term-retention.md)
- 若要了解如何从备份中还原，请参阅[从备份中还原](sql-database-recovery-using-backups.md)
