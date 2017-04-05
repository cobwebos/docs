---
title: "使用 Java 连接 Azure SQL 数据库 | Microsoft Docs"
description: "演示了一个可以用来连接到 Azure SQL 数据库的 Java 代码示例。"
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/27/2017
ms.author: andrela;carlrab;sstein
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: a047d4cdf869fff0d2afaf11f124370c0eae98e4
ms.lasthandoff: 03/30/2017


---
# <a name="azure-sql-database-use-java-to-connect-and-query-data"></a>Azure SQL 数据库：使用 Java 进行连接和数据查询

使用 [Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) 连接 Azure SQL 数据库并进行查询。 本指南详述了如何使用 Java 连接到 Azure SQL 数据库，然后执行查询、插入、更新和删除语句。

此快速入门使用以下某个快速入门中创建的资源作为其起点：

- [创建 DB - 门户](sql-database-get-started-portal.md)
- [创建 DB - CLI](sql-database-get-started-cli.md)

## <a name="configure-development-environment"></a>配置开发环境

下文将详细介绍如何配置现有 Mac OS、Linux(Ubuntu) 和 Windows 开发环境，以便使用 Azure SQL 数据库。

### <a name="mac-os"></a>**Mac OS**
打开终端并导航到要在其中创建 Java 项目的目录。 输入以下命令安装 **brew** 和 **Maven**。 

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
打开终端并导航到要在其中创建 Java 项目的目录。 输入以下命令安装 **Maven**。 

```
sudo apt-get install maven
```

### <a name="windows"></a>**Windows**
使用官方安装程序安装 [Maven](https://maven.apache.org/download.cgi)。  

## <a name="get-connection-information"></a>获取连接信息

在 Azure 门户中获取连接字符串。 请使用连接字符串连接到 Azure SQL 数据库。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 从左侧菜单中选择“SQL 数据库”，然后单击“SQL 数据库”页上的数据库。 
3. 在数据库的“概要”窗格中，查看完全限定的服务器名称。 

    <img src="./media/sql-database-connect-query-dotnet/server-name.png" alt="server name" style="width: 780px;" />

4. 单击“显示数据库连接字符串”。

5. 查看完整 **JDBC** 连接字符串。

    <img src="./media/sql-database-connect-query-jdbc/jdbc-connection-string.png" alt="JDBC connection string" style="width: 780px;" />

### <a name="create-maven-project"></a>**创建 Maven 项目**
从终端创建一个新的 Maven 项目。 
```
mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
```

将 **Microsoft JDBC Driver for SQL Server** 添加到 ***pom.xml*** 中的依赖项。 

```xml
<dependency>
    <groupId>com.microsoft.sqlserver</groupId>
    <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
</dependency>
```

## <a name="select-data"></a>选择数据

借助[连接](https://docs.microsoft.com/sql/connect/jdbc/working-with-a-connection)和 [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 语句，使用 Java 查询 Azure SQL 数据库中的数据。

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.Statement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "yourserver";
        String dbName = "yourdatabase";
        String user = "yourusername";
        String password = "yourpassword";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName " 
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";
                Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql);

                // Print results from select statement
                System.out.println("Top 20 categories:");
                while (resultSet.next())
                {
                    System.out.println(resultSet.getString(1) + " "
                        + resultSet.getString(2));
                }
            }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## <a name="insert-data"></a>插入数据

使用[准备的语句](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql)和 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 语句将数据插入 Azure SQL 数据库。

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "yourserver";
        String dbName = "yourdatabase";
        String user = "yourusername";
        String password = "yourpassword";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Insert data example:");
                System.out.println("=========================================");

                // Prepared statement to insert data
                String insertSql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, )" 
                    + " StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

                java.util.Date date = new java.util.Date();
                java.sql.Timestamp sqlTimeStamp = new java.sql.Timestamp(date.getTime());

                PreparedStatement prep = connection.prepareStatement(insertSql);
                prep.setString(1, "BrandNewProduct");
                prep.setInt(2, 200989);
                prep.setString(3, "Blue");
                prep.setDouble(4, 75);
                prep.setDouble(5, 80);
                prep.setTimestamp(6, sqlTimeStamp);

                int count = prep.executeUpdate();
                System.out.println("Inserted: " + count + " row(s)");
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```
## <a name="update-data"></a>更新数据

使用[准备的语句](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql)和 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 语句更新 Azure SQL 数据库中的数据。

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "yourserver";
        String dbName = "yourdatabase";
        String user = "yourusername";
        String password = "yourpassword";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Update data example:");
                System.out.println("=========================================");

                // Prepared statement to update data
                String updateSql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?";

                PreparedStatement prep = connection.prepareStatement(updateSql);
                prep.setString(1, "500");
                prep.setString(2, "BrandNewProduct");

                int count = prep.executeUpdate();
                System.out.println("Updated: " + count + " row(s)")
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```



## <a name="delete-data"></a>删除数据

使用[准备的语句](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql)和 [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 语句删除 Azure SQL 数据库中的数据。

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "yourserver";
        String dbName = "yourdatabase";
        String user = "yourusername";
        String password = "yourpassword";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Delete data example:");
                System.out.println("=========================================");

                // Prepared statement to delete data
                String deleteSql = "DELETE SalesLT.Product WHERE Name = ?";

                PreparedStatement prep = connection.prepareStatement(deleteSql);
                prep.setString(1, "BrandNewProduct");

                int count = prep.executeUpdate();
                System.out.println("Deleted: " + count + " row(s)");
            }        
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## <a name="next-steps"></a>后续步骤
* 请参阅 [SQL 数据库开发概述](sql-database-develop-overview.md)。
* [用于 SQL Server 的 Microsoft JDBC 驱动程序](https://github.com/microsoft/mssql-jdbc)的 GitHub 存储库。
* [提出问题](https://github.com/microsoft/mssql-jdbc/issues)。
* 浏览所有 [SQL 数据库的功能](https://azure.microsoft.com/services/sql-database/)。


