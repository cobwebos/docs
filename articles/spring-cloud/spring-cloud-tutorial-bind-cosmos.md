---
title: 将 Azure Cosmos DB 绑定到你的 Azure 春季云应用程序 |Microsoft Docs
description: 了解如何将 Azure Cosmos DB 绑定到 Azure 春季云应用程序
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d051fd431180e9cb86f1df4642fb5e947103c007
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038725"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>教程：将 Azure Cosmos DB 绑定到你的 Azure 春季云应用程序

Azure 春季 Cloud 允许自动将选择的 Azure 服务绑定到应用程序，而不是手动配置弹簧 Boot 应用程序。 本文演示如何将应用程序绑定到 Azure Cosmos DB。

先决条件：
* 已部署的 Azure 春季云实例。  请按照我们的[快速](spring-cloud-quickstart-launch-app-cli.md)入门开始操作。
* 具有参与者的最低权限级别的 Azure Cosmos DB 帐户。

## <a name="bind-azure-cosmos-db"></a>绑定 Azure Cosmos DB

Azure Cosmos DB 具有五种支持绑定的不同 API 类型：

1. 创建 Azure Cosmos DB 数据库。 有关创建数据库的帮助，[请参阅此文](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)。 记录数据库的名称。 我们将其命名为 `testdb`。

1. 根据 API 类型在春季 Cloud 应用程序的 `pom.xml` 中添加以下依赖项之一。
    
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

1. 使用 `az spring-cloud app update` 更新当前部署，或使用 `az spring-cloud app deployment create` 为此更改创建新的部署。  这些命令将更新或创建包含新依赖项的应用程序。

1. 在 Azure 门户中，请参阅 Azure 春季云服务页。 这与你在上一步中更新或部署的应用程序相同。 找到**应用程序仪表板**，并选择要绑定到 Cosmos DB 的应用程序。 接下来，选择 "@no__t"，然后选择 "@no__t" 按钮。 填写表单，选择**绑定类型**`Azure Cosmos DB`、API 类型、数据库名称和 Azure Cosmos DB 帐户。

    > [!NOTE]
    > 如果使用的是 Cassandra，请使用数据库名称的密钥空间。

1. 通过选择 "应用程序" 页上的 "**重新启动**" 按钮重新启动应用程序。

1. 若要确保服务绑定正确，请选择绑定名称并验证其详细信息。 @No__t-0 字段应类似于：

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>后续步骤

在本教程中，已学习如何将 Azure 春季云应用程序绑定到 CosmosDB。  若要了解如何将应用程序绑定到 Azure Redis 缓存，请继续学习下一教程。

> [!div class="nextstepaction"]
> [将 Azure 春季云应用程序绑定到 Azure Redis 缓存](spring-cloud-tutorial-bind-redis.md)。
