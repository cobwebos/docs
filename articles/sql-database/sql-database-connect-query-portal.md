---
title: Azure 门户：使用查询编辑器查询 Azure SQL 数据库 | Microsoft Docs
description: 了解如何在 Azure 门户中使用 SQL 查询编辑器连接到 SQL 数据库。 然后，通过运行 Transact-SQL (T-SQL) 语句来查询和编辑数据。
keywords: 连接到 sql 数据库,azure 门户, 门户, 查询编辑器
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: AyoOlubeko
ms.author: ayolubek
ms.reviewer: carlrab
manager: craigg
ms.date: 02/12/2019
ms.openlocfilehash: e16d68f8c30b54c24e091d1b0c7317bf74aadeba
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234571"
---
# <a name="quickstart-use-the-azure-portals-sql-query-editor-to-connect-and-query-data"></a>快速入门：使用 Azure 门户的 SQL 查询编辑器进行连接并查询数据

SQL 查询编辑器是一个 Azure 门户浏览器工具，它提供了一种在 Azure SQL 数据库或 Azure SQL 数据仓库上执行 SQL 查询的简便方法。 在本快速入门中，将使用查询编辑器连接到 SQL 数据库，然后运行 Transact-SQL 语句以查询、插入、更新和删除数据。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

- Azure SQL 数据库。 可以根据下述快速入门中的一个的说明在 Azure SQL 数据库中创建数据库，然后对其进行配置：

  || 单一数据库 |
  |:--- |:--- |
  | 创建| [门户](sql-database-single-database-get-started.md) | 
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | 
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | 
  | 配置 | [服务器级别 IP 防火墙规则](sql-database-server-level-firewall-rule.md)| 
  |||

> [!NOTE]
> 确保在 SQL Server 防火墙设置中将“允许访问 Azure 服务”选项设置为“打开”。 此选项允许 SQL 查询编辑器访问数据库和数据仓库。

## <a name="sign-in-the-azure-portal"></a>登录 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="connect-using-sql-authentication"></a>使用 SQL 身份验证进行连接

1. 从左侧菜单中选择“SQL 数据库”，然后选择“mySampleDatabase”。

2. 在左侧菜单中，找到并选择“查询编辑器(预览)”。 “登录”页随即显示。

    ![查找查询编辑器](./media/sql-database-connect-query-portal/find-query-editor.PNG)

3. 从“授权类型”下拉菜单中，选择“SQL Server 身份验证”，然后输入用于创建数据库的服务器管理员帐户的用户 ID 和密码。

    ![登录](./media/sql-database-connect-query-portal/login-menu.png) 

4. 选择“确定”。


## <a name="connect-using-azure-active-directory"></a>使用 Azure Active Directory 进行连接

配置一个 Active Directory (AD) 管理员即可使用单个标识登录到 Azure 门户和 SQL 数据库。 请按照以下步骤配置 SQL Server 的 AD 管理员。

> [!NOTE]
* AD 管理员尚不支持电子邮件帐户（例如，outlook.com、gmail.com、yahoo.com 等）。 请确保选择在 Azure AD 中本机创建或联合到 Azure AD 中的用户。
* Azure AD 管理员登录不适用于已启用双因素身份验证的帐户。

1. 从左侧菜单中选择“所有资源”，然后选择 SQL Server。

2. 从 SQL Server 的“设置”菜单中，选择“Active Directory 管理员”。

3. 从 AD 管理员页工具栏中，选择“设置管理员”，然后选择用户或组作为 AD 管理员。

    ![选择 active directory](./media/sql-database-connect-query-portal/select-active-directory.png)

4. 从 AD 管理员页工具栏中，选择“保存”。

5. 导航到“mySampleDatabase”数据库，并从左侧菜单中，选择“查询编辑器(预览)”。 “登录”页随即显示。 如果你是 AD 管理员，则在右侧的“Active Directory 单一登录”下会显示一条消息，指出你已登录。 
   
6. 选择“确定”。


## <a name="view-data"></a>查看数据

1. 完成身份验证后，在查询编辑器中粘贴以下 SQL，按类别检索排名前 20 的产品。

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. 在工具栏上，选择“运行”，然后查看“结果”窗格中的输出。

![查询编辑器结果](./media/sql-database-connect-query-portal/query-editor-results.png)

## <a name="insert-data"></a>插入数据

运行以下 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 语句，将新产品添加到 `SalesLT.Product` 表中。

1. 将之前的查询替换为此查询。

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```


2. 选择“运行”在 `Product` 表中插入新行。 “消息”窗格显示“查询已成功：受影响的行：1”。


## <a name="update-data"></a>更新数据

运行以下 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 语句修改新产品。

1. 将之前的查询替换为此查询。

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. 选择“运行”更新 `Product` 表中的指定行。 “消息”窗格显示“查询已成功：受影响的行：1”。

## <a name="delete-data"></a>删除数据

运行以下 [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 语句删除新产品。

1. 将之前的查询替换为此查询：

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. 选择“运行”删除 `Product` 表中的指定行。 “消息”窗格显示“查询已成功：受影响的行：1”。


## <a name="query-editor-considerations"></a>查询编辑器注意事项

使用查询编辑器时，需要注意以下要点。

* 不能在虚拟网络中使用查询编辑器查询 SQL Server 数据库。

* 按 F5 刷新查询编辑器页，任何正在处理的查询都将丢失。

* 查询编辑器不支持连接到 `master` 数据库。

* 查询执行的超时为 5 分钟。

* 查询编辑器仅支持地理数据类型的柱面投影。

* 不支持适用于数据库表和视图的 IntelliSense。 但是，此编辑器支持对已键入的名称启用自动完成功能。


## <a name="next-steps"></a>后续步骤

若要了解有关 Azure SQL 数据库支持的 Transact-SQL 的详细信息，请参阅[解析迁移到 SQL 数据库的过程中的 Transact-SQL 差异](sql-database-transact-sql-information.md)。
