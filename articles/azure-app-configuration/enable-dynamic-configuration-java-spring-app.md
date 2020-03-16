---
title: 在 Spring Boot 应用中使用动态配置
titleSuffix: Azure App Configuration
description: 了解如何动态更新 Spring Boot 应用的配置数据
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.author: lcozzens
ms.openlocfilehash: 6445b9707273d273c562b7d643da34f5ba26e1fc
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967488"
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
    <version>1.1.2</version>
</dependency>
```

**Spring Cloud 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

保存文件，然后正常生成并运行应用程序。

## <a name="next-steps"></a>后续步骤

在本教程中，你已启用 Spring Boot 应用，可以通过应用程序配置动态刷新配置设置。 有关详细信息，请参阅 [Azure 上的 Spring](https://docs.microsoft.com/java/azure/spring-framework/)。
