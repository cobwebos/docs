---
title: 教程 - 准备要部署到 Azure Spring Cloud 中的 Spring 应用程序
description: 在本教程中，我们准备用于部署的 Java Spring 应用程序。
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e112fdc9e6f518e2ea3c72161e8978118cf19335
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890308"
---
# <a name="tutorial-prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>教程：准备要部署到 Azure Spring Cloud 中的 Java Spring 应用程序

本快速入门介绍如何准备一个现有的 Java Spring Cloud 应用程序，以便将其部署到 Azure Spring Cloud。  在配置正确的情况下，Azure Spring Cloud 可以提供强大的服务来监视、缩放和更新 Spring Cloud 应用程序。 

## <a name="java-runtime-version"></a>Java 运行时版本

只有 Spring/Java 应用程序能够在 Azure Spring Cloud 中运行。

Java 8 和 Java 11 均受支持。 托管环境包含用于 Azure 的最新 Azul Zulu OpenJDK。 请参阅[此文](https://docs.microsoft.com/azure/java/jdk/java-jdk-install)，详细了解用于 Azure 的 Azul Zulu OpenJDK。 

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot 和 Spring Cloud 版本

仅 Spring Boot 应用在 Azure Spring Cloud 中受支持。 Spring Boot 2.0 和 2.1 均受支持。 支持的 Spring Boot 和 Spring Cloud 组合在下表中列出。

Spring Boot 版本 | Spring Cloud 版本
---|---
2.0.x | Finchley.RELEASE
2.1.x | Greenwich.RELEASE

验证 `pom.xml` 文件是否有基于版本的 Spring Boot 和 Spring Cloud 依赖项。

### <a name="version-20"></a>版本 2.0：

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.9.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Finchley.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="version-21"></a>版本 2.1：

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.8.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR3</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud 客户端依赖项

Azure Spring Cloud 为你托管和管理 Spring Cloud 组件，例如 Spring Cloud 服务注册表和 Spring Cloud 配置服务器。 在依赖项中包括 Azure Spring Cloud 的客户端库，以便与 Azure Spring Cloud 服务实例通信。

下表列出了 Spring Boot/Spring Cloud 应用的正确版本。

Spring Boot 版本 | Spring Cloud 版本 | Azure Spring Cloud 版本
---|---|---
2.0.x | Finchley.RELEASE | 2.0.x
2.1.x | Greenwich.RELEASE | 2.1.x

在 `pom.xml` 中包括以下代码片段之一。  选择其版本与你自己的版本匹配的代码片段。

### <a name="version-20x"></a>版本 2.0.x：
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.0.0</version>
</dependency>
```

### <a name="version-21x"></a>版本 2.1.x：
```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>其他必需的依赖项

若要启用 Azure Spring Cloud 的内置功能，应用程序必须包含以下依赖项： 这样可以确保应用程序通过每个组件正确地自行配置。  

### <a name="service-registry"></a>服务注册表

若要使用托管的 Azure 服务注册表服务，请在 `POM.xml` 中包括 `spring-cloud-starter-netflix-eureka-client`，如下所示。

服务注册表服务器的终结点会自动注入为应用的环境变量。 应用程序将可以自行注册到服务注册表服务器并发现其他依赖性微服务。

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

### <a name="distributed-configuration"></a>分布式配置

若要启用分布式配置，请在 `pom.xml` 的依赖项部分包括 `spring-cloud-config-client`。

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> 请勿在启动配置中指定 `spring.cloud.config.enabled=false`，因为它会导致应用程序无法与配置服务器配合使用。

### <a name="metrics"></a>度量值

在 pom.xml 的依赖项部分包括 `spring-boot-starter-actuator`。 指标会定期从 JMX 终结点拉取，并可通过 Azure 门户可视化。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

### <a name="distributed-tracing"></a>分布式跟踪

在 pom.xml 的依赖项部分包括 `spring-cloud-starter-sleuth` 和 `spring-cloud-starter-zipkin`，如下所示。 例外，需要让 Azure App Insights 实例能够兼容 Azure Spring Cloud 服务实例。 若要详细了解如何通过 Azure Spring Cloud 来启用 App Insights，请参阅[此文](spring-cloud-tutorial-distributed-tracing.md)

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何配置 Java Spring 应用程序，以便将其部署到 Azure Spring Cloud。  若要了解如何启用配置服务器，请继续阅读下一教程。

> [!div class="nextstepaction"]
> [了解如何设置配置服务器](spring-cloud-tutorial-config-server.md)。

GitHub 中提供了更多示例：[Azure Spring Cloud 示例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql)。
