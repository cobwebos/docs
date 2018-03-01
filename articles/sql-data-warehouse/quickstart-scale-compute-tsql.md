---
title: "快速入门：在 Azure SQL 数据仓库中横向扩展计算资源 - T-SQL | Microsoft Docs"
description: "通过调整 DWU 缩放计算资源的 T-SQL 命令。"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/31/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: f984f9b348f589e20cb8a4b68578ddef84d8292d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-using-t-sql"></a>快速入门：使用 T-SQL 在 Azure SQL 数据仓库中缩放计算资源

缩放 Azure SQL 数据仓库使用 T-SQL 和 SQL Server Management Studio (SSMS) 中的计算。 [横向扩展计算](sql-data-warehouse-manage-compute-overview.md)（可提高性能）或缩放重新计算（可节约成本）。 

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="before-you-begin"></a>开始之前

下载并安装最新版本的 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS)。

这假定你已完成[快速入门：创建并连接-门户](create-data-warehouse-portal.md)。 完成创建和连接快速入门后，知道如何连接到：创建名为数据仓库 **mySampleDataWarehouse**，创建防火墙规则，让我们客户端可安装的服务器访问。
 
## <a name="create-a-data-warehouse"></a>创建数据仓库

使用[创建并连接 - 门户](create-data-warehouse-portal.md)创建名为“mySampleDataWarehouse”的数据仓库。 完成快速入门，以确保防火墙规则并可以连接到数据仓库从 SQL Server Management Studio 中。

## <a name="connect-to-the-server-as-server-admin"></a>以服务器管理员的身份连接到服务器

本部分使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS) 来建立与 Azure SQL Server 的连接。

1. 打开 SQL Server Management Studio。

2. 在“连接到服务器”对话框中，输入以下信息：

   | 设置       | 建议的值 | 说明 | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | 服务器类型 | 数据库引擎 | 此值是必需的 |
   | 服务器名称 | 完全限定的服务器名称 | 下面是一个示例：mynewserver-20171113.database.windows.net。 |
   | 身份验证 | SQL Server 身份验证 | SQL 身份验证是本教程中配置的唯一身份验证类型。 |
   | 登录 | 服务器管理员帐户 | 这是在创建服务器时指定的帐户。 |
   | 密码 | 服务器管理员帐户的密码 | 这是在创建服务器时指定的密码。 |

    ![连接到服务器](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

4. 单击“连接”。 此时会在 SSMS 中打开“对象资源管理器”窗口。 

5. 在对象资源管理器中，展开“数据库”。 然后展开“mySampleDatabase”，查看新数据库中的对象。

    ![数据库对象](media/create-data-warehouse-portal/connected.png) 

## <a name="view-service-objective"></a>查看服务目标
服务目标设置包含的数据仓库的数据仓库单位数。 

若要查看数据仓库当前数据仓库单位：

1. 在连接到下 **mynewserver 20171113.database.windows.net**，展开**系统数据库**。
2. 右键单击“master”，并单击“新建查询”。 此时会打开一个新的查询窗口。
3. 运行以下查询，从 sys.database_service_objectives 动态管理视图中选择。 

    ```sql
    SELECT
        db.name [Database]
    ,   ds.edition [Edition]
    ,   ds.service_objective [Service Objective]
    FROM
        sys.database_service_objectives ds
    JOIN
        sys.databases db ON ds.database_id = db.database_id
    WHERE 
        db.name = 'mySampleDataWarehouse'
    ```

4. 以下结果显示 **mySampleDataWarehouse** 具有一个 DW400 的服务目标。 

    ![查看当前 DWU](media/quickstart-scale-compute-tsql/view-current-dwu.png)


## <a name="scale-compute"></a>缩放计算
在 SQL 数据仓库中，可以通过调整数据仓库单位来增加或减少计算资源。 [创建和 Connect - 门户](create-data-warehouse-portal.md)创建 **mySampleDataWarehouse** 并初始化 400 DWU。 以下步骤调整为 DWU **mySampleDataWarehouse**。

更改数据仓库单位：

1. 右键单击“master”，并单击“新建查询”。
2. 使用 [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) T-SQL 语句修改的服务目标。 运行以下查询以将服务目标更改为 DW300。 

```Sql
ALTER DATABASE mySampleDataWarehouse
MODIFY (SERVICE_OBJECTIVE = 'DW300')
;
```

## <a name="check-database-state"></a>检查数据库状态

若要检查数据库状态，对其运行以下查询 **master** 数据库。

```sql
SELECT name AS "Database Name", state_desc AS "Status" 
FROM sys.databases db
WHERE db.name = 'mySampleDataWarehouse'
;
```

运行此命令后，应收到“联机”、“正在暂停”、“正在恢复”、“正在缩放”和“已暂停”等状态值。

## <a name="check-operation-status"></a>检查操作状态

若要返回有关在 SQL 数据仓库的各种管理操作的信息，请运行以下查询 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) DMV。 例如，它将返回该操作，将是 IN_PROGRESS 或完成的操作的状态。

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND 
    major_resource_id = 'MySQLDW'
```


## <a name="next-steps"></a>后续步骤
现在已暂停并恢复了数据仓库的计算。 若要了解有关 Azure SQL 数据仓库的详细信息，请继续有关加载数据的教程。

> [!div class="nextstepaction"]
>[将数据加载到 SQL 数据仓库](load-data-from-azure-blob-storage-using-polybase.md)
