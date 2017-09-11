---
title: "如何配置 Spring Boot Initializer 应用，以使用 Redis 缓存"
description: "了解如何配置使用 Spring Initializer 创建的 Spring Boot 应用程序，以使用 Azure Redis 缓存。"
services: redis-cache
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
keywords: "Spring、Spring Boot Starter、Redis 缓存"
ms.assetid: 
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: java
ms.topic: article
ms.date: 7/21/2017
ms.author: robmcm;zhijzhao;yidon
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: fb3fc96a2136b7c326bb0eb291b7204e7acf0190
ms.contentlocale: zh-cn
ms.lasthandoff: 08/10/2017

---

# <a name="how-to-configure-a-spring-boot-initializer-app-to-use-redis-cache"></a>如何配置 Spring Boot Initializer 应用，以使用 Redis 缓存

## <a name="overview"></a>概述

[Spring Framework] 是一种开放源代码解决方案，可帮助 Java 开发人员创建企业级应用程序。 基于该平台构建的其中一个更常用的项目是 [Spring Boot]，该项目提供了简化的方法来创建独立的 Java 应用程序。 为帮助开发人员开始使用 Spring Boot，在 <https://github.com/spring-guides/> 网站中提供了几个 Spring Boot 包。 除了从基本的 Spring Boot 项目列表中选择之外，[Spring Initializr] 也可帮助开发人员开始创建自定义 Spring Boot 应用程序。

本文提供以下分步指导：使用 Azure 门户创建 Redis 缓存，使用 Spring Initializr 创建自定义应用程序，然后创建使用 Redis 缓存存储并检索数据的 Java web 应用程序。

## <a name="prerequisites"></a>先决条件

为遵循本文介绍的步骤，需要以下先决条件：

* Azure 订阅；若尚未拥有 Azure 订阅，可激活 [MSDN 订阅者权益]或注册获取[免费 Azure 帐户]。

* [Java 开发工具包 (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/) 1.7 版或更高版本。

* [Apache Maven](http://maven.apache.org/) 3.0 或更高版本。

## <a name="create-a-redis-cache-on-azure"></a>在 Azure 上创建 Redis 缓存

1. 浏览到 Azure 门户 <https://portal.azure.com/>，然后单击“+新建”项。

   ![Azure 门户][AZ01]

1. 单击“数据库”，然后单击“Redis 缓存”。

   ![Azure 门户][AZ02]

1. 在“新建 Redis 缓存”页上，为缓存输入“DNS 名称”，然后指定“订阅”、“资源组”、“位置”和“定价层”。 指定这些选项后，单击“创建”以创建缓存。

   ![Azure 门户][AZ03]

1. 创建缓存完成后，会看到其列在 Azure“仪表板”上，并显示在“所有资源”和“Redis 缓存”页下。 可在任何上述位置上单击缓存，打开缓存的属性页。

   ![Azure 门户][AZ04]

1. 显示包含缓存属性列表的页后，单击“访问密匙”，然后复制缓存的访问密钥。

   ![Azure 门户][AZ05]

## <a name="create-a-custom-application-using-the-spring-initializr"></a>使用 Spring Initializr 创建自定义应用程序

1. 浏览到 https://start.spring.io/<>。

1. 指定要使用 Java 生成的 Maven 项目，输入应用程序的“组”名称和“Aritifact”名称，然后单击链接切换到 Spring Initializr 完整版。

   ![Spring Initializr 的基本选项][SI01]

   > [!NOTE]
   >
   > Spring Initializr 会使用“组”和“Artifact”名称创建包名称，例如：com.contoso.myazuredemo。
   >

1. 向下滚动到“Web”部分，选中“Web”框，然后向下滚动到“NoSQL”，选中“Redis”框，再滚动到页面底部，单击“生成项目”按钮。

   ![Spring Initializr 的完整选项][SI02]

1. 出现提示时，将项目下载到本地计算机中的路径。

   ![下载自定义 Spring Boot 项目][SI03]

1. 提取本地系统上的文件之后，自定义 Spring Boot 应用程序便可进行编辑。

   ![自定义 Spring Boot 项目文件][SI04]

## <a name="configure-your-custom-spring-boot-to-use-your-redis-cache"></a>配置自定义 Spring Boot 以使用 Redis 缓存

1. 在应用的“资源”目录中找到 application.properties 文件，或创建此文件（若此文件不存在）。

   ![找到 application.properties 文件][RE01]

1. 在文本编辑器中打开 application.properties 文件，将以下行添加到文件中，然后将示例值替换为缓存中的相应属性：

   ```yaml
   # Specify the DNS URI of your Redis cache.
   spring.redis.host=myspringbootcache.redis.cache.windows.net

   # Specify the port for your Redis cache.
   spring.redis.port=6380

   # Specify the access key for your Redis cache.
   spring.redis.password=57686f6120447564652c2049495320526f636b73=
   ```

   ![编辑 application.properties 文件][RE02]

1. 保存并关闭 application.properties 文件。

1. 在包的源文件夹中创建名为“控制器”的文件夹，例如：

   `C:\SpringBoot\myazuredemo\src\main\java\com\contoso\myazuredemo\controller`

   -或-

   `/users/example/home/myazuredemo/src/main/java/com/contoso/myazuredemo/controller`

1. 在 controller 文件夹中创建一个名为 HelloController.java 的新文件。 在文本编辑器中打开该文件，然后向其添加以下代码：

   ```java
   package com.contoso.myazuredemo;

   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.beans.factory.annotation.Value;
   import redis.clients.jedis.Jedis;
   import redis.clients.jedis.JedisShardInfo;

   @RestController
   public class HelloController {
   
      // Retrieve the DNS name for your cache.
      @Value("${spring.redis.host}")
      private String redisHost;

      // Retrieve the port for your cache.
      @Value("${spring.redis.port}")
      private int redisPort;

      // Retrieve the access key for your cache.
      @Value("${spring.redis.password}")
      private String redisPassword;

      @RequestMapping("/")
      // Define the Hello World controller.
      public String hello() {
      
         // Create a JedisShardInfo object to connect to your Redis cache.
         JedisShardInfo jedisShardInfo = new JedisShardInfo(redisHost, redisPort, true);
         // Specify your access key.
         jedisShardInfo.setPassword(redisPassword);
         // Create a Jedis object to store/retrieve information from your cache.
         Jedis jedis = new Jedis(jedisShardInfo);

         // Add a Hello World string to your cache.
         jedis.set("greeting", "Hello World!");

         // Return the string from your cache.
         return jedis.get("greeting");
      }
   }
   ```
   
   需要将 `com.contoso.myazuredemo` 替换为项目的包名称的地方。

1. 保存并关闭 HelloController.java 文件。

1. 使用 Maven 生成 Spring Boot 应用程序，然后运行该程序，例如：

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. 使用 Web 浏览器浏览到 http://localhost:8080 以测试 Web 应用，如果有可用的 Curl，也可使用如以下示例所示的语法：

   ```shell
   curl http://localhost:8080
   ```

   应会看到“Hello World!” 消息在示例控制器中显示，这是从 Redis 缓存中动态检索到的。

## <a name="next-steps"></a>后续步骤

有关使用 Azure 上的 Spring Boot 应用程序的详细信息，请参阅以下文章：

* [将 Spring Boot 应用程序部署到 Azure App Service](../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)

* [在 Azure 容器服务中运行 Kubernetes 群集上的 Spring Boot 应用程序](../container-service/container-service-deploy-spring-boot-app-on-kubernetes.md)

有关将 Azure 与 Java 配合使用的详细信息，请参阅 [Azure Java 开发人员中心]和[用于 Visual Studio Team Services 的 Java 工具]。

若要深入了解如何在 Azure 上开始将 Redis 缓存用于 Java，请参阅[如何将 Azure Redis 缓存用于 Java][Redis Cache with Java]。

<!-- URL List -->

[Azure Java 开发人员中心]: https://azure.microsoft.com/develop/java/
[免费 Azure 帐户]: https://azure.microsoft.com/pricing/free-trial/
[用于 Visual Studio Team Services 的 Java 工具]: https://java.visualstudio.com/
[MSDN 订阅者权益]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[Redis Cache with Java]: cache-java-get-started.md

<!-- IMG List -->

[AZ01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ01.png
[AZ02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ02.png
[AZ03]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ03.png
[AZ04]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ04.png
[AZ05]: ./media/cache-java-spring-boot-initializer-with-redis-cache/AZ05.png

[SI01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI01.png
[SI02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI02.png
[SI03]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI03.png
[SI04]: ./media/cache-java-spring-boot-initializer-with-redis-cache/SI04.png

[RE01]: ./media/cache-java-spring-boot-initializer-with-redis-cache/RE01.png
[RE02]: ./media/cache-java-spring-boot-initializer-with-redis-cache/RE02.png

