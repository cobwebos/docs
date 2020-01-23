---
title: 教程 - 如何将 Azure Database for MySQL 实例绑定到 Azure Spring Cloud 应用程序
description: 本教程介绍如何将 Azure Database for MySQL 实例绑定到 Azure Spring Cloud 应用程序
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 657aa70d77fd1af9fd2121a3e98ea3aca7773642
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277545"
---
# <a name="tutorial-bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>教程：将 Azure Database for MySQL 实例绑定到 Azure Spring Cloud 应用程序 

可以通过 Azure Spring Cloud 将所选 Azure 服务自动绑定到应用程序，而不必手动配置 Spring Boot 应用程序。 本教程介绍如何将应用程序绑定到 Azure Database for MySQL 实例。

## <a name="prerequisites"></a>必备条件

* 部署的 Azure Spring Cloud 实例
* Azure Database for MySQL 帐户
* Azure CLI

如果没有已部署的 Azure Spring Cloud 实例，请按照[快速入门：使用 Azure 门户启动 Azure Spring Cloud 应用](spring-cloud-quickstart-launch-app-portal.md)中的说明部署第一个 Spring Cloud 应用。

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>将应用绑定到 Azure Database for MySQL 实例

1. 记下 Azure Database for MySQL 帐户的管理员用户名和密码。 

1. 连接到服务器，从 MySQL 客户端创建名为“testdb”的数据库，然后创建新的非管理员帐户  。

1. 在项目的 pom.xml 文件中，添加以下依赖项  ：

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. 在 application.properties 文件中，删除所有 `spring.datasource.*` 属性  。

1. 通过运行 `az spring-cloud app update` 更新当前部署，或者通过运行 `az spring-cloud app deployment create` 针对此更改创建新的部署。  这些命令会使用新的依赖项更新或创建应用程序。

1. 在 Azure 门户的“Azure Spring Cloud”服务页上，查找“应用程序仪表板”，然后选择要绑定到 Azure Database for MySQL 实例的应用程序   。  这是在上一步更新或部署的应用程序。 

1. 选择“服务绑定”，然后选择“创建服务绑定”按钮   。 

1. 填充窗体，选择“Azure MySQL”作为“绑定类型”，使用以前使用的数据库名称以及在第一步记下的用户名和密码  。

1. 重启应用，此绑定现在应该生效。

1. 若要确保服务绑定正确，请选择绑定名称，然后验证其详细信息。 `property` 字段应如下所示：
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何将 Azure Spring Cloud 应用程序绑定到 Azure Database for MySQL 实例。  若要详细了解如何管理 Azure Spring Cloud 服务，请参阅关于服务发现和注册的文章。

> [!div class="nextstepaction"]
> [使用 Spring Cloud 服务注册表来启用服务发现和注册](spring-cloud-service-registration.md)
