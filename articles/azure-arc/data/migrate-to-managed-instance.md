---
title: 将数据库从 SQL Server 迁移到启用了 Azure Arc 的 SQL 托管实例
description: 将数据库从 SQL Server 迁移到启用了 Azure Arc 的 SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 86563b0a44bade2cedaf76af3c247821756111fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934521"
---
# <a name="migrate-sql-server-to-azure-arc-enabled-sql-managed-instance"></a>将： SQL Server 迁移到启用了 Azure Arc 的 SQL 托管实例

此方案将引导你完成将数据库从 SQL Server 实例迁移到 Azure 中的 Azure SQL 托管实例（通过两种不同的备份和还原方法）的步骤。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="use-azure-blob-storage"></a>使用 Azure blob 存储 

使用 Azure blob 存储迁移到启用了 Azure Arc 的 SQL 托管实例。

此方法使用 Azure Blob 存储作为临时存储位置，你可以备份到该位置，然后从还原。

### <a name="prerequisites"></a>必备条件

- [安装 Azure Data Studio](install-client-tools.md)
- [安装 Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)
- Azure 订阅

### <a name="step-1-provision-azure-blob-storage"></a>步骤1：预配 Azure blob 存储

1. 按照[创建 Azure Blob 存储帐户](../../storage/blobs/storage-blob-create-account-block-blob.md?tabs=azure-portal)中所述的步骤进行操作
1. 启动 Azure 存储资源管理器
1. [登录到 Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) 以访问在上一步中创建的 blob 存储
1. 右键单击 blob 存储帐户，并选择 " **创建 Blob 容器** "，以创建将存储备份文件的新容器

### <a name="step-2-get-storage-blob-credentials"></a>步骤2：获取存储 blob 凭据

1. 在 Azure 存储资源管理器中，右键单击刚创建的 blob 容器，然后选择 "**获取共享访问签名**"

1. 选择 "**读取**"、"**写入**" 和 "**列表**"

1. 选择“创建”

   记下此屏幕中的 URI 和查询字符串。 后面的步骤需要用到这些项。 单击 " **复制** " 按钮，将其保存到记事本/OneNote 等。

1. 关闭 " **共享访问签名** " 窗口。

### <a name="step-3-backup-database-file-to-azure-blob-storage"></a>步骤3：将数据库文件备份到 Azure Blob 存储

在此步骤中，我们将连接到源 SQL Server 并创建要迁移到 SQL 托管实例-Azure Arc 的数据库的备份文件。

1. 启动 Azure Data Studio
1. 连接到包含要迁移到 SQL 托管实例的数据库的 SQL Server 实例-Azure Arc
1. 右键单击该数据库并选择 "**新建查询**"
1. `<...>`使用在前面步骤中使用共享访问签名中的信息替换所指示的占位符的以下格式准备查询。  替换值后，运行查询。

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
      SECRET = '<SAS_TOKEN>';  
   ```

1. 同样，按如下所示准备 **备份数据库** 命令，以便将备份文件创建到 blob 容器。  替换值后，运行查询。

   ```sql
   BACKUP DATABASE <database name> TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>'
   ```

1. 打开 Azure 存储资源管理器并验证在上一步中创建的备份文件在 Blob 容器中是否可见

### <a name="step-4-restore-the-database-from-azure-blob-storage-to-sql-managed-instance---azure-arc"></a>步骤4：将数据库从 Azure blob 存储还原到 SQL 托管实例-Azure Arc

1. 在 Azure Data Studio 中，登录并连接到 SQL 托管实例-Azure Arc。
1. 展开 " **系统数据库**"，右键单击 " **master** 数据库"，然后选择 " **新建查询**"。
1. 在查询编辑器窗口中，准备并运行上一步中的相同查询以创建凭据。

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
     SECRET = '<SAS_TOKEN>';  
   ```

1. 准备并运行以下命令，以验证备份文件是否可读且完好无损。

   ```console
   RESTORE FILELISTONLY FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>.bak'
   ```

1. 准备并运行 **RESTORE DATABASE** 命令，如下所示，将备份文件还原到 SQL 上的数据库托管实例-Azure Arc

   ```sql
   RESTORE DATABASE <database name> FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>'
   WITH MOVE 'Test' to '/var/opt/mssql/data/<file name>.mdf'
   ,MOVE 'Test_log' to '/var/opt/mssql/data/<file name>.ldf'
   ,RECOVERY  
   ,REPLACE  
   ,STATS = 5;  
   GO
   ```

在此处了解有关备份到 URL 的详细信息：

[备份到 URL 文档](/sql/relational-databases/backup-restore/sql-server-backup-to-url)

[使用 SQL Server Management Studio (SSMS 备份到 URL) ](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service)

-------

## <a name="method-2-copy-the-backup-file-into-an-azure-sql-managed-instance---azure-arc-pod-using-kubectl"></a>方法2：使用 kubectl 将备份文件复制到 Azure SQL 托管实例-Azure Arc pod

此方法演示如何获取通过任何方法创建的备份文件，然后将其复制到 Azure SQL 托管实例 pod 中的本地存储中，这样就可以在 Windows 或 Linux 上的典型文件系统中进行还原。 在此方案中，你将使用命令将 `kubectl cp` 文件从一个位置复制到 pod 的文件系统中。

### <a name="prerequisites"></a>必备条件

- 安装并配置 kubectl 以指向部署 Azure Arc 数据服务的 Kubernetes 群集
- 安装 Azure Data Studio 或 SQL Server 管理服务器的工具，并将其连接到要在其中创建备份文件的 SQL Server，或已在本地文件系统上创建现有的 .bak 文件。

### <a name="step-1-backup-the-database-if-you-havent-already"></a>步骤1：备份数据库（如果尚未这样做）

将 SQL Server 数据库备份到本地文件路径，如任何典型的 SQL Server 备份到磁盘：

 ```sql
BACKUP DATABASE Test
TO DISK = 'c:\tmp\test.bak'
WITH FORMAT, MEDIANAME = 'Test’ ;
GO
```

### <a name="step-2-copy-the-backup-file-into-the-pods-file-system"></a>步骤2：将备份文件复制到 pod 的文件系统中

查找部署 sql 实例的 pod 的名称。 通常，其外观应如下所示 `pod/<sqlinstancename>-0`

通过运行以下内容获取所有 pod 的列表：

 ```console
kubectl get pods -n <namespace of data controller>
```

例如：

将备份文件从本地存储复制到群集中的 sql pod。

 ```console
kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>

#Example:
kubectl cp C:\Backupfiles\test.bak sqlinstance1-0:var/opt/mssql/data/test.bak -n arc
```

### <a name="step-3-restore-the-database"></a>步骤3：还原数据库

准备并运行还原命令，将备份文件还原到 Azure SQL 托管实例-Azure Arc

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```

示例：

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/test.bak'
WITH MOVE 'test' to '/var/opt/mssql/data/test.mdf'  
,MOVE 'test' to '/var/opt/mssql/data/test_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```


## <a name="next-steps"></a>后续步骤

[详细了解启用了 Azure Arc 的 SQL 托管实例的特性和功能](managed-instance-features.md)

[首先创建数据控制器](create-data-controller.md)

[已创建数据控制器？创建启用了 Azure Arc 的 SQL 托管实例](create-sql-managed-instance.md)