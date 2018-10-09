---
title: 使用 Java 应用程序将示例数据加载到 Azure Cosmos DB Cassandra API 表 | Microsoft 文档
description: 本文介绍如何使用 Java 应用程序将示例用户数据加载到 Azure Cosmos DB Cassandra API 帐户中的表。
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 662d4b8812ca4b92c1130b9c2c38771e7ec30a06
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393978"
---
# <a name="load-sample-data-into-an-azure-cosmos-db-cassandra-api-table"></a>将示例数据加载到 Azure Cosmos DB Cassandra API 表

本教程介绍如何使用 Java 应用程序将示例用户数据加载到 Azure Cosmos DB Cassandra API 帐户中的表。 Java 应用程序使用 [Java 驱动程序](https://github.com/datastax/java-driver)并加载用户 ID、用户名、用户城市等用户数据。 

本教程涵盖以下任务：

> [!div class="checklist"]
> * 将数据加载到 Cassandra 表
> * 运行应用

## <a name="prerequisites"></a>先决条件

* 本文是由多个部分组成的教程。 在开始使用本文档之前，请确保[创建 Cassandra API 帐户、密钥空间和表](create-cassandra-api-account-java.md)。   

## <a name="load-data-into-the-table"></a>将数据加载到表中

打开“src\main\java\com\azure\cosmosdb\cassandra”文件夹下的“UserRepository.java”文件并追加代码，以将 user_id、user_name 和 user_bcity 字段插入到表中：

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
 
打开“src\main\java\com\azure\cosmosdb\cassandra”文件夹下的“UserProfile.java”文件。 此类包含调用你早前定义的 createKeyspace 和 createTable 方法的主方法。 现在追加以下代码，以将某些示例数据插入到 Cassandra API 表中。

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

打开命令提示符或终端窗口并更改已创建项目的文件夹路径。 运行“mvn clean install”命令，以在目标文件夹中生成 cosmosdb cassandra examples.jar 文件并运行应用程序。 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

现在可以在 Azure 门户中打开数据资源管理器，确认用户信息已添加到表中。
    
## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何将示例数据加载到 Azure Cosmos DB Cassandra API 帐户。 你现在可以继续学习下一篇文章：

> [!div class="nextstepaction"]
> [从 Cassandra API 帐户查询数据](cassandra-api-query-data.md)
