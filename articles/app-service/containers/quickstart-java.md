---
title: 快速入门：创建 Linux Java 应用
description: 将第一个 Java 应用部署到 Azure 应用服务中的 Linux 容器即可开始使用应用服务上的 Linux 应用。
keywords: azure, 应用服务, web 应用, linux, java, maven, 快速入门
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 76521464127780d266ba58eab7306b8fe2930c2b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422214"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>快速入门：在 Linux 上的 Azure 应用服务中创建 Java 应用

[Linux 上的应用服务](app-service-linux-intro.md)使用 Linux 操作系统，提供高度可缩放的自修补 Web 托管服务。 本快速入门介绍如何将 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 与[用于 Azure 应用服务的 Maven 插件](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)配合使用来在 Linux 操作系统上部署 Java Web 存档 (WAR) 文件。

> [!NOTE]
>
> 也可使用 IntelliJ、Eclipse 和 VS Code 等常见 IDE 执行相同的操作。 请在 [Azure Toolkit for IntelliJ 快速入门](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app)、[Azure Toolkit for Eclipse 快速入门](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app) 或 [VS Code 快速入门](https://code.visualstudio.com/docs/java/java-webapp)查看类似文档。
>
![在 Azure 应用服务中运行的示例应用](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>创建 Java 应用

在 Cloud Shell 提示符下，执行以下 Maven 命令来创建一个名为 `helloworld` 的新应用：

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp"
```

## <a name="configure-the-maven-plugin"></a>配置 Maven 插件

部署到 Azure 应用服务的过程中会使用 Azure CLI 中的帐户凭据。 在继续操作之前[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

```azurecli
az login
```

然后，可以配置部署，在命令提示符下运行 maven 命令，并在出现“确认(Y/N)”  提示前一直按 **ENTER** 来使用默认配置，然后按“y”  完成配置。 
```cmd
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.8.0:config
```
示例过程如下所示：

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.8.0:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.8.0:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. Java 11
2. Java 8 [*]
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. TOMCAT 8.5 [*]
3. WILDFLY 14
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```

> [!NOTE]
> 在本文中，我们仅使用在 WAR 文件中打包的 Java 应用。 该插件还支持 JAR Web 应用程序。若要试用，请访问[在 Linux 上将 Java SE JAR 文件部署到应用服务](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)。

再次导航到 `pom.xml` 以查看插件配置是否已更新。如果需要，可以直接在 pom 文件中修改应用服务的其他配置，下面列出了一些常见配置：

 properties | 必选 | 说明 | 版本
---|---|---|---
`<schemaVersion>` | false | 指定配置架构的版本。 支持的值是：`v1`、`v2`。 | 1.5.2
`<resourceGroup>` | true | 用于 Web 应用的 Azure 资源组。 | 0.1.0+
`<appName>` | true | Web 应用的名称。 | 0.1.0+
[`<region>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) | true | 指定将托管 Web 应用的区域；默认值为“westeurope”  。 [支持的区域](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region)部分中列出了所有有效区域。 | 0.1.0+
[`<pricingTier>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme##pricingtier) | false | Web 应用的定价层。 默认值为 **P1V2**。| 0.1.0+
[`<runtime>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting) | true | 运行时环境配置，可以在[此处](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting)查看详细信息。 | 0.1.0+
[`<deployment>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting) | true | 部署配置，可以在[此处](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting)查看详细信息。 | 0.1.0+

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=config)

## <a name="deploy-the-app"></a>部署应用

使用以下命令将 Java 应用部署到 Azure：

```bash
mvn package azure-webapp:deploy
```

部署完成后，在 Web 浏览器中使用以下 URL 浏览到已部署的应用程序，例如 `http://<webapp>.azurewebsites.net`。 

![在 Azure 应用服务中运行的示例应用](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

祝贺你！  现已将第一个 Java 应用部署到 Linux 应用服务。

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>清理资源

在前面的步骤中，你在资源组中创建了 Azure 资源。 如果认为将来不需要这些资源，请通过门户删除资源组，或在 Cloud Shell 中运行以下命令来这样做：

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

此命令可能需要花费一点时间运行。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：将 Java 企业应用与 PostgreSQL 配合使用](tutorial-java-enterprise-postgresql-app.md)

> [!div class="nextstepaction"]
> [配置 Java 应用](configure-custom-container.md)

> [!div class="nextstepaction"]
> [将 CI/CD 与 Jenkins 配合使用](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [面向 Java 开发人员的其他 Azure 资源](/java/azure/)

> [!div class="nextstepaction"]
> [详细了解 Azure 的 Maven 插件](https://github.com/microsoft/azure-maven-plugins)
