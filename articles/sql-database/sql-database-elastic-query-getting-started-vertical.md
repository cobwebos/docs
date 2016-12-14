---
title: "跨数据库查询（垂直分区）入门 | Microsoft 文档"
description: "如何在垂直分区数据库中使用弹性数据库查询"
services: sql-database
documentationcenter: 
manager: jhubbard
author: torsteng
ms.assetid: e5b44b10-c432-4f96-b20e-08615ff4d5dd
ms.service: sql-database
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: torsteng
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b1906835deb4ca413af3a1df7cfb4a86cf26a9bf


---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>跨数据库查询（纵向分区）入门（预览）
Azure SQL 数据库弹性数据库查询（预览版）可让你使用单一连接点运行跨多个数据库的 T-SQL 查询。 本主题适用于[垂直分区数据库](sql-database-elastic-query-vertical-partitioning.md)。  

完成时，你将会：了解如何配置和使用 Azure SQL 数据库执行跨多个相关数据库的查询。 

有关弹性数据库查询功能的详细信息，请参阅 [Azure SQL 数据库弹性数据库查询概述](sql-database-elastic-query-overview.md)。 

## <a name="create-the-sample-databases"></a>创建示例数据库
首先，我们需要在相同或不同逻辑服务器中创建两个数据库 **Customers** 和 **Orders**。   

在 **Orders** 数据库中执行以下查询以创建 **OrderInformation** 表并输入示例数据。 

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

现在，在 **Customers** 数据库中执行以下查询以创建 **CustomerInformation** 表并输入示例数据。 

    CREATE TABLE [dbo].[CustomerInformation]( 
        [CustomerID] [int] NOT NULL, 
        [CustomerName] [varchar](50) NULL, 
        [Company] [varchar](50) NULL 
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
    ) 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## <a name="create-database-objects"></a>创建数据库对象
### <a name="database-scoped-master-key-and-credentials"></a>数据库范围的主密钥和凭据
1. 在 Visual Studio 中打开 SQL Server Management Studio 或 SQL Server Data Tools。
2. 连接到 Orders 数据库，并执行以下 T-SQL 命令：
   
        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  
   
    “username”和“password”应是用于登录到 Customers 数据库的用户名和密码。
    当前不支持使用 Azure Active Directory 通过弹性查询进行身份验证。

### <a name="external-data-sources"></a>外部数据源
若要创建外部数据源，请对 Orders 数据库执行以下命令： 

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### <a name="external-tables"></a>外部表
在 Orders 数据库中创建外部表，该表应与 CustomerInformation 表的定义相匹配：

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>执行示例弹性数据库 T-SQL 查询
定义外部数据源和外部表后，现在可以使用 T-SQL 查询外部表。 对 Orders 数据库执行以下查询： 

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## <a name="cost"></a>成本
目前，弹性数据库查询功能包含在 Azure SQL 数据库的成本中。  

有关定价信息，请参阅 [SQL 数据库定价](/pricing/details/sql-database)。 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->

<!--anchors-->



<!--HONumber=Nov16_HO3-->


