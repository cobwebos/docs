---
title: 教程 - 将 Azure Cosmos DB 绑定到 Azure Spring Cloud 应用程序
description: 本教程介绍如何将 Azure Cosmos DB 绑定到 Azure Spring Cloud 应用程序
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 8eeb67419d2da90ff1e2d2beb8cb59a85c3bbacb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461615"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>将 Azure Cosmos DB 数据库绑定到 Azure Spring Cloud 应用程序

可以通过 Azure Spring Cloud 将所选 Azure 服务自动绑定到应用程序，而不必手动配置 Spring Boot 应用程序。 本文演示如何将应用程序绑定到 Azure Cosmos DB 数据库。

先决条件：

* 部署的 Azure Spring Cloud 实例。 按[有关如何通过 Azure CLI 进行部署的快速入门](spring-cloud-quickstart-launch-app-cli.md)操作即可入门。
* 最低权限级别为“参与者”的 Azure Cosmos DB 帐户。

## <a name="bind-azure-cosmos-db"></a>绑定 Azure Cosmos DB

Azure Cosmos DB 具有五种支持绑定的不同 API 类型。 以下过程演示如何使用它们：

1. 创建 Azure Cosmos DB 数据库。 如需帮助，请参阅有关如何[创建数据库](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)的快速入门。 

1. 记录数据库名称。 对于此过程，数据库名称是 **testdb**。

1. 在 Azure Spring Cloud 应用程序的 pom.xml 文件中添加以下依赖项之一。 选择适合 API 类型的依赖项。

    * API 类型：Core (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * API 类型：MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * API 类型：Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * API 类型：Gremlin（图形）

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * API 类型：Azure 表

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. 使用 `az spring-cloud app update` 更新当前部署，或者使用 `az spring-cloud app deployment create` 创建新的部署。 这些命令会使用新的依赖项更新或创建应用程序。

1. 转到 Azure 门户中的 Azure Spring Cloud 服务页面。 转到“应用程序仪表板”  ，选择要绑定到 Azure Cosmos DB 的应用程序。 此应用程序是在上一步更新或部署的应用程序。

1. 选择“服务绑定”，然后选择“创建服务绑定”   。 若要填写表单，请选择以下项：
   * **绑定类型**值 **Azure Cosmos DB**。
   * API 类型。
   * 数据库名称。
   * Azure Cosmos DB 帐户。

    > [!NOTE]
    > 如果使用 Cassandra，请使用数据库名称的密钥空间。

1. 选择应用程序页上的“重启”来重启应用程序  。

1. 若要确保服务绑定正确，请选择绑定名称并验证其详细信息。 `property` 字段应类似于以下示例：

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何将 Azure Spring Cloud 应用程序绑定到 Azure Cosmos DB 数据库。 若要了解如何将应用程序绑定到 Azure Cache for Redis 缓存，请继续学习下一教程。

> [!div class="nextstepaction"]
> [了解如何绑定到 Azure Cache for Redis 缓存](spring-cloud-tutorial-bind-redis.md)
