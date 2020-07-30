---
title: 快速入门：在 Windows 上创建 Java 应用
description: 在数分钟内将第一个 Java Hello World 部署到 Windows 上的 Azure 应用服务。 可以通过 Maven 的 Azure Web 应用插件方便地部署 Java 应用。
keywords: azure, 应用服务, web 应用, windows, java, maven, 快速入门
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: ca3c7d6bc6621c4b82a44431ae313384c1653f79
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324227"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>快速入门：在 Windows 上的 Azure 应用服务中创建 Java 应用

> [!NOTE]
> 本文将应用部署到 Windows 上的应用服务。 若要部署到 _Linux_上的应用服务，请参阅[在 Linux 上创建 Java Web 应用](./containers/quickstart-java.md)。
>

[Azure 应用服务](overview.md)提供高度可缩放、自修复的 Web 托管服务。  本快速入门介绍如何将 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 与[用于 Maven 的 Azure Web 应用插件](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)配合使用来部署 Java Web 存档 (WAR) 文件。

> [!NOTE]
> 也可使用 IntelliJ 和 Eclipse 等常见 IDE 执行相同的操作。 请查看 [Azure Toolkit for IntelliJ 快速入门](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app)或 [Azure Toolkit for Eclipse 快速入门](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)中的类似文档。
>
![在 Azure 应用服务中运行的示例应用](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>创建 Java 应用

在 Cloud Shell 提示符下，执行以下 Maven 命令来创建一个名为 `helloworld` 的新应用：

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

然后，将工作目录更改为项目文件夹：

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>配置 Maven 插件

部署到 Azure 应用服务的过程会自动从 Azure CLI 选取 Azure 凭据。 如果尚未安装 Azure CLI，Maven 插件将使用 Oauth 或设备登录名登录。 如果需要，请查看[使用 Maven 插件进行身份验证](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)了解详细信息。

可以在命令提示符中运行以下 maven 命令以配置部署，在第一步中为 Windows OS 选择“2”并按 Enter 接受默认配置，直到出现“确认(Y/N)”提示，然后按“y”完成配置    。 

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

示例过程如下所示：

```console
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use: 2
Define value for javaVersion(Default: 1.8): 
1. 1.7
2. 1.7.0_191_ZULU
3. 1.7.0_51
4. 1.7.0_71
5. 1.7.0_80
6. 1.8 [*]
7. 1.8.0_102
8. 1.8.0_111
9. 1.8.0_144
10. 1.8.0_172
11. 1.8.0_172_ZULU
12. 1.8.0_181
13. 1.8.0_181_ZULU
14. 1.8.0_202
15. 1.8.0_202_ZULU
16. 1.8.0_25
17. 1.8.0_60
18. 1.8.0_73
19. 1.8.0_92
20. 11
21. 11.0.2_ZULU
Enter index to use:
Define value for webContainer(Default: tomcat 8.5): 
1. jetty 9.1
2. jetty 9.1.0.20131115
3. jetty 9.3
4. jetty 9.3.13.20161014
5. tomcat 7.0
6. tomcat 7.0.50
7. tomcat 7.0.62
8. tomcat 8.0
9. tomcat 8.0.23
10. tomcat 8.5 [*]
11. tomcat 8.5.20
12. tomcat 8.5.31
13. tomcat 8.5.34
14. tomcat 8.5.37
15. tomcat 8.5.6
16. tomcat 9.0
17. tomcat 9.0.0
18. tomcat 9.0.12
19. tomcat 9.0.14
20. tomcat 9.0.8
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1590394316693
ResourceGroup : helloworld-1590394316693-rg
Region : westeurope
PricingTier : PremiumV2_P1v2
OS : Windows
Java : 1.8
WebContainer : tomcat 8.5
Deploy to slot : false
Confirm (Y/N)? :
[INFO] Saving configuration to pom.
```

> [!NOTE]
> 在本文中，我们仅使用在 WAR 文件中打包的 Java 应用。 该插件还支持 JAR Web 应用程序。若要试用，请访问[在 Linux 上将 Java SE JAR 文件部署到应用服务](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)。

打开 `pom.xml`，查看更新后的配置。

```bash
code pom.xml
```

如果需要，可以直接在 pom 文件中修改应用服务的配置，下面列出了一些常见配置：

 属性 | 必须 | 说明 | 版本
---|---|---|---
`<schemaVersion>` | false | 指定配置架构的版本。 支持的值是：`v1`、`v2`。 | 1.5.2
`<resourceGroup>` | true | 用于 Web 应用的 Azure 资源组。 | 0.1.0+
`<appName>` | true | Web 应用的名称。 | 0.1.0+
`<region>` | true | 指定将托管 Web 应用的区域；默认值为“westeurope”。 [支持的区域](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)部分中列出了所有有效区域。 | 0.1.0+
`<pricingTier>` | false | Web 应用的定价层。 默认值为 **P1V2**。| 0.1.0+
`<runtime>` | true | 运行时环境配置，可以在[此处](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)查看详细信息。 | 0.1.0+
`<deployment>` | true | 部署配置，可以在[此处](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)查看详细信息。 | 0.1.0+

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/javae2e?tutorial=app-service-web-get-started-java&step=config)

## <a name="deploy-the-app"></a>部署应用

部署到 Azure 应用服务的过程中会使用 Azure CLI 中的帐户凭据。 在继续操作之前[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

```azurecli
az login
```
然后可以使用以下命令将 Java 应用部署到 Azure：

```bash
mvn package azure-webapp:deploy
```

部署完成后，在 Web 浏览器中使用以下 URL 浏览到已部署的应用程序，例如 `http://<webapp>.azurewebsites.net/`。

![在 Azure 应用服务中运行的示例应用](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**祝贺你！** 现已将第一个 Java 应用部署到 Windows 应用服务。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [使用 Java 连接到 Azure SQL 数据库](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [使用 Java 连接到 Azure DB for MySQL](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [使用 Java 连接到 Azure DB for PostgreSQL](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [面向 Java 开发人员的 Azure 资源](/java/azure/)

> [!div class="nextstepaction"]
> [映射自定义域](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [详细了解 Azure 的 Maven 插件](https://github.com/microsoft/azure-maven-plugins)
