---
title: 教程：从 Azure Cosmos DB 中的 Cassandra API 帐户查询数据
description: 本教程介绍如何使用 Java 应用程序从 Azure Cosmos DB Cassandra API 帐户查询用户数据。
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 28bb69dc21702a7c98755dc07389ea4147848f7a
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853034"
---
# <a name="tutorial-query-data-from-a-cassandra-api-account-in-azure-cosmos-db"></a>教程：从 Azure Cosmos DB 中的 Cassandra API 帐户查询数据

作为开发人员，你可能具有使用键/值对的应用程序。 可以使用 Azure Cosmos DB 中的 Cassandra API 帐户来存储和查询键/值数据。 本教程介绍如何使用 Java 应用程序从 Azure Cosmos DB 中的 Cassandra API 帐户查询用户数据。 Java 应用程序使用 [Java 驱动程序](https://github.com/datastax/java-driver)并查询用户 ID、用户名和用户城市等用户数据。 

本教程涵盖以下任务：

> [!div class="checklist"]
> * 从 Cassandra 表查询数据
> * 运行应用

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备条件

* 本文是由多个部分组成的教程。 在开始之前，请务必完成前面的步骤以创建 Cassandra API 帐户、密钥空间、表并[将示例数据加载到表](cassandra-api-load-data.md)。 

## <a name="query-data"></a>查询数据

通过以下步骤从 Cassandra API 帐户查询数据：

1. 打开 `UserRepository.java` 文件夹下的 `src\main\java\com\azure\cosmosdb\cassandra` 文件。 追加以下代码块。 此代码提供三种方法： 

   * 查询数据库中的所有用户
   * 查询按用户 ID 筛选的特定用户
   * 删除表

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

2. 打开 `UserProfile.java` 文件夹下的 `src\main\java\com\azure\cosmosdb\cassandra` 文件。 此类包含调用你以前定义的插入数据方法 createKeyspace 和 createTable 的主方法。 现在追加查询所有用户或特定用户的以下代码：

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

2. 现在，在 Azure 门户中，打开“数据资源管理器”  并确认用户表已删除。

## <a name="clean-up-resources"></a>清理资源

不再需要资源组、Azure Cosmos 帐户和所有相关的资源时，可将其删除。 为此，请选择虚拟机的资源组，选择“删除”  ，然后确认要删除的资源组的名称。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何从 Azure Cosmos DB 中的 Cassandra API 帐户查询数据。 你现在可以继续学习下一篇文章：

> [!div class="nextstepaction"]
> [将数据迁移到 Cassandra API 帐户](cassandra-import-data.md)


