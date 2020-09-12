---
title: 如何在 Azure 春季云中为部署准备 Java 弹簧应用程序
description: 了解如何准备 Java 弹簧应用程序以部署到 Azure 春季 Cloud。
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 59318cca33ba1607498546161764aa3aaaaea13e
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90014932"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>准备要部署到 Azure Spring Cloud 中的 Java Spring 应用程序

本主题介绍如何准备现有的需要部署到 Azure Spring Cloud 的 Java Spring 应用程序。 在配置正确的情况下，Azure Spring Cloud 可以提供强大的服务来监视、缩放和更新 Java Spring Cloud 应用程序。

在运行此示例之前，可以尝试[基础知识快速入门](spring-cloud-quickstart.md)。

其他示例说明了在配置 POM 文件时，如何将应用程序部署到 Azure Spring Cloud。 
* [启动你的第一个应用](spring-cloud-quickstart.md)
* [生成并运行微服务](spring-cloud-quickstart-sample-app-introduction.md)

本文介绍所需的依赖项，以及如何将它们添加到 POM 文件。

## <a name="java-runtime-version"></a>Java 运行时版本

只有 Spring/Java 应用程序能够在 Azure Spring Cloud 中运行。

Azure Spring Cloud 支持 Java 8 和 Java 11。 托管环境包含用于 Azure 的最新版 Azul Zulu OpenJDK。 若要详细了解用于 Azure 的 Azul Zulu OpenJDK，请参阅[安装 JDK](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)。

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot 和 Spring Cloud 版本

若要准备要部署到 Azure Spring Cloud 的现有 Spring Boot 应用程序，请按以下部分中所述，在应用程序 POM 文件中包含 Spring Boot 和 Spring Cloud 依赖项。

Azure Spring Cloud 仅支持使用 Spring Boot 版本2.1 或 2.2 的 Spring Boot 应用。 下表列出了支持的 Spring Boot 和 Spring Cloud 组合：

Spring Boot 版本 | Spring Cloud 版本
---|---
2.1 | Greenwich.RELEASE
2.2 | Hoxton.SR8
2.3 | Hoxton.SR8

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
                <version>Greenwich.RELEASE</version>
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
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
### <a name="dependencies-for-spring-boot-version-23"></a>弹簧 Boot 版本2.3 的依赖项

对于春季 Boot 版本2.3，请将以下依赖项添加到应用程序 POM 文件。

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.0.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud 客户端依赖项

Azure Spring Cloud 将会托管和管理 Spring Cloud 组件。 组件包括 Spring Cloud 服务注册表和 Spring Cloud 配置服务器。 建议使用春季 Boot 2.2 或2.3。 对于春季 Boot 2.1，你将需要在依赖项中包含 Azure 春季云客户端库，以允许与 Azure 春季云服务实例通信。

下表列出了正确的 Azure Spring Cloud 版本，针对使用 Spring Boot 和 Spring Cloud 的应用。

Spring Boot 版本 | Spring Cloud 版本 | Azure 春季 Cloud client 入门版
---|---|---
2.1.x | Greenwich.RELEASE | 2.1.2
2.2. x | Hoxton.SR8 | 不需要
2.3. x | Hoxton.SR8 | 不需要

如果使用的是春季 Boot 2.1，请在 pom.xml 文件中包含以下 dependenciy。

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.2</version>
</dependency>
```

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>启用 Azure Spring Cloud 功能的其他推荐依赖项

若要启用服务注册表到分布式跟踪的 Azure Spring Cloud 内置功能，你还需要在应用程序中包含以下依赖项。 如果不需要特定应用的相应功能，你可以删除这些依赖项。

### <a name="service-registry"></a>服务注册表

若要使用托管的 Azure 服务注册表服务，请在 pom.xml 文件中包括 `spring-cloud-starter-netflix-eureka-client` 依赖项，如下所示：

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

服务注册表服务器的终结点自动作为应用的环境变量注入。 然后，应用程序可自行注册到服务注册表服务器，并发现其他依赖性微服务。

#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient 注释

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

### <a name="distributed-configuration"></a>分布式配置

若要启用分布式配置，请在 pom.xml 文件的 dependencies 节中包括以下 `spring-cloud-config-client` 依赖项：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> 请勿在启动配置中指定 `spring.cloud.config.enabled=false`。 否则，应用程序将再也不能与配置服务器配合使用。

### <a name="metrics"></a>指标

在 pom.xml 文件的 dependencies 节中包括 `spring-boot-starter-actuator` 依赖项，如下所示：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 指标会定期从 JMX 终结点拉取。 可以通过 Azure 门户将指标可视化。

 > [!WARNING]
 > 请 `spring.jmx.enabled=true` 在配置属性中指定。 否则，无法在 Azure 门户中可视化度量值。

### <a name="distributed-tracing"></a>分布式跟踪

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

 还需让 Azure Application Insights 实例能够兼容 Azure Spring Cloud 服务实例。 有关如何将 Application Insights 与 Azure 春季云一起使用的信息，请参阅有关 [分布式跟踪的文档](spring-cloud-tutorial-distributed-tracing.md)。

## <a name="see-also"></a>另请参阅
* [分析应用程序日志和指标](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [设置配置服务器](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [将分布式跟踪与 Azure Spring Cloud 配合使用](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Spring 快速入门指南](https://spring.io/quickstart)
* [Spring Boot 文档](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>后续步骤

本主题介绍了如何配置 Java Spring 应用程序，以便将其部署到 Azure Spring Cloud。 若要了解如何设置配置服务器实例，请参阅 [设置配置服务器实例](spring-cloud-tutorial-config-server.md)。

GitHub 中提供了更多示例：[Azure Spring Cloud 示例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)。
