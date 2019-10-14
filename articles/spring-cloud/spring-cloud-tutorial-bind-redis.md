---
title: 如何将 Azure Redis 缓存绑定到 Azure Spring Cloud 应用程序 | Microsoft Docs
description: 了解如何将 Azure Redis 缓存绑定到 Azure Spring Cloud 应用程序
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d8fa31207baecc80674fb11b492927800676b8a2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038236"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>教程：将 Azure 服务绑定到 Azure Spring Cloud 应用程序：用于 Redis 的 Azure 缓存

可以通过 Azure Spring Cloud 将所选 Azure 服务自动绑定到应用程序，而不必手动配置 Spring Boot 应用程序。 本文演示如何将应用程序绑定到 Azure Redis 缓存。

## <a name="prerequisites"></a>先决条件

* 部署的 Azure Spring Cloud 实例
* Azure Redis 缓存服务实例
* 用于 Azure CLI 的 Azure Spring Cloud 扩展

必要时，使用以下命令安装用于 Azure CLI 的 Azure Spring Cloud 扩展：

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。  它预安装有常用 Azure 工具，其中包括最新版的 Git、JDK、Maven 和 Azure CLI。 如果已登录到 Azure 订阅，请从 shell.azure.com 启动 [Azure Cloud Shell](https://shell.azure.com)。  若要详细了解 Azure Cloud Shell，可[阅读我们的文档](../cloud-shell/overview.md)

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