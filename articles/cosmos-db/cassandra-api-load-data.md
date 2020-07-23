---
title: 教程：Java 应用：将示例数据加载到 Azure Cosmos DB 的 Cassandra API 表中
description: 本教程展示了如何使用 Java 应用程序将示例用户数据加载到 Azure Cosmos DB 中的 Cassandra API 表。
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 6a12db8e34421dd16c12d167896ef66b3377d524
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853012"
---
# <a name="tutorial-load-sample-data-into-a-cassandra-api-table-in-azure-cosmos-db"></a>教程：将示例数据加载到 Azure Cosmos DB 中的 Cassandra API 表

作为开发人员，你可能具有使用键/值对的应用程序。 你可以使用 Azure Cosmos DB 中的 Cassandra API 帐户来存储键/值数据。 本教程展示了如何使用 Java 应用程序将示例用户数据加载到 Azure Cosmos DB 中的 Cassandra API 帐户中的表。 该 Java 应用程序使用 [Java 驱动程序](https://github.com/datastax/java-driver)并加载用户数据，例如用户 ID、用户名和用户城市。 

本教程涵盖以下任务：

> [!div class="checklist"]
> * 将数据加载到 Cassandra 表
> * 运行应用

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* 本文是由多个部分组成的教程。 在开始使用本文档之前，请确保[创建 Cassandra API 帐户、密钥空间和表](create-cassandra-api-account-java.md)。   

## <a name="load-data-into-the-table"></a>将数据加载到表中

通过以下步骤将数据加载到 Cassandra API 表中：

1. 打开“src\main\java\com\azure\cosmosdb\cassandra”文件夹下的“UserRepository.java”文件并追加代码，以将 user_id、user_name 和 user_bcity 字段插入到表中：

   ```java
   /**
   * Insert a row into user table
   *
   * @param id   user_id
   * @param name user_name
   * @param city user_bcity
   */
   public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
   }

   /**
   * Create a PrepareStatement to insert a row to user table
   *
   * @return PreparedStatement
   */
   public PreparedStatement prepareInsertStatement() {
      final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
   return session.prepare(insertStatement);
   }
   ```
 
2. 打开“src\main\java\com\azure\cosmosdb\cassandra”文件夹下的“UserProfile.java”文件。 此类包含调用你早前定义的 createKeyspace 和 createTable 方法的主方法。 现在追加以下代码，以将某些示例数据插入到 Cassandra API 表中。

   ```java
   //Insert rows into user table
   PreparedStatement preparedStatement = repository.prepareInsertStatement();
     repository.insertUser(preparedStatement, 1, "JohnH", "Seattle");
     repository.insertUser(preparedStatement, 2, "EricK", "Spokane");
     repository.insertUser(preparedStatement, 3, "MatthewP", "Tacoma");
     repository.insertUser(preparedStatement, 4, "DavidA", "Renton");
     repository.insertUser(preparedStatement, 5, "PeterS", "Everett");
   ```

## <a name="run-the-app"></a>运行应用

打开一个命令提示符或终端窗口，并将文件夹路径更改到你在其中创建了项目的位置。 运行“mvn clean install”命令，以在目标文件夹中生成 cosmosdb cassandra examples.jar 文件并运行应用程序。 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

现在可以在 Azure 门户中打开数据资源管理器，确认用户信息已添加到表中。
    
## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何将示例数据加载到 Azure Cosmos DB 中的 Cassandra API 帐户。 你现在可以继续学习下一篇文章：

> [!div class="nextstepaction"]
> [从 Cassandra API 帐户查询数据](cassandra-api-query-data.md)
