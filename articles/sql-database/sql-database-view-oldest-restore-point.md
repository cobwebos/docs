---
title: "从 Azure SQL 数据库的服务生成备份查看最早的还原点 | Microsoft Docs"
description: "有关如何从数据库的服务生成备份查看最早的还原点的快速参考"
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
ms.openlocfilehash: ae35dd15c386bbbf4e77a143d245efcd1f44f238


---
# <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-an-azure-sql-database"></a>从 Azure SQL 数据库的服务生成备份查看最早的还原点

在此操作说明主题中，你会了解如何从 Azure SQL 数据库的服务生成备份查看最早的还原点。

## <a name="view-the-oldest-restore-point-using-the-azure-portal"></a>使用 Azure 门户查看最早的还原点

1. 打开数据库的“SQL 数据库”边栏选项卡。

    ![新示例数据库边栏选项卡](./media/sql-database-get-started/new-sample-db-blade.png)

2. 在工具栏上，单击“还原”。

    ![还原工具栏](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

3. 在“还原”边栏选项卡，查看最早还原点。

    ![最早还原点](./media/sql-database-get-started-backup-recovery/oldest-restore-point.png)

> [!TIP]
> 有关教程，请参阅[数据保护和恢复的备份和还原入门](sql-database-get-started-backup-recovery.md)
>

## <a name="next-steps"></a>后续步骤

- 若要了解服务生成的自动备份，请参阅[自动备份](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD)
- 若要了解长期备份保留，请参阅[长期备份保留](sql-database-long-term-retention.md)
- 若要了解如何从备份中还原，请参阅[从备份中还原](sql-database-recovery-using-backups.md)


<!--HONumber=Dec16_HO2-->


