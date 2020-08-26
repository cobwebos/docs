---
title: 在 Spring Boot 应用中使用动态配置
titleSuffix: Azure App Configuration
description: 了解如何动态更新 Spring Boot 应用的配置数据
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.custom: devx-track-java
ms.author: lcozzens
ms.openlocfilehash: 653fcb6f6590fd503a97800ec8196025cf14a3b9
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121555"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>教程：在 Java Spring 应用中使用动态配置

应用配置 Spring Boot 客户端库支持按需更新一组配置设置，不会导致应用程序重启。 客户端库会缓存每项设置，避免过多调用配置存储。 在缓存值过期前，刷新操作不会更新该值，即使该值在配置存储中已发生更改。 每个请求的默认过期时间为 30 秒。 可以根据需要重写它。

可以通过调用 `AppConfigurationRefresh` 的 `refreshConfigurations()` 方法，根据需要检查是否有更新的设置。

也可使用 `spring-cloud-azure-appconfiguration-config-web` 包，该包依赖 `spring-web` 来处理自动刷新。

## <a name="use-automated-refresh"></a>使用自动刷新

若要使用自动刷新，请从使用应用程序配置的 Spring Boot 应用（例如，按[适用于应用程序配置的 Spring Boot 快速入门](quickstart-java-spring-app.md)创建的应用）着手。

然后，在文本编辑器中打开 *pom.xml* 文件，为 `<dependency>` 添加 `spring-cloud-azure-appconfiguration-config-web`。

**Spring Cloud 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.5</version>
</dependency>
```

**Spring Cloud 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## <a name="run-and-test-the-app-locally"></a>在本地运行并测试应用

1. 使用 Maven 生成 Spring Boot 应用程序，然后运行该程序。

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. 打开浏览器窗口，访问 URL：`http://localhost:8080`。  将显示与密钥关联的消息。 

    还可以使用 curl 来测试应用程序，例如： 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. 若要测试动态配置，请打开与应用程序关联的 Azure 应用程序配置门户。 选择“配置资源管理器”，并更新所显示的密钥的值，例如：
    | 键 | 值 |
    |---|---|
    | application/config.message | Hello - Updated |

1. 刷新浏览器页面，查看显示的新消息。

## <a name="next-steps"></a>后续步骤

在本教程中，你已启用 Spring Boot 应用，可以通过应用程序配置动态刷新配置设置。 若要了解如何使用 Azure 托管标识来简化对应用程序配置的访问，请继续学习下一篇教程。

> [!div class="nextstepaction"]
> [托管标识集成](./howto-integrate-azure-managed-service-identity.md)
