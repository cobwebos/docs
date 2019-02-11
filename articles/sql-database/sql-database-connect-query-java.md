---
title: 使用 Java 查询 Azure SQL 数据库 | Microsoft Docs
description: 介绍如何使用 Java 创建连接到 Azure SQL 数据库的程序并使用 T-SQL 语句对其进行查询。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
manager: craigg
ms.date: 12/01/2018
ms.openlocfilehash: 3a036ac1260923a5030b8b0c3345482346c183fe
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2019
ms.locfileid: "55563112"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>快速入门：使用 Java 查询 Azure SQL 数据库

本文演示了如何使用 [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) 连接到 Azure SQL 数据库。 然后即可使用 T-SQL 语句来查询数据。

## <a name="prerequisites"></a>先决条件

若要完成此示例，请确保具备以下先决条件：

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- 已为操作系统安装与 Java 相关的软件：

  - **MacOS**：安装 Homebrew 和 Java，然后安装 Maven。 请参阅[步骤 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/mac/)。

  - **Ubuntu**：安装 Java 和 Java 开发工具包，然后安装 Maven。 请参阅[步骤 1.2、1.3 和 1.4](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/)。

  - **Windows**：安装 Java，然后安装 Maven。 请参阅[步骤 1.2 和 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/windows/)。

## <a name="get-database-connection"></a>获取数据库连接

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-the-project"></a>创建项目

1. 从命令提示符创建名为 sqltest 的新 Maven 项目。

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. 将文件夹更改为 *sqltest*，然后使用喜欢的文本编辑器打开 pom.xml。 使用以下代码，将 **Microsoft JDBC Driver for SQL Server** 添加到项目的依赖项。

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. 另请在 pom.xml 中向项目添加以下属性。 如果没有 properties 节，可以将其添加到 dependencies 后面。

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. 保存并关闭 pom.xml。

## <a name="add-code-to-query-database"></a>添加用于查询数据库的代码

1. 此时，你应该在 Maven 项目中有了一个名为 App.java 的文件，其位置为：

   *..\sqltest\src\main\java\com\sqldbsamples\App.java*

1. 打开该文件并将其内容替换为以下代码。 然后，为服务器、数据库、用户和密码添加相应的值。

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
            String hostName = "your_server.database.windows.net"; // update me
            String dbName = "your_database"; // update me
            String user = "your_username"; // update me
            String password = "your_password"; // update me
            String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;"
                + "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
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

                try (Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql)) {

                    // Print results from select statement
                    System.out.println("Top 20 categories:");
                    while (resultSet.next())
                    {
                        System.out.println(resultSet.getString(1) + " "
                            + resultSet.getString(2));
                    }
                    connection.close();
                }
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    ```

   > [!NOTE]
   > 代码示例使用适用于 Azure SQL 的 **AdventureWorksLT** 示例数据库。

## <a name="run-the-code"></a>运行代码

1. 在命令提示符下运行此应用。

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. 验证是否返回了前 20 行，然后关闭应用窗口。

## <a name="next-steps"></a>后续步骤

- [设计第一个 Azure SQL 数据库](sql-database-design-first-database.md)  

- [用于 SQL Server 的 Microsoft JDBC 驱动程序](https://github.com/microsoft/mssql-jdbc)  

- [报告问题/提出问题](https://github.com/microsoft/mssql-jdbc/issues)  
