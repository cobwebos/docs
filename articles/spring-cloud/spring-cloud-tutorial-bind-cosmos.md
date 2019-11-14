---
title: 将 Azure Cosmos DB 绑定到 Azure Spring Cloud 应用程序 | Microsoft Docs
description: 了解如何将 Azure Cosmos DB 绑定到 Azure Spring Cloud 应用程序
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 76a2b9f305f041a19b8d7ace8234a804825f6a0e
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607133"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>教程：将 Azure Cosmos DB 绑定到 Azure Spring Cloud 应用程序

可以通过 Azure Spring Cloud 将所选 Azure 服务自动绑定到应用程序，而不必手动配置 Spring Boot 应用程序。 本文演示如何将应用程序绑定到 Azure Cosmos DB。

先决条件：
* 部署的 Azure Spring Cloud 实例。  按[快速入门](spring-cloud-quickstart-launch-app-cli.md)的说明开始操作。
* 具有参与者的最低权限级别的 Azure Cosmos DB 帐户。

## <a name="bind-azure-cosmos-db"></a>绑定 Azure Cosmos DB

Azure Cosmos DB 具有五种支持绑定的不同 API 类型：

1. 创建 Azure Cosmos DB 数据库。 有关创建数据库的帮助，[请参阅本文](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)。 记录数据库名称。 我们的数据库名为 `testdb`。

1. 根据 API 类型在 Spring Cloud 应用程序的 `pom.xml` 中添加以下依赖项之一。
    
    #### <a name="api-type-core-sql"></a>API 类型：Core (SQL)

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.1.6</version>
    </dependency>
    ```
    
    #### <a name="api-type-mongodb"></a>API 类型：MongoDB

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    ```

    #### <a name="api-type-cassandra"></a>API 类型：Cassandra

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-cassandra</artifactId>
    </dependency>
    ```

    #### <a name="api-type-gremlin-graph"></a>API 类型：Gremlin（图形）

    ```xml
    <dependency>
        <groupId>com.microsoft.spring.data.gremlin</groupId>
        <artifactId>spring-data-gremlin</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    #### <a name="api-type-azure-table"></a>API 类型：Azure 表

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-storage-spring-boot-starter</artifactId>
        <version>2.0.5</version>
    </dependency>
    ```

1. 使用 `az spring-cloud app update` 更新当前部署，或者使用 `az spring-cloud app deployment create` 针对此更改创建新的部署。  这些命令会使用新的依赖项更新或创建应用程序。

1. 转到 Azure 门户中的 Azure Spring Cloud 服务页面。 这是在上一步更新或部署的应用程序。 找到“应用程序仪表板”  ，选择要绑定到 Cosmos DB 的应用程序。 接下来选择“`Service binding`”，然后选择“`Create service binding`”按钮。 填写表单，选择绑定类型 `Azure Cosmos DB`、API 类型、数据库名称和 Azure Cosmos DB 帐户  。

    > [!NOTE]
    > 如果使用的是 Cassandra，请使用数据库名称的密钥空间。

1. 通过选择应用程序页上的“重新启动”按钮来重新启动应用程序  。

1. 若要确保服务绑定正确，请选择绑定名称并验证其详细信息。 `property` 字段应如下所示：

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何将 Azure Spring Cloud 应用程序绑定到 CosmosDB。  若要了解如何将应用程序绑定到 Azure Redis Cache，请继续学习下一教程。

> [!div class="nextstepaction"]
> [将 Azure Spring Cloud 应用程序绑定到 Azure Redis Cache](spring-cloud-tutorial-bind-redis.md)
