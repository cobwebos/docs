---
title: 如何将 Azure Database for MySQL 绑定到 Azure Spring Cloud 应用程序 | Microsoft Docs
description: 本文介绍如何将 Azure MySQL 绑定到 Azure Spring Cloud 应用程序
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: e2add139f5cfd8299ec809793dd822b051d0f542
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038346"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>教程：将 Azure 服务绑定到 Azure Spring Cloud 应用程序：Azure Database for MySQL

可以通过 Azure Spring Cloud 将所选 Azure 服务自动绑定到应用程序，而不必手动配置 Spring Boot 应用程序。 本教程会演示如何将应用程序绑定到 Azure MySQL。

## <a name="prerequisites"></a>先决条件

* 部署的 Azure Spring Cloud 实例
* Azure Database for MySQL 帐户
* Azure CLI

必要时，使用以下命令安装用于 Azure CLI 的 Azure Spring Cloud 扩展：

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。  它预安装有常用 Azure 工具，其中包括最新版的 Git、JDK、Maven 和 Azure CLI。 如果已登录到 Azure 订阅，请从 shell.azure.com 启动 [Azure Cloud Shell](https://shell.azure.com)。  若要详细了解 Azure Cloud Shell，可[阅读我们的文档](../cloud-shell/overview.md)

## <a name="bind-azure-database-for-mysql"></a>生成 Azure Database for MySQL

1. 记下 Azure MySQL 帐户的管理员用户名和密码。 从 MySQL 客户端连接到服务器并创建名为 `testdb` 的数据库。 创建新的非管理员帐户。

1. 在项目的 `pom.xml` 中添加以下依赖项

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. 删除 `application.properties` 文件中的 `spring.datasource.*` 属性（如果有）。

1. 使用 `az spring-cloud app update` 更新当前部署，或者使用 `az spring-cloud app deployment create` 针对此更改创建新的部署。  这些命令会使用新的依赖项更新或创建应用程序。

1. 转到 Azure 门户中的 Azure Spring Cloud 服务页面。 找到“应用程序仪表板”  ，选择要绑定到 Azure MySQL 的应用程序。  这是在上一步更新或部署的应用程序。 接下来选择“`Service binding`”，然后选择“`Create service binding`”按钮。 填充窗体，确保选择 `Azure MySQL` 作为“绑定类型”，  并选择以前使用的数据库名称以及在第一步记下的用户名和密码。

1. 重启应用，此绑定现在应该生效。

1. 若要确保服务绑定正确，请选择绑定名称并验证其详细信息。 `property` 字段应如下所示：
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何将 Azure Spring Cloud 应用程序绑定到 MySQL DB。  若要详细了解如何管理 Azure Spring Cloud 服务，请继续阅读，了解服务发现和注册。

> [!div class="nextstepaction"]
> [了解如何使用 Spring Cloud 服务注册表来启用服务发现和注册](spring-cloud-service-registration.md)。

