---
title: 使用 Ruby 进行查询
description: 本主题介绍如何使用 Ruby 创建连接到数据库的程序并使用 Transact-SQL 语句对其进行查询。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2 
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 57e364d8cbd3cb9e1e2996786ee6414b7d7e04a8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "86504949"
---
# <a name="quickstart-use-ruby-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>快速入门：使用 Ruby 查询 Azure SQL 数据库中的数据库或 Azure SQL 托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本快速入门演示了如何使用 [Ruby](https://www.ruby-lang.org) 连接到数据库并使用 Transact-SQL 语句查询数据。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，需要具备以下先决条件：

- 数据库。 可以使用以下某个快速入门创建数据库，然后对其进行配置：

  | 操作 | SQL 数据库 | SQL 托管实例 | Azure VM 上的 SQL Server |
  |:--- |:--- |:---|:---|
  | 创建| [门户](single-database-create-quickstart.md) | [门户](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | 配置 | [服务器级别 IP 防火墙规则](firewall-create-server-level-portal-quickstart.md)| [从 VM 进行连接](../managed-instance/connect-vm-instance-configure.md)|
  |||[来自本地的连接](../managed-instance/point-to-site-p2s-configure.md) | [连接到 SQL Server 实例](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |加载数据|根据快速入门加载的 Adventure Works|[还原 Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [还原 Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||从 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 的一个 [BACPAC](database-import.md) 文件还原或导入 Adventure Works| 从 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 所提供的 [BACPAC](database-import.md) 文件还原或导入 Adventure Works|
  |||

  > [!IMPORTANT]
  > 本文中脚本的编写目的是使用 Adventure Works 数据库。 使用 SQL 托管实例时，必须将 Adventure Works 数据库导入一个实例数据库，或者修改本文中的脚本，以便使用 Wide World Importers 数据库。
  
- 适用于你的操作系统的 Ruby 和相关软件：
  
  - **macOS**：安装 Homebrew、rbenv 和 ruby-build、Ruby、FreeTDS 和 TinyTDS。 请参阅 [Create Ruby apps using SQL Server on macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/)（在 macOS 上使用 SQL Server 创建 Ruby 应用）中的步骤 1.2、1.3、1.4、1.5 和 2.1。
  
  - **Ubuntu**：安装 Ruby、rbenv 和 ruby-build、Ruby、FreeTDS 和 TinyTDS 的先决条件。 请参阅 [Create Ruby apps using SQL Server on Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/)（在 Ubuntu 上使用 SQL Server 创建 Ruby 应用）中的步骤 1.2、1.3、1.4、1.5 和 2.1。
  
  - Windows：安装 Ruby、Ruby Devkit 和 TinyTDS。 请参阅[配置用于 Ruby 开发的开发环境](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development)。

## <a name="get-server-connection-information"></a>获取服务器连接信息

获取连接到 Azure SQL 数据库中的数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称或主机名称、数据库名称和登录信息。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 导航到“SQL 数据库”或“SQL 托管实例”页。

3. 在“概述”页上，在“Server 名称”旁查看 Azure SQL 数据库中的数据库的完全限定服务器名称，或在“Host”旁边查看 Azure VM 上的 Azure SQL 托管实例或 SQL Server 的完全限定服务器名称（或 IP 地址）  。 若要复制服务器名称或主机名称，请将鼠标悬停在其上方，然后选择“复制”图标。

> [!NOTE]
> 有关 Azure VM 上的 SQL Server 的连接信息，请参阅[连接到 SQL Server 实例](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)。

## <a name="create-code-to-query-your-database-in-azure-sql-database"></a>创建用于查询 Azure SQL 数据库中的数据库的代码

1. 在文本或代码编辑器中，创建新文件 sqltest.py。

1. 添加以下代码。 将 Azure SQL 数据库中的数据库的值替换为`<server>`、`<database>`、`<username>` 和 `<password>`。

   >[!IMPORTANT]
   >本示例中的代码使用示例 AdventureWorksLT 数据，在创建数据库时可以选择该数据作为源。 如果数据库有不同数据，请在 SELECT 查询中使用自己数据库中的表。 

   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   client = TinyTds::Client.new username: username, password: password, 
       host: server, port: 1433, database: database, azure: true
   
   puts "Reading data from table"
   tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
           ON pc.productcategoryid = p.productcategoryid"
   result = client.execute(tsql)
   result.each do |row|
       puts row
   end
   ```

## <a name="run-the-code"></a>运行代码

1. 请在命令提示符处运行以下命令：

   ```bash
   ruby sqltest.rb
   ```
   
1. 验证是否返回了数据库中的前 20 个类别/产品行。 

## <a name="next-steps"></a>后续步骤
- [在 Azure SQL 数据库中设计第一个数据库](design-first-database-tutorial.md)
- [TinyTDS 的 GitHub 存储库](https://github.com/rails-sqlserver/tiny_tds)
- [针对 TinyTDS 报告问题或提问](https://github.com/rails-sqlserver/tiny_tds/issues)
- [用于 SQL Server 的 Ruby 驱动程序](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)
