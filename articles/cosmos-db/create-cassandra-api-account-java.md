---
title: 教程：生成 Java 应用以创建 Azure Cosmos DB Cassandra API 帐户
description: 本教程介绍如何使用 Java 应用程序创建 Cassandra API 帐户，以及如何向该帐户添加数据库（也称键空间）和表。
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 4122e575fc7823fd2e79a26f210f06e5c1b0a835
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857987"
---
# <a name="tutorial-create-a-cassandra-api-account-in-azure-cosmos-db-by-using-a-java-application-to-store-keyvalue-data"></a>教程：使用 Java 应用程序在 Azure Cosmos DB 中创建 Cassandra API 帐户，以便存储键/值数据

作为开发人员，你可能具有使用键/值对的应用程序。 可以在 Azure Cosmos DB 中使用 Cassandra API 帐户来存储键/值数据。 本教程介绍如何使用 Java 应用程序在 Azure Cosmos DB 中创建 Cassandra API 帐户、添加数据库（也称为键空间）以及添加表。 Java 应用程序使用 [Java 驱动程序](https://github.com/datastax/java-driver)来创建包含用户 ID、用户名、用户城市等详细信息的用户数据库。  

本教程涵盖以下任务：

> [!div class="checklist"]
> * 创建 Cassandra 数据库帐户
> * 获取帐户连接字符串
> * 创建 Maven 项目和依赖项
> * 添加数据库和表
> * 运行应用

## <a name="prerequisites"></a>先决条件 

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 

* 获取最新版本的 [Java 开发工具包 (JDK)](/java/azure/jdk/?view=azure-java-stable)。 

* [下载](https://maven.apache.org/download.cgi)和[安装](https://maven.apache.org/install.html)[Maven](https://maven.apache.org/) 二进制存档。 
  - 在 Ubuntu 上，可以通过运行 `apt-get install maven` 来安装 Maven。 

## <a name="create-a-database-account"></a>创建数据库帐户 

1. 登录 [Azure 门户](https://portal.azure.com/)。 

2. 选择“创建资源”   >   “数据库” >   “Azure Cosmos DB”。 

3. 在“新建帐户”窗格上，输入新 Azure Cosmos 帐户的设置。  

   |设置   |建议的值  |说明  |
   |---------|---------|---------|
   |ID   |   输入唯一的名称    | 输入标识此 Azure Cosmos 帐户的唯一名称。 <br/><br/>由于 cassandra.cosmosdb.azure.com 将追加到所提供的用于创建接触点的 ID 后面，因此，请使用唯一但可识别的 ID。         |
   |API    |  Cassandra   |  API 确定要创建的帐户的类型。 <br/> 选择“Cassandra”，因为在本文中，你将创建可使用 Cassandra 查询语言 (CQL) 语法查询的宽列数据库  。  |
   |订阅    |  订阅        |  选择要用于此 Azure Cosmos 帐户的 Azure 订阅。        |
   |资源组   | 输入名称    |  选择“新建”，然后输入帐户的新资源组名称  。 为简单起见，可以使用与 ID 相同的名称。    |
   |位置    |  选择离用户最近的区域    |  选择要在其中托管 Azure Cosmos 帐户的地理位置。 使用离用户最近的位置，使他们能够以最快的速度访问数据。    |

   ![使用门户创建帐户](./media/create-cassandra-api-account-java/create-account.png)

4. 选择“创建”  。 <br/>创建帐户需要几分钟时间。 创建资源后，可以在门户右侧看到“部署成功”  通知。

## <a name="get-the-connection-details-of-your-account"></a>获取帐户的连接详细信息  

从 Azure 门户获取连接字符串信息，并将其复制到 Java 配置文件。 连接字符串使应用能与托管数据库进行通信。 

1. 从 [Azure 门户](https://portal.azure.com/)转到 Azure Cosmos 帐户。 

2. 打开“连接字符串”  窗格。  

3. 复制接触点、端口、用户名以及主密码值，以便在后续步骤中使用     。

## <a name="create-the-project-and-the-dependencies"></a>创建项目和依赖项 

本文中使用的 Java 示例项目托管在 GitHub 中。 可以运行本文档中的步骤，也可以从 [azure-cosmos-db-cassandra-java-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started) 存储库下载示例。 

下载文件后，更新 `java-examples\src\main\resources\config.properties` 文件中的连接字符串信息并运行它。  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

若要从头生成示例，请执行以下步骤： 

1. 在终端窗口或命令提示符中，创建名为 Cassandra-demo 的新 Maven 项目。 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. 找到 `cassandra-demo` 文件夹。 使用文本编辑器打开已生成的 `pom.xml` 文件。 

   添加 Cassandra 依赖项并生成项目所需的插件，如 [pom.xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/pom.xml) 文件中所示。  

3. 在 `cassandra-demo\src\main` 文件夹下，创建名为 `resources` 的新文件夹。  在资源文件夹下，添加 config.properties 和 log4j.properties 文件：

   - [Config.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/src/main/resources/config.properties) 文件存储 Cassandra API 帐户的连接终结点和键值。 
   
   - [log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/src/main/resources/log4j.properties) 文件定义与 Cassandra API 交互所需的日志记录级别。  

4. 浏览到 `src/main/java/com/azure/cosmosdb/cassandra/` 文件夹。 在 cassandra 文件夹中，另创建一个名为 `utils` 的文件夹。 新的文件夹存储连接到 Cassandra API 帐户所需的实用程序类。 

   添加 [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) 类来创建群集并打开和关闭 Cassandra 会话。 群集将连接到 Azure Cosmos DB 中的 Cassandra API 帐户并返回可供访问的会话。 使用 [Configurations](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) 类从 config.properties 文件中读取连接字符串信息。 

5. Java 示例使用用户信息（如用户名、用户ID、用户城市）创建数据库。 你需要定义 get 和 set 方法以访问主函数中的用户详细信息。
 
   使用 get 和 set 方法在 `src/main/java/com/azure/cosmosdb/cassandra/` 文件夹下创建 [User.java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/src/main/java/com/azure/cosmosdb/cassandra/examples/UserProfile.java) 类。 

## <a name="add-a-database-and-a-table"></a>添加数据库和表  

本部分介绍如何添加数据库（键空间）和表，方法是使用 CQL。

1. 在 `src\main\java\com\azure\cosmosdb\cassandra` 文件夹下，创建名为 `repository` 的新文件夹。 

2. 创建 `UserRepository` Java 类，并向其添加以下代码： 

   ```java
   package com.azure.cosmosdb.cassandra.repository; 
   import java.util.List; 
   import com.datastax.driver.core.BoundStatement; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Row; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Create a Cassandra session 
    */ 
   public class UserRepository { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserRepository.class); 
       private Session session; 
       public UserRepository(Session session) { 
           this.session = session; 
       } 
   
       /** 
       * Create keyspace uprofile in cassandra DB 
        */ 
   
       public void createKeyspace() { 
            final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"; 
           session.execute(query); 
           LOGGER.info("Created keyspace 'uprofile'"); 
       } 
   
       /** 
        * Create user table in cassandra DB 
        */ 
   
       public void createTable() { 
           final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"; 
           session.execute(query); 
           LOGGER.info("Created table 'user'"); 
       } 
   } 
   ```

3. 找到 `src\main\java\com\azure\cosmosdb\cassandra` 文件夹，并创建名为 `examples` 的新的子文件夹。

4. 创建 `UserProfile` Java 类。 此类包含调用前面定义的 createKeyspace 和 createTable 方法的主方法： 

   ```java
   package com.azure.cosmosdb.cassandra.examples; 
   import java.io.IOException; 
   import com.azure.cosmosdb.cassandra.repository.UserRepository; 
   import com.azure.cosmosdb.cassandra.util.CassandraUtils; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Example class which will demonstrate following operations on Cassandra Database on CosmosDB 
    * - Create Keyspace 
    * - Create Table 
    * - Insert Rows 
    * - Select all data from a table 
    * - Select a row from a table 
    */ 
   
   public class UserProfile { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserProfile.class); 
       public static void main(String[] s) throws Exception { 
           CassandraUtils utils = new CassandraUtils(); 
           Session cassandraSession = utils.getSession(); 
   
           try { 
               UserRepository repository = new UserRepository(cassandraSession); 
               //Create keyspace in cassandra database 
               repository.createKeyspace(); 
               //Create table in cassandra database 
               repository.createTable(); 
   
           } finally { 
               utils.close(); 
               LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL"); 
           } 
       } 
   } 
   ```
 
## <a name="run-the-app"></a>运行应用 

1. 打开命令提示符或终端窗口。 粘贴以下代码块。 

   此代码将目录 (cd) 更改为在其中创建项目的文件夹路径。 接着，它将运行 `mvn clean install` 命令以在目标文件夹中生成 `cosmosdb-cassandra-examples.jar` 文件。 最后，它运行 Java 应用程序。

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   终端窗口会显示通知，指出密钥空间和表已创建。 
   
2. 现在在 Azure 门户中，打开“数据资源管理器”以确认是否已创建键空间和表  。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用 Java 应用程序在 Azure Cosmos DB 中创建 Cassandra API 帐户，以及创建数据库和表。 你现在可以继续学习下一篇文章：

> [!div class="nextstepaction"]
> [将示例数据加载到 Cassandra API 表](cassandra-api-load-data.md)。
