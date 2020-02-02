---
title: 使用 Java 查询数据库
description: 介绍如何使用 Java 创建连接到 Azure SQL 数据库的程序并使用 T-SQL 语句对其进行查询。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-java-july2019. seo-java-august2019
ms.openlocfilehash: 034f92ca3b7552373ae69148d09d58d3a5dd166a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768645"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>快速入门：使用 Java 查询 Azure SQL 数据库

在本快速入门中，你将使用 Java 连接到 Azure SQL 数据库，并使用 T-SQL 语句来查询数据。

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 一个 [Azure SQL 数据库](sql-database-single-database-get-started.md)
- [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) 相关的软件

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  安装 Homebrew 和 Java，然后使用[在 macOS 上创建使用 SQL Server 的 Java 应用](https://www.microsoft.com/sql-server/developer-get-started/java/mac/)中的步骤 **1.2** 和 **1.3** 安装 Maven。

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  安装 Java 和 Java 开发工具包，然后使用[在 Ubuntu 上创建使用 SQL Server 的 Java 应用](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/)中的步骤 **1.2**、**1.3** 和 **1.4** 安装 Maven。

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  安装 Java，然后使用[在 Windows 上创建使用 SQL Server 的 Java 应用](https://www.microsoft.com/sql-server/developer-get-started/java/windows/)中的步骤 **1.2** 和 **1.3** 安装 Maven。

  ---

> [!IMPORTANT]
> 本文中脚本的编写目的是使用 **Adventure Works** 数据库。

> [!NOTE]
> 可以选择使用 Azure SQL 托管实例。
>
> 若要执行创建和配置操作，请使用 [Azure 门户](sql-database-managed-instance-get-started.md)、[PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) 或 [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)，然后设置[现场](sql-database-managed-instance-configure-p2s.md)或 [VM](sql-database-managed-instance-configure-vm.md) 连接性。
>
> 若要加载数据，请参阅[通过 BACPAC 进行还原](sql-database-import.md)（使用 [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 文件），或参阅[还原 Wide World Importers 数据库](sql-database-managed-instance-get-started-restore.md)。

## <a name="get-sql-server-connection-information"></a>获取 SQL Server 连接信息

获取连接到 Azure SQL 数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称或主机名称、数据库名称和登录信息。

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 选择“SQL 数据库”  或打开“SQL 托管实例”  页。

3. 在“概览”页中，查看单一数据库的“服务器名称”旁边的完全限定的服务器名称，或者托管实例的“主机”旁边的完全限定的服务器名称    。 若要复制服务器名称或主机名称，请将鼠标悬停在其上方，然后选择“复制”图标  。 

## <a name="create-the-project"></a>创建项目

1. 从命令提示符创建名为 sqltest 的新 Maven 项目。 

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. 将文件夹更改为 *sqltest*，然后使用喜欢的文本编辑器打开 pom.xml  。 使用以下代码，将 **Microsoft JDBC Driver for SQL Server** 添加到项目的依赖项。

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. 另请在 pom.xml  中向项目添加以下属性。 如果没有 properties 节，可以将其添加到 dependencies 后面。

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. 保存并关闭  pom.xml。

## <a name="add-code-to-query-database"></a>添加用于查询数据库的代码

1. 此时，你应该在 Maven 项目中有了一个名为  App.java 的文件，其位置为：

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
