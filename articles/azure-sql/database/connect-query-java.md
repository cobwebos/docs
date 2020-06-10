---
title: 使用 Java 查询数据库
description: 介绍如何使用 Java 创建连接到 Azure SQL 数据库中数据库或 Azure SQL 托管实例的程序，并使用 T-SQL 语句对其进行查询。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: seo-java-july2019. seo-java-august2019, sqldbrb=2 
ms.openlocfilehash: 6be52d2d3472888607bbd6276b4794184bb11273
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267384"
---
# <a name="quickstart-use-java-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>快速入门：使用 Java 查询 Azure SQL 数据库中的数据库或 Azure SQL 托管实例
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

在本快速入门中，你将使用 Java 连接到 Azure SQL 数据库中的数据库或 Azure SQL 托管实例，并使用 T-SQL 语句来查询数据。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

  || SQL 数据库 | SQL 托管实例 | Azure VM 上的 SQL Server |
  |:--- |:--- |:---|:---|
  | 创建| [门户](single-database-create-quickstart.md) | [门户](../managed-instance/instance-create-quickstart.md) | [门户](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | 配置 | [服务器级别 IP 防火墙规则](firewall-create-server-level-portal-quickstart.md)| [从 VM 进行连接](../managed-instance/connect-vm-instance-configure.md)|
  |||[从本地建立的连接](../managed-instance/point-to-site-p2s-configure.md) | [连接到 SQL Server 实例](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |加载数据|根据快速入门加载的 Adventure Works|[还原 Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [还原 Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||从 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 的一个 [BACPAC](database-import.md) 文件还原或导入 Adventure Works| 从 [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 的一个 [BACPAC](database-import.md) 文件还原或导入 Adventure Works|
  |||

- [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) 相关的软件

  # <a name="macos"></a>[macOS](#tab/macos)

  安装 Homebrew 和 Java，然后使用[在 macOS 上使用 SQL Server 创建 Java 应用](https://www.microsoft.com/sql-server/developer-get-started/java/mac/)中的步骤 1.2 和 1.3 安装 Maven 。

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  安装 Java 和 Java 开发工具包，然后使用[在 Ubuntu 上使用 SQL Server 创建 Java 应用](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/)中的步骤 1.2、1.3 和 1.4 安装 Maven  。

  # <a name="windows"></a>[Windows](#tab/windows)

  安装 Java，然后使用[在 Windows 上使用 SQL Server 创建 Java 应用](https://www.microsoft.com/sql-server/developer-get-started/java/windows/)中的步骤 1.2 和 1.3 安装 Maven 。

  ---

> [!IMPORTANT]
> 本文中脚本的编写目的是使用 **Adventure Works** 数据库。

> [!NOTE]
> 可以选择使用 Azure SQL 托管实例。
>
> 若要执行创建和配置操作，请使用 [Azure 门户](../managed-instance/instance-create-quickstart.md)、[PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) 或 [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)，然后设置[本地](../managed-instance/point-to-site-p2s-configure.md)或 [VM](../managed-instance/connect-vm-instance-configure.md) 连接性。
>
> 若要加载数据，请参阅[通过 BACPAC 进行还原](database-import.md)（使用 [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 文件），或参阅[还原 Wide World Importers 数据库](../managed-instance/restore-sample-database-quickstart.md)。

## <a name="get-server-connection-information"></a>获取服务器连接信息

获取连接到 Azure SQL 数据库中的数据库所需的连接信息。 在后续过程中，将需要完全限定的服务器名称或主机名称、数据库名称和登录信息。

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 选择“SQL 数据库”或打开“SQL 托管实例”页。

3. 在“概述”页上，在“Server 名称”旁查看 Azure SQL 数据库中的数据库的完全限定服务器名称，或在“Host”旁边查看 Azure VM 上的 Azure SQL 托管实例或 SQL Server 的完全限定服务器名称（或 IP 地址）  。 若要复制服务器名称或主机名称，请将鼠标悬停在其上方，然后选择“复制”图标。

> [!NOTE]
> 有关 Azure VM 上的 SQL Server 的连接信息，请参阅[连接到 SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)。

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

## <a name="add-code-to-query-the-database"></a>添加用于查询数据库的代码

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
   > 代码示例使用 Azure SQL 数据库中的 AdventureWorksLT 示例数据库。

## <a name="run-the-code"></a>运行代码

1. 在命令提示符下运行此应用。

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. 验证是否返回了前 20 行，然后关闭应用窗口。

## <a name="next-steps"></a>后续步骤

- [在 Azure SQL 数据库中设计第一个数据库](design-first-database-tutorial.md)  
- [用于 SQL Server 的 Microsoft JDBC 驱动程序](https://github.com/microsoft/mssql-jdbc)  
- [报告问题/提出问题](https://github.com/microsoft/mssql-jdbc/issues)  
