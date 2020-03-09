---
title: 教程 - 准备要部署到 Azure Spring Cloud 中的 Java Spring 应用程序
description: 在本教程中，你将准备要部署到 Azure Spring Cloud 的 Java Spring 应用程序。
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 49ebfec131c8b9fa7b8535163c03eb7cb692790d
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200016"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>准备要部署到 Azure Spring Cloud 中的 Java Spring 应用程序

本快速入门介绍如何准备一个现有的 Java Spring 应用程序，以便将其部署到 Azure Spring Cloud。 在配置正确的情况下，Azure Spring Cloud 可以提供强大的服务来监视、缩放和更新 Java Spring Cloud 应用程序。

其他示例说明了在配置 POM 文件时，如何将应用程序部署到 Azure Spring Cloud。 
* [使用 Azure 门户启动应用](spring-cloud-quickstart-launch-app-portal.md)
* [使用 Azure CLI 启动应用](spring-cloud-quickstart-launch-app-cli.md)

本文介绍所需的依赖项，以及如何将它们添加到 POM 文件。

## <a name="java-runtime-version"></a>Java 运行时版本

只有 Spring/Java 应用程序能够在 Azure Spring Cloud 中运行。

Azure Spring Cloud 支持 Java 8 和 Java 11。 托管环境包含用于 Azure 的最新版 Azul Zulu OpenJDK。 若要详细了解用于 Azure 的 Azul Zulu OpenJDK，请参阅[安装 JDK](https://docs.microsoft.com/azure/java/jdk/java-jdk-install)。

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot 和 Spring Cloud 版本

若要准备要部署到 Azure Spring Cloud 的现有 Spring Boot 应用程序，请按以下部分中所述，在应用程序 POM 文件中包含 Spring Boot 和 Spring Cloud 依赖项。

Azure Spring Cloud 仅支持使用 Spring Boot 版本2.1 或 2.2 的 Spring Boot 应用。 下表列出了支持的 Spring Boot 和 Spring Cloud 组合：

Spring Boot 版本 | Spring Cloud 版本
---|---
2.1 | Greenwich.RELEASE
2.2 | Hoxton.RELEASE

### <a name="dependencies-for-spring-boot-version-21"></a>Spring Boot 版本 2.1 的依赖项

对于 Spring Boot 版本 2.1，请将以下依赖项添加到应用程序 POM 文件中。

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Spring Boot 版本 2.2 的依赖项

对于 Spring Boot 版本 2.2，请将以下依赖项添加到应用程序 POM 文件中。

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud 客户端依赖项

Azure Spring Cloud 将会托管和管理 Spring Cloud 组件。 组件包括 Spring Cloud 服务注册表和 Spring Cloud 配置服务器。 在依赖项中包括 Azure Spring Cloud 客户端库，以便与 Azure Spring Cloud 服务实例通信。

下表列出了正确的 Azure Spring Cloud 版本，针对使用 Spring Boot 和 Spring Cloud 的应用。

Spring Boot 版本 | Spring Cloud 版本 | Azure Spring Cloud 版本
---|---|---
2.1 | Greenwich.RELEASE | 2.1
2.2 | Hoxton.RELEASE | 2.2

将以下依赖项之一包括到 pom.xml 文件中。 选择其 Azure Spring Cloud 版本与你自己的匹配的依赖项。

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Azure Spring Cloud 版本 2.1 的依赖项

对于 Spring Boot 版本 2.1，请将以下依赖项添加到应用程序 POM 文件中。

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Azure Spring Cloud 版本 2.2 的依赖项

对于 Spring Boot 版本 2.2，请将以下依赖项添加到应用程序 POM 文件中。

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>其他必需的依赖项

若要启用 Azure Spring Cloud 的内置功能，应用程序必须包含以下依赖项： 这样包含可以确保应用程序通过每个组件正确地自行配置。

### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient 注释

将以下注释添加到应用程序源代码中。
```java
@EnableDiscoveryClient
```
有关示例，请参阅前面示例中的 piggymetrics 应用程序：
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="service-registry-dependency"></a>服务注册表依赖项

若要使用托管的 Azure 服务注册表服务，请在 pom.xml 文件中包括 `spring-cloud-starter-netflix-eureka-client` 依赖项，如下所示：

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

服务注册表服务器的终结点自动作为应用的环境变量注入。 然后，应用程序可自行注册到服务注册表服务器，并发现其他依赖性微服务。

### <a name="distributed-configuration-dependency"></a>分布式配置依赖项

若要启用分布式配置，请在 pom.xml 文件的 dependencies 节中包括以下 `spring-cloud-config-client` 依赖项：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> 请勿在启动配置中指定 `spring.cloud.config.enabled=false`。 否则，应用程序将再也不能与配置服务器配合使用。

### <a name="metrics-dependency"></a>指标依赖项

在 pom.xml 文件的 dependencies 节中包括 `spring-boot-starter-actuator` 依赖项，如下所示：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 指标会定期从 JMX 终结点拉取。 可以通过 Azure 门户将指标可视化。

### <a name="distributed-tracing-dependency"></a>分布式跟踪依赖项

在 pom.xml 文件的 dependencies 节中包括下面的 `spring-cloud-starter-sleuth` 和 `spring-cloud-starter-zipkin` 依赖项：

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

 还需让 Azure Application Insights 实例能够兼容 Azure Spring Cloud 服务实例。 阅读[有关分布式跟踪的教程](spring-cloud-tutorial-distributed-tracing.md)，了解如何将 Application Insights 与 Azure Spring Cloud 配合使用。

## <a name="see-also"></a>另请参阅
* [分析应用程序日志和指标](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [设置配置服务器](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [将分布式跟踪与 Azure Spring Cloud 配合使用](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Spring 快速入门指南](https://spring.io/quickstart)
* [Spring Boot 文档](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何配置 Java Spring 应用程序，以便将其部署到 Azure Spring Cloud。 若要了解如何设置配置服务器实例，请继续阅读下一教程。

> [!div class="nextstepaction"]
> [了解如何设置配置服务器实例](spring-cloud-tutorial-config-server.md)

GitHub 中提供了更多示例：[Azure Spring Cloud 示例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)。
