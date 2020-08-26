---
title: 连接到 Azure 媒体服务 v3 API - Java
description: 本文介绍如何通过 Java 连接到 Azure 媒体服务 v3 API。
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
ms.date: 09/18/2019
ms.custom: devx-track-java
ms.author: juliako
ms.openlocfilehash: 098e1db7470124dc7c15b3ee65d6ab9cb3fadabd
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319824"
---
# <a name="connect-to-media-services-v3-api---java"></a>连接到媒体服务 v3 API - Java

本文介绍如何使用服务主体登录方法连接到 Azure 媒体服务 v3 Java SDK。

在本文中，Visual Studio Code 用于开发示例应用。

## <a name="prerequisites"></a>必备条件

- 按照 [Writing Java with Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial)（使用 Visual Studio Code 编写 Java）中的说明安装以下内容：

   - JDK
   - Apache Maven
   - Java 扩展包
- 请确保设置 `JAVA_HOME` 和 `PATH` 环境变量。
- [创建媒体服务帐户](./create-account-howto.md)。 请务必记住资源组名称和媒体服务帐户名称。
- 按照[访问 API](./access-api-howto.md) 主题中的步骤进行操作。 记下订阅 ID、应用程序 ID（客户端 ID）、身份验证密钥（机密）和租户 ID，以便在后面的步骤中使用。

另请参阅：

- [Java in Visual Studio Code](https://code.visualstudio.com/docs/languages/java)（Visual Studio Code 中的 Java）
- [Java Project Management in VS Code](https://code.visualstudio.com/docs/java/java-project)（VS Code 中的 Java 项目管理）

> [!IMPORTANT]
> 查看[命名约定](media-services-apis-overview.md#naming-conventions)。

## <a name="create-a-maven-project"></a>创建 Maven 项目

打开命令行工具，使用 `cd` 命令转到需要在其中创建此项目的目录。
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

运行此命令时，会创建 `pom.xml`、`App.java` 和其他文件。 

## <a name="add-dependencies"></a>添加依赖项

1. 在 Visual Studio Code 中，打开项目所在的文件夹
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

1. 打开 `src\main\java\com\azure\ams` 下的 `App.java` 文件，确保包包括在顶部：

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
1. 若要创建发出请求所需的 Active Directory 凭据，请将以下代码添加到 App 类的 main 方法，然后设置从[访问 API](./access-api-howto.md) 获取的值：
   
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

现在可以包括 `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` 并开始操作实体。

有关更多代码示例，请参阅 [Java SDK 示例](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/)存储库。
