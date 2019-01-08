---
title: 使用 Ruby 查询 Azure SQL 数据库 | Microsoft Docs
description: 本主题介绍如何使用 Ruby 创建连接到 Azure SQL 数据库的程序并使用 Transact-SQL 语句对其进行查询。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: 66819cbd65f6f044d0dac68326eb5890476964b6
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993903"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>快速入门：使用 Ruby 查询 Azure SQL 数据库

本快速入门演示了如何使用 [Ruby](https://www.ruby-lang.org) 连接到 Azure SQL 数据库并使用 Transact-SQL 语句查询数据。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，需要具备以下先决条件：

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- 针对用于本快速入门的计算机的公共 IP 地址制定[服务器级防火墙规则](sql-database-get-started-portal-firewall.md)。
  
- 适用于你的操作系统的 Ruby 和相关软件：
  
  - **MacOS**：安装 Homebrew、rbenv 和 ruby-build、Ruby、FreeTDS 和 TinyTDS。 请参阅 [Create Ruby apps using SQL Server on macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/)（在 macOS 上使用 SQL Server 创建 Ruby 应用）中的步骤 1.2、1.3、1.4、1.5 和 2.1。
  
  - **Ubuntu**：安装 Ruby、rbenv 和 ruby-build、Ruby、FreeTDS 和 TinyTDS 的先决条件。 请参阅 [Create Ruby apps using SQL Server on Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/)（在 Ubuntu 上使用 SQL Server 创建 Ruby 应用）中的步骤 1.2、1.3、1.4、1.5 和 2.1。
  
  - Windows：安装 Ruby、Ruby Devkit 和 TinyTDS。 请参阅[配置用于 Ruby 开发的开发环境](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development)。

## <a name="get-sql-server-connection-information"></a>获取 SQL Server 连接信息

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

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
