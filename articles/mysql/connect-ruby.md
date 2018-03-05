---
title: "使用 Ruby 连接到 Azure Database for MySQL"
description: "本快速入门提供多个 Ruby 代码示例，使用这些示例可连接到适用于 MySQL 的 Azure 数据库并查询其中的数据。"
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: 84898a5bc3afd21aa6b922b4074fd09b4315dd6e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="azure-database-for-mysql-use-ruby-to-connect-and-query-data"></a>适用于 MySQL 的 Azure 数据库：使用 Ruby 进行连接并查询数据
本快速入门演示如何在 Windows、Ubuntu Linux 和 Mac 平台中使用 [Ruby](https://www.ruby-lang.org) 应用程序和 [mysql2](https://rubygems.org/gems/mysql2) gem 连接到适用于 MySQL 的 Azure 数据库。 同时还介绍了如何使用 SQL 语句在数据库中查询、插入、更新和删除数据。 本主题假设你熟悉如何使用 Ruby 进行开发，但不太熟悉 Azure Database for MySQL 的用法。

## <a name="prerequisites"></a>先决条件
此快速入门使用以下任意指南中创建的资源作为起点：
- [使用 Azure 门户创建用于 MySQL 服务器的 Azure 数据库](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [使用 Azure CLI 创建用于 MySQL 服务器的 Azure 数据库](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-ruby"></a>安装 Ruby
在自己的计算机上安装 Ruby、Gem 和 MySQL2 库。 

### <a name="windows"></a>Windows
1. 下载并安装 [Ruby](http://rubyinstaller.org/downloads/) 版本 2.3。
2. 从“开始”菜单启动新的命令提示符 (cmd)。
3. 将目录切换到 Ruby 版本 2.3 所在的目录。 `cd c:\Ruby23-x64\bin`
4. 运行 `ruby -v` 命令查看所安装的版本，以测试 Ruby 安装。
5. 运行 `gem -v` 命令查看所安装的版本，以测试 Gem 安装。
6. 运行命令 `gem install mysql2`，使用 Gem 生成适用于 Ruby 的 Mysql2 模块。

### <a name="macos"></a>MacOS
1. 运行命令 `brew install ruby`，使用 Homebrew 安装 Ruby。 如需更多安装选项，请参阅 Ruby [安装文档](https://www.ruby-lang.org/en/documentation/installation/#homebrew)。
2. 运行 `ruby -v` 命令查看所安装的版本，以测试 Ruby 安装。
3. 运行 `gem -v` 命令查看所安装的版本，以测试 Gem 安装。
4. 运行命令 `gem install mysql2`，使用 Gem 生成适用于 Ruby 的 Mysql2 模块。

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. 通过运行命令 `sudo apt-get install ruby-full` 安装 Ruby。 如需更多安装选项，请参阅 Ruby [安装文档](https://www.ruby-lang.org/en/documentation/installation/)。
2. 运行 `ruby -v` 命令查看所安装的版本，以测试 Ruby 安装。
3. 通过运行命令 `sudo gem update --system` 安装 Gem 的最新更新。
4. 运行 `gem -v` 命令查看所安装的版本，以测试 Gem 安装。
5. 通过运行命令 `sudo apt-get install build-essential` 安装 gcc、make 和其他生成工具。
6. 运行 `sudo apt-get install libmysqlclient-dev` 命令安装 MySQL 客户端开发人员库。
7. 运行命令 `sudo gem install mysql2`，使用 Gem 生成适用于 Ruby 的 mysql2 模块。

## <a name="get-connection-information"></a>获取连接信息
获取连接到 Azure Database for MySQL 所需的连接信息。 需要完全限定的服务器名称和登录凭据。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在 Azure 门户的左侧菜单中，单击“所有资源”，然后搜索已创建的服务器（例如 mydemoserver）。
3. 单击服务器名称。
4. 从服务器的“概览”面板中记下“服务器名称”和“服务器管理员登录名”。 如果忘记了密码，也可通过此面板来重置密码。
 ![Azure Database for MySQL 服务器名称](./media/connect-ruby/1_server-overview-name-login.png)

## <a name="run-ruby-code"></a>运行 Ruby 代码 
1. 将以下部分中的 Ruby 代码粘贴到文本文件，然后使用文件扩展名 .rb 将这些文件保存到项目文件夹中（例如 `C:\rubymysql\createtable.rb` 或 `/home/username/rubymysql/createtable.rb`）。
2. 若要运行此代码，请启动命令提示符或 Bash shell。 将目录切换到项目文件夹 `cd rubymysql`
3. 然后键入 Ruby 命令并后接文件名（例如 `ruby createtable.rb`）以运行应用程序。
4. 在 Windows OS 上，如果 Ruby 应用程序不在路径环境变量中，则可能需要使用完整路径来启动 Node 应用程序，例如 `"c:\Ruby23-x64\bin\ruby.exe" createtable.rb`

## <a name="connect-and-create-a-table"></a>进行连接并创建表
使用以下代码进行连接，使用 CREATE TABLE SQL 语句创建表，然后使用 INSERT INTO SQL 语句将行添加到表中。

该代码使用 [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) 类的 .new() 方法连接到适用于 MySQL 的 Azure 数据库。 然后，该代码调用 [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) 方法多次，以运行 DROP、CREATE TABLE 和 INSERT INTO 命令。 然后，它会调用方法 [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method)，在终止之前关闭连接。

将 `host`、`database`、`username` 和 `password` 字符串替换为你自己的值。 
```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Drop previous table of same name if one exists
    client.query('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    client.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    client.query("INSERT INTO inventory VALUES(1, 'banana', 150)")
    client.query("INSERT INTO inventory VALUES(2, 'orange', 154)")
    client.query("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="read-data"></a>读取数据
使用以下代码进行连接，并使用 SELECT SQL 语句读取数据。 

该代码使用 [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) class.new() 方法连接到 Azure Database for MySQL。 然后，该代码调用 [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) 方法来运行 SELECT 命令。 然后，它会调用方法 [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method)，在终止之前关闭连接。

将 `host`、`database`、`username` 和 `password` 字符串替换为你自己的值。 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Read data
    resultSet = client.query('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end
    puts 'Read ' + resultSet.count.to_s + ' row(s).'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="update-data"></a>更新数据
使用以下代码进行连接，并使用 UPDATE SQL 语句更新数据。

该代码使用 [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) 类的 .new() 方法连接到适用于 MySQL 的 Azure 数据库。 然后，该代码调用 [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) 方法来运行 UPDATE 命令。 然后，它会调用方法 [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method)，在终止之前关闭连接。

将 `host`、`database`、`username` 和 `password` 字符串替换为你自己的值。 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Update data
   client.query('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
   puts 'Updated 1 row of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```


## <a name="delete-data"></a>删除数据
使用以下代码进行连接，并使用 DELETE SQL 语句读取数据。 

该代码使用 [mysql2::client](http://www.rubydoc.info/gems/mysql2/0.4.8) 类的 .new() 方法连接到适用于 MySQL 的 Azure 数据库。 然后，该代码调用 [query()](http://www.rubydoc.info/gems/mysql2/0.4.8#Usage) 方法来运行 DELETE 命令。 然后，它会调用方法 [close()](http://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method)，在终止之前关闭连接。

将 `host`、`database`、`username` 和 `password` 字符串替换为你自己的值。 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Delete data
    resultSet = client.query('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [使用导出和导入功能迁移数据库](./concepts-migrate-import-export.md)
