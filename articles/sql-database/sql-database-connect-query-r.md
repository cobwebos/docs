---
title: 将 R 与机器学习服务配合使用进行查询
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
ms.openlocfilehash: a54b538247f81ea3bb0ea70a2af374158bd9e2ff
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826969"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>快速入门：将 R 与机器学习服务配合使用来查询 Azure SQL 数据库（预览版）

本快速入门演示了如何将 [R](https://www.r-project.org/) 与机器学习服务配合使用来连接到 Azure SQL 数据库并使用 Transact-SQL 语句来查询数据。 机器学习服务是 Azure SQL 数据库的一项功能，用于执行数据库中的 R 脚本。 有关进一步的信息，请参阅[使用 R 的 Azure SQL 数据库机器学习服务（预览版）](sql-database-machine-learning-services-overview.md)。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，请确保符合以下条件：

- Azure SQL 数据库。 可以根据下述快速入门之一，在 Azure SQL 数据库中创建数据库，然后对其进行配置：

<!-- Managed instance is not supported during the preview
  || Single database | Managed instance |
  |:--- |:--- |:---|
  | Create| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configure | [Server-level IP firewall rule](sql-database-server-level-firewall-rule.md) | [Connectivity from a VM](sql-database-managed-instance-configure-vm.md) |
  ||| [Connectivity from on-site](sql-database-managed-instance-configure-p2s.md) |
  | Load data | Adventure Works loaded per quickstart | [Restore Wide World Importers](sql-database-managed-instance-get-started-restore.md) |
  ||| Restore or import Adventure Works from [BACPAC](sql-database-import.md) file from [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) |
  |||
-->

  || 单一数据库 |
  |:--- |:--- |
  | 创建| [门户](sql-database-single-database-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | 配置 | [服务器级别 IP 防火墙规则](sql-database-server-level-firewall-rule.md) |
  | 加载数据 | 根据快速入门加载的 Adventure Works |
  |||

  > [!NOTE]
  > 在使用 R 预览 Azure SQL 数据库机器学习服务期间，不支持托管实例部署选项。

<!-- Managed instance is not supported during the preview
  > [!IMPORTANT]
  > The scripts in this article are written to use the Adventure Works database. With a managed instance, you must either import the Adventure Works database into an instance database or modify the scripts in this article to use the Wide World Importers database.
-->

- 启用了机器学习服务（使用 R）。 在发布公共预览版期间，Microsoft 会将你加入该版本并为你的现有数据库或新数据库启用机器学习。 执行[注册预览版](sql-database-machine-learning-services-overview.md#signup)中的步骤。

- 最新 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。 你可以使用其他数据库管理或查询工具运行 R 脚本，但在本快速入门中，你将使用 SSMS。

## <a name="get-sql-server-connection-information"></a>获取 SQL Server 连接信息

获取连接到 Azure SQL 数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称或主机名称、数据库名称和登录信息。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 导航到“SQL 数据库”或“SQL 托管实例”页。  

3. 在“概览”页中，查看单一数据库的“服务器名称”旁边的完全限定的服务器名称，或者托管实例的“主机”旁边的完全限定的服务器名称    。 若要复制服务器名称或主机名称，请将鼠标悬停在其上方，然后选择“复制”图标  。

## <a name="create-code-to-query-your-sql-database"></a>创建用于查询 SQL 数据库的代码

1. 打开 **SQL Server Management Studio**，连接到 SQL 数据库。

   如果在连接方面需要帮助，请参阅[快速入门：使用 SQL Server Management Studio 连接和查询 Azure SQL 数据库](sql-database-connect-query-ssms.md)。

1. 将完整的 R 脚本传递给 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 存储过程。

   此脚本是通过 `@script` 参数传递的。 `@script` 参数中的所有内容都必须是有效的 R 代码。
   
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
