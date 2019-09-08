---
title: 在 Windows 上创建 Java Web 应用 - Azure 应用服务
description: 本快速入门介绍如何在数分钟内在 Windows 上的 Azure 应用服务中部署第一个 Java Hello World。
keywords: azure, 应用服务, web 应用, windows, java, maven, 快速入门
services: app-service\web
documentationcenter: ''
author: msangapu-msft
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019
ms.openlocfilehash: 483adcccff9f49b3335a453f43fc667dd4f0e61b
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171317"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>快速入门：在 Windows 上的 Azure 应用服务中创建 Java 应用

> [!NOTE]
> 本文将应用部署到 Windows 上的应用服务。 若要部署到基于 _Linux_ 的应用服务，请参阅[在 Linux 上创建 Java Web 应用](./containers/quickstart-java.md)。
>

[Azure 应用服务](overview.md)提供高度可缩放、自修补的 Web 托管服务。  本快速入门介绍如何将 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 与[用于 Azure 应用服务的 Maven 插件](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)配合使用来部署 Java Web 存档 (WAR) 文件。

> [!NOTE]
> 也可使用 IntelliJ 和 Eclipse 等常见 IDE 执行相同的操作。 请查看 [Azure Toolkit for IntelliJ 快速入门](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app)或 [Azure Toolkit for Eclipse 快速入门](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app)中的类似文档。
>
![在 Azure 中运行应用的示例](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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
    <!-- Deploy to Tomcat in App Service Windows         -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>${SUBSCRIPTION_ID}</subscriptionId>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            <!-- Java Runtime Stack for App Service on Windows-->
            <runtime>
                <os>windows</os>
                <javaVersion>1.8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```

> [!NOTE]
> 在本文中，我们仅使用在 WAR 文件中打包的 Java 应用。 该插件还支持 JAR Web 应用程序。若要试用，请访问[在 Linux 上将 Java SE JAR 文件部署到应用服务](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)。


更新插件配置中的以下占位符：

| 占位符 | 说明 |
| ----------- | ----------- |
| `SUBSCRIPTION_ID` | 想要将应用部署到的订阅的唯一 ID。 可以使用 `az account show` 命令从 Cloud Shell 或 CLI 中找到默认订阅的 ID。 对于所有可用的订阅，请使用 `az account list` 命令。|
| `RESOURCEGROUP_NAME` | 要在其中创建应用的新资源组的名称。 通过将应用的所有资源都放在一个组中，可以一起管理它们。 例如，删除资源组会删除与该应用关联的所有资源。 使用唯一的新资源组名称（例如 *myResourceGroup*）更新此值。 将在后面的部分使用此资源组名称来清除所有 Azure 资源。 |
| `WEBAPP_NAME` | 应用名称将成为部署到 Azure 时应用 (WEBAPP_NAME.azurewebsites.net) 的主机名的一部分。 使用将托管 Java 应用的新应用服务应用的唯一名称（例如 *contoso*）更新此值。 |
| `REGION` | 托管着应用的 Azure 区域，例如 westus2  。 可以从 Cloud Shell 或 CLI 使用 `az account list-locations` 命令获取区域列表。 |

## <a name="deploy-the-app"></a>部署应用

使用以下命令将 Java 应用部署到 Azure：

```bash
mvn package azure-webapp:deploy
```

部署完成后，在 Web 浏览器中使用以下 URL 浏览到已部署的应用程序，例如 `http://<webapp>.azurewebsites.net/`。

![在 Azure 中运行应用的示例](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

祝贺你！  现已将第一个 Java 应用部署到 Windows 应用服务。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [面向 Java 开发人员的 Azure 资源](/java/azure/)

> [!div class="nextstepaction"]
> [映射自定义域](app-service-web-tutorial-custom-domain.md)
