---
title: "结合使用弹性查询和 Azure SQL 数据仓库的教程 | Microsoft Docs"
description: "了解如何结合使用弹性查询和 Azure SQL 数据仓库"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67g
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 11/03/2017
ms.author: elbutter
ms.openlocfilehash: a13b81213b7a47cb7209bc914f514fa10aede5c4
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="configure-elastic-query-with-sql-data-warehouse"></a>配置结合使用弹性查询和 SQL 数据仓库

本教程将介绍如何使用弹性查询，将查询从 SQL 数据库提交到 SQL 数据仓库。 弹性查询是一种跨 Azure SQL 产品的功能。 若要详细了解弹性查询的概念，请参阅[如何结合使用弹性查询和 SQL 数据仓库][How to use Elastic Query with SQL Data Warehouse]。

## <a name="prerequisites-for-the-tutorial"></a>教程先决条件

开始阅读本教程前，必须先满足下列先决条件：

1. 已安装 SQL Server Management Studio (SSMS)。
2. 已创建 Azure SQL Server，此服务器中有数据库和数据仓库。
3. 设置用于访问 Azure SQL Server 的防火墙规则。

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>在 SQL 数据仓库和 SQL 数据库实例之间建立连接 

1. 使用 SSMS 或其他查询客户端，在逻辑服务器上对数据库 master 打开新查询。

2. 创建表示 SQL 数据库与数据仓库之间连接的登录用户凭据。

  ```sql
  CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
  ```

3. 使用 SSMS 或其他查询客户端，在逻辑服务器上对 SQL 数据仓库实例打开新查询。

4. 使用第 2 步中创建的登录用户凭据，在数据仓库实例上创建用户

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. 向在第 4 步中创建的供 SQL 数据库执行的用户授予权限。 在此示例中，仅针对特定架构上的 SELECT 授予权限，这说明了可能会将来自 SQL 数据库的查询限制为属于特定域。 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. 使用 SSMS 或其他查询客户端，在逻辑服务器上对 SQL 数据库实例打开新查询。

7. 如果还没有主密钥，请创建一个。 

   ```sql
   CREATE MASTER KEY; 
   ```

8. 使用在第 2 步中创建的凭据，创建数据库限定范围的凭据。

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL SalesDBElasticCredential
   WITH IDENTITY = 'SalesDBLogin',
   SECRET = 'aReallyStrongPassword@#!';
   ```

9. 创建指向数据仓库实例的外部数据源。

  ```sql
  CREATE EXTERNAL DATA SOURCE EnterpriseDwSrc WITH 
      (TYPE = RDBMS, 
      LOCATION = '<SERVER NAME>.database.windows.net', 
      DATABASE_NAME = '<SQL DATA WAREHOUSE NAME>', 
      CREDENTIAL = SalesDBElasticCredential, 
  ) ;
  ```

10. 现在，可以创建外部表来引用此外部数据源。 使用这些表的查询被发送到数据仓库实例，以供处理并发送回数据库实例。


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>从 SQL 数据库提交到 SQL 数据仓库的弹性查询

接下来的几步将使用几个值，在数据仓库实例中创建一个表。 然后，介绍了如何设置外部表，从数据库实例查询数据仓库实例。

1. 使用 SSMS 或其他查询客户端，在逻辑服务器上对 SQL 数据仓库打开新查询。

2. 提交以下查询，在数据仓库实例中创建 OrdersInformation 表。

   ```sql
   CREATE TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL 
   ,   [CustomerID] [int] NOT NULL 
   ) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
   ```

3. 使用 SSMS 或其他查询客户端，在逻辑服务器上对 SQL 数据库打开新查询。

4. 提交以下查询，创建指向数据仓库实例中 OrdersInformation 表的外部表定义。

   ```sql
   CREATE EXTERNAL TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL
   ,   [CustomerID] [int] NOT NULL 
   ) 
   WITH 
   (
        DATA_SOURCE = EnterpriseDwSrc
   ,    SCHEMA_NAME = N'dbo'
   ,    OBJECT_NAME = N'OrderInformation'
   )
   ```

5. 请注意，现在 SQL 数据库实例中有一个外部表定义。

   ![弹性查询外部表定义](./media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. 提交以下查询，用于查询数据仓库实例。 应该会收到在第 2 步中插入的五个值。 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> 请注意，尽管值很少，但此查询需要相当长的一段时间才能返回值。 结合使用弹性查询和数据仓库时，应考虑查询处理和在线传输开销。 如果计算能力（而不是延迟）为优先事项，可利用弹性查询远程执行。

恭喜，已设置了非常基础的弹性查询。 




<!--Image references-->

<!--Article references-->

[How to use Elastic Query with SQL Data Warehouse]: ./how-to-use-elastic-query-with-sql-data-warehouse.md

<!--MSDN references-->

<!--Other Web references-->