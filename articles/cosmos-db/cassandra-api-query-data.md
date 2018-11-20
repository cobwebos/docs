---
title: 从 Azure Cosmos DB Cassandra API 帐户查询数据
description: 本文介绍如何使用 Java 应用程序从 Azure Cosmos DB Cassandra API 帐户查询用户数据。
services: cosmos-db
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: a06e7e6159953bfeffa966759d29b91bbcbafd37
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739203"
---
# <a name="tutorial-query-data-from-an-azure-cosmos-db-cassandra-api-account"></a>教程：从 Azure Cosmos DB Cassandra API 帐户查询数据

本教程介绍如何使用 Java 应用程序从 Azure Cosmos DB Cassandra API 帐户查询用户数据。 Java 应用程序使用 [Java 驱动程序](https://github.com/datastax/java-driver)并查询用户 ID、用户名、用户城市等用户数据。 

本教程涵盖以下任务：

> [!div class="checklist"]
> * 从 Cassandra 表查询数据
> * 运行应用

## <a name="prerequisites"></a>先决条件

* 本文是由多个部分组成的教程。 在开始之前，请务必完成前面的步骤以[创建 Cassandra API 帐户、密钥空间、表并[将示例数据加载到表](cassandra-api-load-data.md)。 

## <a name="query-data"></a>查询数据

通过以下步骤从 Cassandra API 帐户查询数据：

1. 打开 `src\main\java\com\azure\cosmosdb\cassandra` 文件夹下的 `UserRepository.java` 文件。 追加以下代码块。 此代码提供三种方法： 

   * 查询数据库中的所有用户
   * 查询按用户 ID 筛选的特定用户
   * 删除表。

   ```java
   /**
   * Select all rows from user table
   */
   public void selectAllUsers() {

     final String query = "SELECT * FROM uprofile.user";
     List<Row> rows = session.execute(query).all();

     for (Row row : rows) {
        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
     }
   }

   /**
   * Select a row from user table
   *
   * @param id user_id
   */
   public void selectUser(int id) {
      final String query = "SELECT * FROM uprofile.user where user_id = 3";
      Row row = session.execute(query).one();

      LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
   }

   /**
   * Delete user table.
   */
   public void deleteTable() {
     final String query = "DROP TABLE IF EXISTS uprofile.user";
     session.execute(query);
   }
   ```

2. 打开 `src\main\java\com\azure\cosmosdb\cassandra` 文件夹下的 `UserProfile.java` 文件。 此类包含调用你以前定义的插入数据方法 createKeyspace 和 createTable 的主方法。 现在追加查询所有用户或特定用户的以下代码：

   ```java
   LOGGER.info("Select all users");
   repository.selectAllUsers();

   LOGGER.info("Select a user by id (3)");
   repository.selectUser(3);

   LOGGER.info("Delete the users profile table");
   repository.deleteTable();
   ```

## <a name="run-the-java-app"></a>运行 Java 应用
1. 打开命令提示符或终端窗口。 粘贴以下代码块。 

   此代码将目录 (cd) 更改为在其中创建项目的文件夹路径。 接着，它将运行 `mvn clean install` 命令以在目标文件夹中生成 `cosmosdb-cassandra-examples.jar` 文件。 最后，它运行 Java 应用程序。

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. 现在，在 Azure 门户中，打开“数据资源管理器”并确认用户表已删除。

## <a name="next-steps"></a>后续步骤

* 在本教程中，你已了解如何从 Azure Cosmos DB Cassandra API 帐户查询数据。 你现在可以继续学习下一篇文章：

> [!div class="nextstepaction"]
> [将数据迁移到 Cassandra API 帐户](cassandra-import-data.md)


