---
title: 将 R 与机器学习服务配合使用来查询数据库（预览版）
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 本文演示如何将 R 脚本与 Azure SQL 数据库机器学习服务配合使用，以连接到 Azure SQL 数据库并使用 Transact-SQL 语句对其进行查询。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ms.openlocfilehash: 7103afc29e4021d950d9a3634b190f4439ecfe8d
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768513"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>快速入门：将 R 与机器学习服务配合使用来查询 Azure SQL 数据库（预览版）

在本快速入门中，我们将 R 与机器学习服务配合使用来连接到 Azure SQL 数据库，并使用 T-SQL 语句来查询数据。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 一个 [Azure SQL 数据库](sql-database-single-database-get-started.md)
- 启用了 R 的[机器学习服务](sql-database-machine-learning-services-overview.md)。 [注册预览版](sql-database-machine-learning-services-overview.md#signup)。
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> 本文中脚本的编写目的是使用 **Adventure Works** 数据库。

> [!NOTE]
> 在发布公共预览版期间，Microsoft 会让你加入并为你的现有数据库或新数据库启用机器学习，但目前不支持托管实例部署选项。

使用 R 的机器学习服务是 Azure SQL 数据库的一项功能，用于执行数据库中的 R 脚本。 有关详细信息，请参阅 [R 项目](https://www.r-project.org/)。

## <a name="get-sql-server-connection-information"></a>获取 SQL Server 连接信息

获取连接到 Azure SQL 数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称或主机名称、数据库名称和登录信息。

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 导航到“SQL 数据库”或“SQL 托管实例”页。  

3. 在“概览”页中，查看单一数据库的“服务器名称”旁边的完全限定的服务器名称，或者托管实例的“主机”旁边的完全限定的服务器名称    。 若要复制服务器名称或主机名称，请将鼠标悬停在其上方，然后选择“复制”图标  。

## <a name="create-code-to-query-your-sql-database"></a>创建用于查询 SQL 数据库的代码

1. 打开 **SQL Server Management Studio**，连接到 SQL 数据库。

   如果在连接方面需要帮助，请参阅[快速入门：使用 SQL Server Management Studio 连接和查询 Azure SQL 数据库](sql-database-connect-query-ssms.md)。

1. 将完整的 R 脚本传递给 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 存储过程。

   通过 `@script` 参数传递脚本。 `@script` 参数内的所有内容都必须是有效的 R 代码。
   
   >[!IMPORTANT]
   >本示例中的代码使用示例 AdventureWorksLT 数据，在创建数据库时可以选择该数据作为源。 如果数据库有不同数据，请在 SELECT 查询中使用自己数据库中的表。 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > 如果出现错误，则可能是因为尚未为 SQL 数据库启用机器学习服务（使用 R）的公共预览版。 请参阅上述[先决条件](#prerequisites)。

## <a name="run-the-code"></a>运行代码

1. 执行 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 存储过程。

1. 在“消息”窗口中验证是否返回了前 20 个类别/产品行。 

## <a name="next-steps"></a>后续步骤

- [设计第一个 Azure SQL 数据库](sql-database-design-first-database.md)
- [Azure SQL 数据库机器学习服务（使用 R）](sql-database-machine-learning-services-overview.md)
- [在 Azure SQL 数据库机器学习服务（预览版）中创建和运行简单的 R 脚本](sql-database-quickstart-r-create-script.md)
- [使用机器学习服务（预览版）在 Azure SQL 数据库中编写高级 R 函数](sql-database-machine-learning-services-functions.md)
