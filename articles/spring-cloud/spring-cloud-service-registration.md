---
title: 自动执行服务注册表和发现
description: 了解如何使用 Spring 云服务注册表自动执行服务发现和注册
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.openlocfilehash: 6c217096f0ba4200f49bd1fd8056768a6f6f6dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278858"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>发现并注册您的春云服务

服务发现是基于微服务的体系结构的关键要求。  手动配置每个客户端需要时间，并引入了人为错误的可能性。  Azure 春季云服务注册表解决了此问题。  配置后，服务注册表服务器将控制应用程序的微服务的服务注册和发现。 服务注册表服务器维护已部署微服务的注册表，启用客户端负载平衡，并在不依赖于 DNS 的情况下将服务提供商与客户端分离。

## <a name="register-your-application-using-spring-cloud-service-registry"></a>使用 Spring 云服务注册表注册您的应用程序

在应用程序可以使用 Spring 云服务注册表管理服务注册和发现之前，必须在应用程序的*pom.xml*文件中包含多个依赖项。
包括*弹簧云启动器-netflix-eureka 客户端*和*弹簧云启动器-azure-弹簧云-客户端*对*pom.xml*的依赖关系

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>更新顶级类

最后，我们将注释添加到应用程序的顶级类

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

Spring 云服务注册表服务器终结点将作为环境变量注入到应用程序中。  微服务现在将能够在服务注册表服务器注册自己并发现其他从属微服务。

请注意，更改可能需要几分钟时间才能从服务器传播到所有微服务。
