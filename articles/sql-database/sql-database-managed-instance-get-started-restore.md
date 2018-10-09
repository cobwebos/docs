---
title: 将备份还原到 Azure SQL 数据库托管实例 | Microsoft Docs
description: 使用 SSMS 将数据库备份还原到 Azure SQL 数据库托管实例。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 6cad73c8b8fa6a2fa95a6ea0c1fdb5d4114ffd41
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47180093"
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>将数据库备份还原到 Azure SQL 数据库托管实例

本快速入门演示了如何使用 Wide World Importers - 标准备份文件将存储在 Azure blob 存储中的数据库备份还原到托管实例。 此方法需要一些停机时间。 

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

有关使用 Azure 数据库迁移服务 (DMS) 进行迁移的教程，请参阅[使用 DMS 进行托管实例迁移](../dms/tutorial-sql-server-to-managed-instance.md)。 有关各种迁移方法的讨论，请参阅[将 SQL Server 实例迁移到 Azure SQL 数据库托管实例](sql-database-managed-instance-migrate.md)。

## <a name="prerequisites"></a>先决条件

本快速入门：
- 使用以下快速入门中创建的资源作为起点：[创建托管实例](sql-database-managed-instance-get-started.md)。
- 需要本地客户端计算机上的最新版本的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
- 需要使用 SQL Server Management Studio 连接到托管实例。 有关连接选项，请参阅以下快速入门：
  - [从 Azure VM 连接到 Azure SQL 数据库托管实例](sql-database-managed-instance-configure-vm.md)
  - [使用点到站点连接从本地连接到 Azure SQL 数据库托管实例](sql-database-managed-instance-configure-p2s.md)。
- 使用包含 Wide World Importers - 标准备份文件（从 https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak) 下载的）的预配置 Azure blob 存储帐户。

> [!NOTE]
> 有关使用 Azure blob 存储和共享访问签名 (SAS) 备份和还原 SQL Server 数据库的详细信息，请参阅[将 SQL Server 备份到 URL](sql-database-managed-instance-get-started-restore.md)。

## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>从备份文件还原 Wide World Importers 数据库

使用 SSMS 时，请执行以下步骤，从备份文件将 Wide World Importers 数据库还原到托管实例。

1. 打开 SQL Server Management Studio (SSMS) 并连接到你的托管实例。
2. 在 SSMS 中打开新的查询窗口。
3. 使用以下脚本使用预配置的存储帐户和 SAS 密钥在托管实例中创建凭据。

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases] 
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D' 
   ```

    ![创建凭据](./media/sql-database-managed-instance-get-started-restore/credential.png)

    > [!NOTE]
    > 始终从生成的 SAS 密钥中删除前导 **?** 。
  
3. 使用以下脚本检查 SAS 凭据和备份有效性 - 提供包含备份文件的容器的 URL：

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![文件列表](./media/sql-database-managed-instance-get-started-restore/file-list.png)

4. 使用以下脚本从备份文件还原 Wide World Importers 数据库 - 提供包含备份文件的容器的 URL：

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![还原](./media/sql-database-managed-instance-get-started-restore/restore.png)

5. 若要跟踪还原状态，请在新的查询会话中运行以下查询：

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

6. 还原完成后，请在对象资源管理器中查看它。 

## <a name="next-steps"></a>后续步骤

- 若要对到 URL 的备份进行故障排除，请参阅[到 URL 的 SQL Server 备份最佳做法和故障排除](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)。
- 如需应用程序的连接选项的概述，请参阅[将应用程序连接到托管实例](sql-database-managed-instance-connect-app.md)。
- 若要使用你偏好的工具或语言之一进行查询，请参阅[连接和查询](sql-database-connect-query.md)。
