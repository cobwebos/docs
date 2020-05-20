---
title: 教程 - 将 Azure Cache for Redis 绑定到 Azure Spring Cloud 应用程序
description: 本教程介绍如何将 Azure Cache for Redis 绑定到 Azure Spring Cloud 应用程序
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: 94f7b5a2363b7c53e0f70500e5a0a8cb6f64e611
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277505"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>将 Azure Cache for Redis 绑定到 Azure Spring Cloud 应用程序 

可以通过 Azure Spring Cloud 将所选 Azure 服务自动绑定到应用程序，而不必手动配置 Spring Boot 应用程序。 本文介绍如何将应用程序绑定到 Azure Redis 缓存。

## <a name="prerequisites"></a>必备条件

* 部署的 Azure Spring Cloud 实例
* Azure Redis 缓存服务实例
* 用于 Azure CLI 的 Azure Spring Cloud 扩展

如果没有已部署的 Azure Spring Cloud 实例，请按照[有关如何部署 Azure Spring Cloud 应用的快速入门](spring-cloud-quickstart-launch-app-portal.md)中的步骤操作。

## <a name="bind-azure-cache-for-redis"></a>绑定 Azure Redis 缓存

1. 在项目的 pom.xml 文件中，添加以下依赖项：

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. 从 `application.properties` 文件中删除任何 `spring.redis.*` 属性

1. 使用 `az spring-cloud app update` 更新当前部署，或者使用 `az spring-cloud app deployment create` 创建新的部署。

1. 转到 Azure 门户中的 Azure Spring Cloud 服务页面。 转到“应用程序仪表板”  ，选择要绑定到 Azure Cache for Redis 的应用程序。 此应用程序是在上一步更新或部署的应用程序。

1. 选择“服务绑定”，然后选择“创建服务绑定”   。 填充窗体，确保选择“绑定类型”值“Azure Cache for Redis”、你的 Azure Cache for Redis 服务器，  以及“主密钥”选项。  

1. 重新启动应用。 绑定现在应该生效。

1. 若要确保服务绑定正确，请选择绑定名称并验证其详细信息。 `property` 字段应如下所示：
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何将 Azure Spring Cloud 应用程序绑定到 Azure Cache for Redis。 若要详细了解如何将服务绑定到应用程序，请继续学习介绍如何将应用程序绑定到 Azure Database for MySQL 实例的教程。

> [!div class="nextstepaction"]
> [了解如何绑定到 Azure Database for MySQL 实例](spring-cloud-tutorial-bind-mysql.md)
