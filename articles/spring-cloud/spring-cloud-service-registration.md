---
title: 自动执行服务注册表和发现
description: 了解如何使用春季云服务注册表自动执行服务发现和注册
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: brendm
ms.openlocfilehash: 6c217096f0ba4200f49bd1fd8056768a6f6f6dbd
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278858"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>发现和注册春季云服务

服务发现是基于微服务的体系结构的关键要求。  手动配置每个客户端会耗费时间，并引入了人为错误的可能性。  Azure 春季云服务注册表解决了这一问题。  配置后，服务注册表服务器将为应用程序的微服务控制服务注册和发现。 服务注册表服务器维护已部署的微服务的注册表，启用客户端负载平衡，并从客户端分离服务提供程序，而无需依赖 DNS。

## <a name="register-your-application-using-spring-cloud-service-registry"></a>使用春季云服务注册表注册应用程序

在应用程序可以使用春季云服务注册表管理服务注册和发现之前，应用程序的*pom*文件中必须包含几个依赖项。
将 netflix 的依赖项*包含在* *eureka-客户端*和*春季*---------云和

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

最后，向应用程序的顶级类添加批注

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

在应用程序中，弹簧云服务注册表服务器终结点将被注入为环境变量。  微服务现在将能够向服务注册表服务器注册自己，并发现其他依赖微服务。

请注意，将更改从服务器传播到所有微服务可能需要几分钟时间。
