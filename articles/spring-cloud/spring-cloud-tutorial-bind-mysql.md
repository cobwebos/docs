---
title: 如何将 Azure Database for MySQL 绑定到 Azure Spring Cloud 应用程序 | Microsoft Docs
description: 本文介绍如何将 Azure MySQL 绑定到 Azure Spring Cloud 应用程序
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: b6de5bb3b25c111d1b7775ea9570a4ae2cf45042
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607586"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-database-for-mysql"></a>教程：将 Azure 服务绑定到 Azure Spring Cloud 应用程序：Azure Database for MySQL

可以通过 Azure Spring Cloud 将所选 Azure 服务自动绑定到应用程序，而不必手动配置 Spring Boot 应用程序。 本教程会演示如何将应用程序绑定到 Azure MySQL。

## <a name="prerequisites"></a>先决条件

* 部署的 Azure Spring Cloud 实例
* Azure Database for MySQL 帐户
* Azure CLI

如果没有已部署的 Azure Spring Cloud 实例，请按照此[快速入门](spring-cloud-quickstart-launch-app-portal.md)中的步骤部署第一个 Spring Cloud 应用。

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

