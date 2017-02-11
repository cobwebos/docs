---
title: "从 Azure 恢复服务保管库中的备份还原数据库 | Microsoft Docs"
description: "有关如何针对 Azure 恢复服务保管库中的备份和这些备份所用的空间恢复数据库的快速参考"
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
ms.openlocfilehash: cdb898ef86517b6eb6eed4b2ae38c4e327e78181


---
# <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>从长期备份保留中的备份还原数据库

在此操作说明主题中，你会了解如何从长期备份保留中的备份还原数据库。

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

> [!TIP]
> 有关教程，请参阅[数据保护和恢复的备份和还原入门](sql-database-get-started-backup-recovery.md)
>

## <a name="next-steps"></a>后续步骤

- 若要在 Azure 恢复服务保管库中配置自动备份的长期保留，请参阅[配置长期备份保留](sql-database-configure-long-term-retention.md)
- 若要查看 Azure 恢复服务保管库中的备份，请参阅[查看长期保留的备份](sql-database-view-backups-in-vault.md)
- 若要了解服务生成的自动备份，请参阅[自动备份](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD)
- 若要了解长期备份保留，请参阅[长期备份保留](sql-database-long-term-retention.md)
- 若要了解如何从备份中还原，请参阅[从备份中还原](sql-database-recovery-using-backups.md)


<!--HONumber=Dec16_HO2-->


