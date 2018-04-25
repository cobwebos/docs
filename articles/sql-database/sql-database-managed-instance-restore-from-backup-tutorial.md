---
title: 将备份还原到 Azure SQL 数据库托管实例 | Microsoft Docs
description: 使用 SSMS 将数据库备份还原到 Azure SQL 数据库托管实例。
keywords: sql 数据库教程, sql 数据库托管实例, 还原备份
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 04/10/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: ff605b7512a27f81b111560f5d151010dbb62273
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>将数据库备份还原到 Azure SQL 数据库托管实例

本教程演示如何使用 Wide World Importers - 标准备份文件将存储在 Azure blob 存储中的数据库备份还原到托管实例。 此方法需要一些停机时间。 有关使用 Azure 数据库迁移服务 (DMS) 进行迁移的教程，请参阅[使用 DMS 进行托管实例迁移](../dms/tutorial-sql-server-to-managed-instance.md)。 有关各种迁移方法的讨论，请参阅[将 SQL Server 实例迁移到 Azure SQL 数据库托管实例](sql-database-managed-instance-migrate.md)。

> [!div class="checklist"]
> * 下载 Wide World Importers - 标准备份文件
> * 创建 Azure 存储帐户并上传备份文件
> * 从备份文件还原 Wide World Importers 数据库

## <a name="prerequisites"></a>先决条件

本教程使用此教程中创建的资源作为其起点：[创建 Azure SQL 数据库托管实例](sql-database-managed-instance-create-tutorial-portal.md)。

## <a name="download-the-wide-world-importers---standard-backup-file"></a>下载 Wide World Importers - 标准备份文件

通过以下步骤下载 Wide World Importers - 标准备份文件。

在 Internet Explorer 的 URL 地址框中输入 https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak，然后在出现提示时，单击“保存”将此文件保存到“下载”文件夹。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/#create/Microsoft.SQLManagedInstance)。

## <a name="create-azure-storage-account-and-upload-backup-file"></a>创建 Azure 存储帐户并上传备份文件

1. 在 Azure 门户的左上角单击“创建资源”。
2. 找到“存储”，然后单击“存储帐户”以打开存储帐户窗体。

   ![存储帐户](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. 根据下表中的说明，在存储帐户窗体中填充请求的信息：

   | 设置| 建议的值 | 说明 |
   | ------ | --------------- | ----------- |
   |**Name**|任何有效的名称|请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)，了解什么是有效的名称。|
   |**部署模型**|资源模型||
   |**帐户种类**|Blob 存储||
   |**性能**|标准或高级|磁驱动器或 SSD|
   |**复制**|本地冗余存储||
   |**访问层（默认）|冷或热||
   |**需要安全传输**|已禁用||
   |**订阅**|你的订阅|有关订阅的详细信息，请参阅[订阅](https://account.windowsazure.com/Subscriptions)。|
   |**资源组**|之前创建的资源组|| 
   |**位置**|以前选择的位置||
   |**虚拟网络**|已禁用||

4. 单击“创建”。

   ![存储帐户详细信息](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. 存储帐户部署成功后，打开新的存储帐户。
6. 在“设置”下单击“共享访问签名”，打开共享访问签名 (SAS) 窗体。

   ![sas 窗体](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. 在 SAS 窗体上，根据需要修改默认值。 请注意，到期日期/时间默认情况下仅为 8 小时。
8. 单击“生成 SAS”。

   ![完成的 sas 窗体](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. 复制并保存“SAS 令牌”和“Blob 服务器 SAS URL”。
10. 在“设置”下，单击“容器”。

    ![containers](./media/sql-database-managed-instance-tutorial/containers.png)

11. 单击“+ 容器”，创建一个容器来存储备份文件。
12. 根据下表中的说明，在容器窗体中填充请求的信息：

    | 设置| 建议的值 | 说明 |
   | ------ | --------------- | ----------- |
   |**Name**|任何有效的名称|请参阅[命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)，了解什么是有效的名称。|
   |**公共访问级别**|专用（不允许匿名访问）||

    ![容器详细信息](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. 单击“确定”。
14. 创建容器后，打开容器。

    ![container](./media/sql-database-managed-instance-tutorial/container.png)

15. 单击“容器属性”，然后将 URL 复制到容器。

    ![容器 URL](./media/sql-database-managed-instance-tutorial/container-url.png)

16. 单击“上传”以打开“上传 Blob”窗体。

    ![upload](./media/sql-database-managed-instance-tutorial/upload.png)

17. 浏览到下载文件夹，并选择 **WideWorldIimporters-Standard.bak** 文件。

    ![upload](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. 单击“上传” 。
19. 在上传完成前，请勿继续操作。

    ![上传完成](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>从备份文件还原 Wide World Importers 数据库

使用 SSMS 时，请执行以下步骤，从备份文件将 Wide World Importers 数据库还原到托管实例。

1. 在 SSMS 中打开新的查询窗口。
2. 使用以下脚本创建 SAS 凭据 - 根据指示提供存储帐户容器的 URL 和 SAS 密钥。

   ```sql
   CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
      WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
      , SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![credential](./media/sql-database-managed-instance-tutorial/credential.png)

3. 使用以下脚本查看 SAS 凭据和备份有效性 - 提供包含备份文件的容器的 URL：

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![文件列表](./media/sql-database-managed-instance-tutorial/file-list.png)

4. 使用以下脚本从备份文件还原 Adventure Works 2012 数据库 - 提供包含备份文件的容器的 URL：

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![正在执行还原](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. 若要跟踪还原状态，请在新的查询会话中运行以下查询：

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![还原的完成百分比](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. 还原完成后，请在对象资源管理器中查看它。 

    ![还原已完成](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>后续步骤

本教程介绍如何使用 Wide World Importers - 标准备份文件将存储在 Azure blob 存储中的数据库备份还原到托管实例。 你已了解如何： 

> [!div class="checklist"]
> * 下载 Wide World Importers - 标准备份文件
> * 创建 Azure 存储帐户并上传备份文件
> * 从备份文件还原 Wide World Importers 数据库

转到下一教程，了解如何使用 DMS 将 SQL Server 迁移到 Azure SQL 数据库托管实例。

> [!div class="nextstepaction"]
>[使用 DMS 将 SQL Server 迁移到 Azure SQL 数据库托管实例](../dms/tutorial-sql-server-to-managed-instance.md)