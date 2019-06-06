---
title: 连接到 Azure 媒体服务 v3 API-Java
description: 了解如何连接到媒体服务 v3 API 与 Java 配合使用。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: juliako
ms.openlocfilehash: b7ee54c852ce3332415b69ca6105b472dab0ab8a
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480270"
---
# <a name="connect-to-media-services-v3-api---java"></a>连接到媒体服务 v3 API-Java

本文介绍您如何连接到 Azure 媒体服务 v3 Java SDK 在方法中使用服务主体登录。

在本文中，Visual Studio Code 用于开发示例应用程序。

## <a name="prerequisites"></a>必备组件

- 请按照[使用 Visual Studio Code 通过编写 Java](https://code.visualstudio.com/docs/java/java-tutorial)安装：

   - JDK
   - Apache Maven
   - Java 扩展包
- 请务必设置`JAVA_HOME`和`PATH`环境变量。
- [创建媒体服务帐户](create-account-cli-how-to.md)。 请务必记住的资源组名称和媒体服务帐户名称。
- 按照中的步骤[访问 Api](access-api-cli-how-to.md)主题。 在后面的步骤中记录的订阅 ID、 应用程序 ID (客户端 ID)、 身份验证密钥 （机密） 和所需的租户 ID。

此外请查看：

- [在 Visual Studio Code 中的 Java](https://code.visualstudio.com/docs/languages/java)
- [在 VS Code 中的 Java 项目管理](https://code.visualstudio.com/docs/java/java-project)

## <a name="create-a-maven-project"></a>创建 Maven 项目

打开一个命令行工具和`cd`到想要创建项目的目录。
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

当运行该命令， `pom.xml`， `App.java`，以及创建其他文件。 

## <a name="add-dependencies"></a>添加依赖项

1. 在 Visual Studio Code 中，打开你的项目的文件夹
1. 找到并打开 `pom.xml`
1. 添加所需的依赖项

    ```xml
   <dependency>
     <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
     <artifactId>azure-mgmt-media</artifactId>
     <version>1.0.0-beta-3</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.rest</groupId>
     <artifactId>client-runtime</artifactId>
     <version>1.6.6</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-client-authentication</artifactId>
     <version>1.6.6</version>
   </dependency>
    ```

## <a name="connect-to-the-java-client"></a>连接到 Java 客户端

1. 打开`App.java`文件下`src\main\java\com\azure\ams`，并确保您的软件包不包含在顶部：

    ```java
    package com.azure.ams;
    ```
1. 在包语句下，添加这些 import 语句：
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. 若要创建发出请求所需的 Active Directory 凭据，将以下代码添加到 App 类的 main 方法，并设置时所获取的值[访问 Api](access-api-cli-how-to.md):
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. 运行应用。

## <a name="see-also"></a>另请参阅

- [媒体服务概念](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Java 参考](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>后续步骤

现在可以包括`import com.microsoft.azure.management.mediaservices.v2018_07_01.*;`并开始操作实体。
