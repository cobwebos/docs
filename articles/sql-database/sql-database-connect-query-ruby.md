---
title: 使用 Ruby 查询 Azure SQL 数据库 | Microsoft Docs
description: 本主题介绍如何使用 Ruby 创建连接到 Azure SQL 数据库的程序并使用 Transact-SQL 语句对其进行查询。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: d674928bbe585174db897b2a052a5fd09bcee329
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792070"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>快速入门：使用 Ruby 查询 Azure SQL 数据库

本快速入门演示了如何使用 [Ruby](https://www.ruby-lang.org) 连接到 Azure SQL 数据库并使用 Transact-SQL 语句查询数据。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，需要具备以下先决条件：

- Azure SQL 数据库。 可以根据下述快速入门中的一个的说明在 Azure SQL 数据库中创建数据库，然后对其进行配置：

  || 单一数据库 | 托管实例 |
  |:--- |:--- |:---|
  | 创建| [门户](sql-database-single-database-get-started.md) | [门户](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | 配置 | [服务器级别 IP 防火墙规则](sql-database-server-level-firewall-rule.md)| [从 VM 进行连接](sql-database-managed-instance-configure-vm.md)|
  |||[从现场进行连接](sql-database-managed-instance-configure-p2s.md)
  |加载数据|根据快速入门加载的 Adventure Works|[还原 Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||从 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 所提供的 [BACPAC](sql-database-import.md) 文件还原或导入 Adventure Works|
  |||

  > [!IMPORTANT]
  > 本文中脚本的编写目的是使用 Adventure Works 数据库。 使用托管实例时，必须将 Adventure Works 数据库导入一个实例数据库，或者修改本文中的脚本，以便使用 Wide World Importers 数据库。
  
- 适用于你的操作系统的 Ruby 和相关软件：
  
  - **MacOS**：安装 Homebrew、rbenv 和 ruby-build、Ruby、FreeTDS 和 TinyTDS。 请参阅 [Create Ruby apps using SQL Server on macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/)（在 macOS 上使用 SQL Server 创建 Ruby 应用）中的步骤 1.2、1.3、1.4、1.5 和 2.1。
  
  - **Ubuntu**：安装 Ruby、rbenv 和 ruby-build、Ruby、FreeTDS 和 TinyTDS 的先决条件。 请参阅 [Create Ruby apps using SQL Server on Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/)（在 Ubuntu 上使用 SQL Server 创建 Ruby 应用）中的步骤 1.2、1.3、1.4、1.5 和 2.1。
  
  - Windows：安装 Ruby、Ruby Devkit 和 TinyTDS。 请参阅[配置用于 Ruby 开发的开发环境](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development)。

## <a name="get-sql-server-connection-information"></a>获取 SQL Server 连接信息

获取连接到 Azure SQL 数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称或主机名称、数据库名称和登录信息。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 导航到“SQL 数据库”或“SQL 托管实例”页。

3. 在“概览”页中，查看单一数据库的“服务器名称”旁边的完全限定的服务器名称，或者托管实例的“主机”旁边的完全限定的服务器名称。 若要复制服务器名称或主机名称，请将鼠标悬停在其上方，然后选择“复制”图标。 

## <a name="create-code-to-query-your-sql-database"></a>创建用于查询 SQL 数据库的代码

1. 在文本或代码编辑器中，创建新文件 sqltest.py。
   
1. 添加以下代码。 将 Azure SQL 数据库中的值替换为`<server>`、`<database>``<username>` 和 `<password>`。
   
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
- [设计第一个 Azure SQL 数据库](sql-database-design-first-database.md)。
- [TinyTDS 的 GitHub 存储库](https://github.com/rails-sqlserver/tiny_tds)。
- [针对 TinyTDS 报告问题或提问](https://github.com/rails-sqlserver/tiny_tds/issues)。
- [用于 SQL Server 的 Ruby 驱动程序](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)。
