---
title: 如何将 Azure Redis 缓存绑定到 Azure Spring Cloud 应用程序 | Microsoft Docs
description: 了解如何将 Azure Redis 缓存绑定到 Azure Spring Cloud 应用程序
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: a901e4194909df85f53799d5937515e42ea87a69
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607572"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>教程：将 Azure 服务绑定到 Azure Spring Cloud 应用程序：用于 Redis 的 Azure 缓存

可以通过 Azure Spring Cloud 将所选 Azure 服务自动绑定到应用程序，而不必手动配置 Spring Boot 应用程序。 本文演示如何将应用程序绑定到 Azure Redis 缓存。

## <a name="prerequisites"></a>先决条件

* 部署的 Azure Spring Cloud 实例
* Azure Redis 缓存服务实例
* 用于 Azure CLI 的 Azure Spring Cloud 扩展

如果没有已部署的 Azure Spring Cloud 实例，请按照此[快速入门](spring-cloud-quickstart-launch-app-portal.md)中的步骤部署第一个 Spring Cloud 应用。

## <a name="bind-azure-cache-for-redis"></a>绑定 Azure Redis 缓存

1. 在项目的 `pom.xml` 中添加以下依赖项

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. 删除 `application.properties` 文件中的 `spring.redis.*` 属性（如果有）

1. 使用 `az spring-cloud app update` 更新当前部署，或者使用 `az spring-cloud app deployment create` 创建新的部署。

1. 转到 Azure 门户中的 Azure Spring Cloud 服务页面。 找到“应用程序仪表板”  ，选择要绑定到 Azure Redis 缓存的应用程序。  这是在上一步更新或部署的应用程序。 接下来选择“`Service binding`”，然后选择“`Create service binding`”按钮。 填充窗体，确保选择 `Azure Cache for Redis` 作为“绑定类型”，  并选择 Redis 服务器和“主密钥”选项。 

1. 重启应用，此绑定现在应该生效。

1. 若要确保服务绑定正确，请选择绑定名称并验证其详细信息。 `property` 字段应如下所示：
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何将 Azure Spring Cloud 应用程序绑定到 Azure Redis 缓存。  若要详细了解如何将服务绑定到应用程序，请继续学习将应用程序绑定到 MySQL DB 的教程。

> [!div class="nextstepaction"]
> [了解如何将 Azure MySql 服务绑定到 Azure Spring Cloud 服务](spring-cloud-tutorial-bind-mysql.md)。