---
title: 使用 Ruby 查询 Azure SQL 数据库 | Microsoft Docs
description: 本主题介绍如何使用 Ruby 创建连接到 Azure SQL 数据库的程序并使用 Transact-SQL 语句对其进行查询。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: ruby
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 30dce6caefc0b95435d1dadc48aa927be475fc66
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="use-ruby-to-query-an-azure-sql-database"></a>使用 Ruby 查询 Azure SQL 数据库

本快速入门教程演示如何使用 [Ruby](https://www.ruby-lang.org) 创建连接到 Azure SQL 数据库的程序，并使用 Transact-SQL 语句查询数据。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门教程，请确保符合以下先决条件：

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- 针对用于本快速入门教程的计算机的公共 IP 地址制定[服务器级防火墙规则](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。

- 为操作系统安装了 Ruby 和相关软件：
    - **MacOS**：依次安装 Homebrew、rbenv、ruby-build、Ruby 和 FreeTDS。 请参阅[步骤 1.2、1.3、1.4 和 1.5](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/)。
    - **Ubuntu**：依次安装 Ruby 必备组件、rbenv、ruby-build、Ruby 和 FreeTDS。 请参阅[步骤 1.2、1.3、1.4 和 1.5](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/)。

## <a name="sql-server-connection-information"></a>SQL Server 连接信息

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> 对于在其上执行本教程操作的计算机，必须为其公共 IP 地址制定防火墙规则。 如果使用其他计算机或其他公共 IP 地址，则[使用 Azure 门户创建服务器级防火墙规则](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。 

## <a name="insert-code-to-query-sql-database"></a>插入用于查询 SQL 数据库的代码

1. 在偏好的文本编辑器中创建新文件 **sqltest.rb**。

2. 将内容替换为以下代码，为服务器、数据库、用户和密码添加相应的值。

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
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

2. 验证是否已返回前 20 行，然后关闭应用程序窗口。


## <a name="next-steps"></a>后续步骤
- [设计第一个 Azure SQL 数据库](sql-database-design-first-database.md)
- [TinyTDS 的 GitHub 存储库](https://github.com/rails-sqlserver/tiny_tds)
- [针对 TinyTDS 报告问题或提问](https://github.com/rails-sqlserver/tiny_tds/issues)
- [用于 SQL Server 的 Ruby 驱动程序](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)
