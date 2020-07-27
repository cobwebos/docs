---
title: 在 Azure SQL 数据库中使用 Java 和 JDBC
description: 了解如何将 Java 和 JDBC 用于 Azure SQL 数据库。
services: sql-database
author: jdubois
ms.author: judubois
ms.service: sql-database
ms.subservice: development
ms.topic: quickstart
ms.devlang: java
ms.date: 06/26/2020
ms.openlocfilehash: 59124928e9bfb75265e3556e37d65a3b30c851d3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515069"
---
# <a name="use-java-and-jdbc-with--azure-sql-database"></a>在 Azure SQL 数据库中使用 Java 和 JDBC

本主题演示如何创建示例应用程序，使其使用 Java 和 [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) 在 [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) 中存储和检索信息。

JDBC 是标准的 Java API，用于连接到传统的关系数据库。

## <a name="prerequisites"></a>先决条件

- 一个 Azure 帐户。 如果没有帐户，可[获取一个免费试用帐户](https://azure.microsoft.com/free/)。
- [Azure Cloud Shell](/azure/cloud-shell/quickstart) 或 [Azure CLI](/cli/azure/install-azure-cli)。 建议使用 Azure Cloud Shell，这样你便可自动登录且有权访问所需的所有工具。
- 受支持的 [Java 开发工具包](https://aka.ms/azure-jdks)，版本 8（已在 Azure Cloud Shell 中包括）。
- [Apache Maven](https://maven.apache.org/) 生成工具。

## <a name="prepare-the-working-environment"></a>准备工作环境

我们将使用环境变量来减少键入错误，并使你能够更轻松地根据特定需求自定义以下配置。

使用以下命令设置这些环境变量：

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_SQL_SERVER_USERNAME=demo
AZ_SQL_SERVER_PASSWORD=<YOUR_AZURE_SQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

使用以下值替换占位符，在本文中将使用这些值：

- `<YOUR_DATABASE_NAME>`：Azure SQL 数据库服务器的名称。 它在 Azure 中应是唯一的。
- `<YOUR_AZURE_REGION>`：将使用的 Azure 区域。 默认情况下可以使用 `eastus`，但我们建议你配置一个离居住位置更近的区域。 可输入 `az account list-locations`，获取可用区域的完整列表。
- `<AZ_SQL_SERVER_PASSWORD>`：Azure SQL 数据库服务器的密码。 该密码应该至少有八个字符。 这些字符应该属于以下类别中的三个类别：英文大写字母、英文小写字母、数字 (0-9)和非字母数字字符（!, $, #, % 等）。
- `<YOUR_LOCAL_IP_ADDRESS>`：你将在其中运行 Java 应用程序的本地计算机的 IP 地址。 若要找到该地址，一种简便方法是使浏览器指向 [whatismyip.akamai.com](http://whatismyip.akamai.com/)。

接下来，使用以下命令创建资源组：

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
  	| jq
```

> [!NOTE]
> 我们使用 `jq` 实用工具来显示 JSON 数据，使其更易于阅读。 默认情况下，此实用工具安装在 [Azure Cloud Shell](https://shell.azure.com/) 上。 如果你不喜欢该实用工具，可安全地删除要使用的所有命令的 `| jq` 部分。

## <a name="create-an-azure-sql-database-instance"></a>创建 Azure SQL 数据库实例

首先，我们将创建一个托管的 Azure SQL 数据库服务器。

> [!NOTE]
> 可以在[快速入门：创建 Azure SQL 数据库单一数据库](/azure/sql-database/sql-database-single-database-get-started)中阅读有关创建 Azure SQL 数据库服务器的更多详细信息。

在 [Azure Cloud Shell](https://shell.azure.com/) 中运行以下命令：

```azurecli
az sql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --admin-user $AZ_SQL_SERVER_USERNAME \
    --admin-password $AZ_SQL_SERVER_PASSWORD \
  	| jq
```

此命令创建 Azure SQL 数据库服务器。

### <a name="configure-a-firewall-rule-for-your-azure-sql-database-server"></a>为 Azure SQL 数据库服务器配置防火墙规则

Azure SQL 数据库实例在默认情况下受保护。 它们有不允许任何传入连接的防火墙。 为了能够使用数据库，需要添加一项防火墙规则，允许本地 IP 地址访问数据库服务器。

由于我们已在本文开头配置了本地 IP 地址，因此你可通过运行以下命令来打开服务器的防火墙：

```azurecli
az sql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
  	| jq
```

### <a name="configure-a-azure-sql-database"></a>配置 Azure SQL 数据库

你在前面创建的 Azure SQL 数据库服务器为空。 它没有任何可以与 Java 应用程序配合使用的数据库。 通过运行以下命令创建名为 `demo` 的新数据库：

```azurecli
az sql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server $AZ_DATABASE_NAME \
  	| jq
```

### <a name="create-a-new-java-project"></a>新建一个 Java 项目

使用最喜欢的 IDE 创建一个新的 Java 项目，并在其根目录中添加一个 `pom.xml` 文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>

    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.sqlserver</groupId>
            <artifactId>mssql-jdbc</artifactId>
            <version>7.4.1.jre8</version>
        </dependency>
    </dependencies>
</project>
```

此文件是一个 [Apache Maven](https://maven.apache.org/)，用于配置要使用的项目：

- Java 8
- 适用于 Java 的最近的 SQL Server 驱动程序

### <a name="prepare-a-configuration-file-to-connect-to-azure-sql-database"></a>准备用于连接到 Azure SQL 数据库的配置文件

创建 src/main/resources/application.properties 文件，添加以下内容：

```properties
url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
user=demo@$AZ_DATABASE_NAME
password=$AZ_SQL_SERVER_PASSWORD
```

- 将两个 `$AZ_DATABASE_NAME` 变量替换为在本文开头部分配置的值。
- 将 `$AZ_SQL_SERVER_PASSWORD` 变量替换为在本文开头部分配置的值。

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>创建 SQL 文件以生成数据库架构

我们将使用 src/main/resources/`schema.sql` 文件来创建数据库架构。 创建包含以下内容的文件：

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id INT PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BIT);
```

## <a name="code-the-application"></a>编写应用程序代码

### <a name="connect-to-the-database"></a>连接到数据库

接下来添加 Java 代码，该代码使用 JDBC 在 Azure SQL 数据库中存储和检索数据。

创建 src/main/java/DemoApplication.java 文件，其中包含：

```java
package com.example.demo;

import java.sql.*;
import java.util.*;
import java.util.logging.Logger;

public class DemoApplication {

    private static final Logger log;

    static {
        System.setProperty("java.util.logging.SimpleFormatter.format", "[%4$-7s] %5$s %n");
        log =Logger.getLogger(DemoApplication.class.getName());
    }

    public static void main(String[] args) throws Exception {
        log.info("Loading application properties");
        Properties properties = new Properties();
        properties.load(DemoApplication.class.getClassLoader().getResourceAsStream("application.properties"));

        log.info("Connecting to the database");
        Connection connection = DriverManager.getConnection(properties.getProperty("url"), properties);
        log.info("Database connection test: " + connection.getCatalog());

        log.info("Create database schema");
        Scanner scanner = new Scanner(DemoApplication.class.getClassLoader().getResourceAsStream("schema.sql"));
        Statement statement = connection.createStatement();
        while (scanner.hasNextLine()) {
            statement.execute(scanner.nextLine());
        }

        /*
        Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
        insertData(todo, connection);
        todo = readData(connection);
        todo.setDetails("congratulations, you have updated data!");
        updateData(todo, connection);
        deleteData(todo, connection);
        */

        log.info("Closing database connection");
        connection.close();
    }
}
```

此 Java 代码将使用之前创建的 application.properties 和 schema.sql 文件 ，来连接到 SQL Server 数据库并创建用于存储数据的架构。

在此文件中，你可以看到，我们注释了用于插入、读取、更新和删除数据的方法：我们将在本文的其余部分对这些方法进行编码，并且你将能够逐个取消注释。

> [!NOTE]
> 数据库凭据存储在“application.properties”文件的“user”和“password”属性中  。 执行 `DriverManager.getConnection(properties.getProperty("url"), properties);` 时使用这些凭据，因为属性文件是作为参数传递的。

现在可以通过喜欢的工具执行此主类：

- 使用你的 IDE，你应该能够右键单击 DemoApplication 类并执行它。
- 使用 Maven，可以通过执行以下操作来运行应用程序：`mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"`。

应用程序应连接到 Azure SQL 数据库，创建数据库架构，然后关闭连接，如控制台日志中所示：

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Closing database connection 
```

### <a name="create-a-domain-class"></a>创建域类

在 `DemoApplication` 类旁创建新的 `Todo` Java 类并添加以下代码：

```java
package com.example.demo;

public class Todo {

    private Long id;
    private String description;
    private String details;
    private boolean done;

    public Todo() {
    }

    public Todo(Long id, String description, String details, boolean done) {
        this.id = id;
        this.description = description;
        this.details = details;
        this.done = done;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }

    @Override
    public String toString() {
        return "Todo{" +
                "id=" + id +
                ", description='" + description + '\'' +
                ", details='" + details + '\'' +
                ", done=" + done +
                '}';
    }
}
```

此类是在执行 schema .sql 脚本时创建的 `todo` 表上映射的域模型。

### <a name="insert-data-into-azure-sql-database"></a>将数据插入 Azure SQL 数据库

在 src/main/java/DemoApplication.java 文件中，在 main 方法之后添加以下方法，以将数据插入数据库：

```java
private static void insertData(Todo todo, Connection connection) throws SQLException {
    log.info("Insert data");
    PreparedStatement insertStatement = connection
            .prepareStatement("INSERT INTO todo (id, description, details, done) VALUES (?, ?, ?, ?);");

    insertStatement.setLong(1, todo.getId());
    insertStatement.setString(2, todo.getDescription());
    insertStatement.setString(3, todo.getDetails());
    insertStatement.setBoolean(4, todo.isDone());
    insertStatement.executeUpdate();
}
```

你现在可以对 `main` 方法中的以下两行执行取消注释操作：

```java
Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
insertData(todo, connection);
```

现在，执行主类应会生成以下输出：

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Closing database connection
```

### <a name="reading-data-from-azure-sql-database"></a>从 Azure SQL 数据库读取数据

接下来，请阅读前面插入的数据，验证代码是否正常工作。

在 src/main/java/DemoApplication.java 文件中，在 `insertData` 方法之后添加以下方法，以从数据库中读取数据：

```java
private static Todo readData(Connection connection) throws SQLException {
    log.info("Read data");
    PreparedStatement readStatement = connection.prepareStatement("SELECT * FROM todo;");
    ResultSet resultSet = readStatement.executeQuery();
    if (!resultSet.next()) {
        log.info("There is no data in the database!");
        return null;
    }
    Todo todo = new Todo();
    todo.setId(resultSet.getLong("id"));
    todo.setDescription(resultSet.getString("description"));
    todo.setDetails(resultSet.getString("details"));
    todo.setDone(resultSet.getBoolean("done"));
    log.info("Data read from the database: " + todo.toString());
    return todo;
}
```

你现在可以对 `main` 方法中的以下行执行取消注释操作：

```java
todo = readData(connection);
```

现在，执行主类应会生成以下输出：

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="updating-data-in-azure-sql-database"></a>更新 Azure SQL 数据库中的数据

让我们更新之前插入的数据。

在 src/main/java/DemoApplication.java 文件中，在 `readData` 方法之后添加以下方法，以更新数据库中的数据：

```java
private static void updateData(Todo todo, Connection connection) throws SQLException {
    log.info("Update data");
    PreparedStatement updateStatement = connection
            .prepareStatement("UPDATE todo SET description = ?, details = ?, done = ? WHERE id = ?;");

    updateStatement.setString(1, todo.getDescription());
    updateStatement.setString(2, todo.getDetails());
    updateStatement.setBoolean(3, todo.isDone());
    updateStatement.setLong(4, todo.getId());
    updateStatement.executeUpdate();
    readData(connection);
}
```

你现在可以对 `main` 方法中的以下两行执行取消注释操作：

```java
todo.setDetails("congratulations, you have updated data!");
updateData(todo, connection);
```

现在，执行主类应会生成以下输出：

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="deleting-data-in-azure-sql-database"></a>删除 Azure SQL 数据库中的数据

最后，让我们删除之前插入的数据。

在 src/main/java/DemoApplication.java 文件中，在 `updateData` 方法之后添加以下方法，以删除数据库中的数据：

```java
private static void deleteData(Todo todo, Connection connection) throws SQLException {
    log.info("Delete data");
    PreparedStatement deleteStatement = connection.prepareStatement("DELETE FROM todo WHERE id = ?;");
    deleteStatement.setLong(1, todo.getId());
    deleteStatement.executeUpdate();
    readData(connection);
}
```

你现在可以对 `main` 方法中的以下行执行取消注释操作：

```java
deleteData(todo, connection);
```

现在，执行主类应会生成以下输出：

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Delete data 
[INFO   ] Read data 
[INFO   ] There is no data in the database! 
[INFO   ] Closing database connection 
```

## <a name="conclusion-and-resources-clean-up"></a>结论和资源清除

祝贺你！ 你已创建了一个 Java 应用程序，该应用程序使用 JDBC 在 Azure SQL Database 中存储和检索数据。

若要清理本快速入门中使用的所有资源，请使用以下命令删除该资源组：

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>后续步骤

- [在 Azure SQL 数据库中设计第一个数据库](design-first-database-tutorial.md)  
- [用于 SQL Server 的 Microsoft JDBC 驱动程序](https://github.com/microsoft/mssql-jdbc)  
- [报告问题/提出问题](https://github.com/microsoft/mssql-jdbc/issues)  
