---
title: 在 Linux 上创建 Java Web 应用 - Azure 应用服务
description: 本快速入门介绍如何在数分钟内在 Linux 上的 Azure 应用服务中部署第一个 Java Hello World。
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 12/10/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: e4ae0c944323f18876ffcd1ee5c21aa29e9806cc
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2019
ms.locfileid: "56650812"
---
# <a name="quickstart-create-a-java-app-in-app-service-on-linux"></a>快速入门：在 Linux 上的应用服务中创建 Java 应用

[Linux 上的应用服务](app-service-linux-intro.md)使用 Linux 操作系统，提供高度可缩放的自修补 Web 托管服务。 本快速入门介绍如何将 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 与[用于 Azure Web 应用的 Maven 插件（预览版）](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)配合使用来部署 Java Web 存档 (WAR) 文件。

![在 Azure 中运行应用的示例](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>创建 Java 应用

在 Cloud Shell 提示符下，执行以下 Maven 命令来创建一个名为 `helloworld` 的新应用：

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>配置 Maven 插件

若要从 Maven 进行部署，请在 Cloud Shell 中使用代码编辑器打开 `helloworld` 目录中的项目 `pom.xml` 文件。 

```bash
code pom.xml
```

然后在 `pom.xml` 文件的 `<build>` 元素内添加以下插件定义。

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Linux           -->
    <!--*************************************************-->
      
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.5.3</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
   
            <!-- Java Runtime Stack for App on Linux-->
            <runtime>
                <os>linux</os>
                <javaVersion>jre8</javaVersion>
                <webContainer>tomcat 8.5</webContainer>
            </runtime>
        </configuration>
    </plugin>
</plugins>
```    


> [!NOTE] 
> 在本文中，我们仅使用在 WAR 文件中打包的 Java 应用。 该插件还支持 JAR Web 应用程序。若要试用，请访问[在 Linux 上将 Java SE JAR 文件部署到应用服务](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)。


更新插件配置中的以下占位符：

| 占位符 | 说明 |
| ----------- | ----------- |
| `RESOURCEGROUP_NAME` | 要在其中创建应用的新资源组的名称。 通过将应用的所有资源都放在一个组中，可以一起管理它们。 例如，删除资源组会删除与该应用关联的所有资源。 使用唯一的新资源组名称（例如 *TestResources*）更新此值。 将在后面的部分使用此资源组名称来清除所有 Azure 资源。 |
| `WEBAPP_NAME` | 应用名称将成为部署到 Azure 时应用 (WEBAPP_NAME.azurewebsites.net) 的主机名的一部分。 使用将托管 Java 应用的新应用服务应用的唯一名称（例如 *contoso*）更新此值。 |
| `REGION` | 托管着应用的 Azure 区域，例如 `westus2`。 可以从 Cloud Shell 或 CLI 使用 `az account list-locations` 命令获取区域列表。 |

## <a name="deploy-the-app"></a>部署应用

使用以下命令将 Java 应用部署到 Azure：

```bash
mvn package azure-webapp:deploy
```

部署完成后，在 Web 浏览器中使用以下 URL 浏览到已部署的应用程序，例如 `http://<webapp>.azurewebsites.net/helloworld`。 

![在 Azure 中运行应用的示例](media/quickstart-java/java-hello-world-in-browser-curl.png)

祝贺你！ 现已将第一个 Java 应用部署到 Linux 应用服务。


[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 Maven 创建了一个 Java 应用，配置了[适用于 Azure Web 应用的 Maven 插件](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)，然后将 Web 存档打包的 Java 应用部署到了 Linux 上的应用服务。 有关在 Linux 上的应用服务中托管 Java 应用程序的详细信息，请参阅以下教程和操作指南文章。

- [教程：使用 PostgreSQL 部署 Java 企业应用](tutorial-java-enterprise-postgresql-app.md)
- [配置 Tomcat 数据源](app-service-linux-java.md#connecting-to-data-sources)
- [将 CI/CD 与 Jenkins 配合使用](/azure/jenkins/deploy-jenkins-app-service-plugin)
- [设置应用程序性能监视工具](how-to-java-apm-monitoring.md)

