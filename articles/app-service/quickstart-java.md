---
title: 快速入门：在 Azure 应用服务中创建 Java 应用
description: 在数分钟内将第一个 Java Hello World 部署到 Azure 应用服务。 可以通过 Maven 的 Azure Web 应用插件方便地部署 Java 应用。
keywords: azure, 应用服务, web 应用, windows, linux, java, maven, 快速入门
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 7f8e87b22e3d8f6e265789f910863b2790024cbf
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532403"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>快速入门：在 Azure 应用服务中创建 Java 应用

[Azure 应用服务](overview.md)提供高度可缩放、自修复的 Web 托管服务。  本快速入门介绍如何将 [Azure CLI](/cli/azure/get-started-with-azure-cli) 与[用于 Maven 的 Azure Web 应用插件](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)配合使用来部署 .jar 文件或 .war 文件。 使用选项卡在 Java SE 和 Tomcat 说明之间切换。


> [!NOTE]
> 也可使用 IntelliJ 和 Eclipse 等常见 IDE 执行相同的操作。 请查看 [Azure Toolkit for IntelliJ 快速入门](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app)或 [Azure Toolkit for Eclipse 快速入门](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)中的类似文档。


![在 Azure 应用服务中运行的示例应用](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>创建 Java 应用

# <a name="java-se"></a>[Java SE](#tab/javase)

克隆 [Spring Boot 入门](https://github.com/spring-guides/gs-spring-boot)示例项目。

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

将目录更改为已完成项目。

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

在 Cloud Shell 提示符下，执行以下 Maven 命令来创建一个名为 `helloworld` 的新应用：

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

然后，将工作目录更改为项目文件夹：

```bash
cd helloworld
```

---

## <a name="configure-the-maven-plugin"></a>配置 Maven 插件

部署到 Azure 应用服务的过程会自动使用 Azure CLI 中的 Azure 凭据。 如果未在本地安装 Azure CLI，则 Maven 插件将使用 Oauth 或设备登录名进行身份验证。 有关详细信息，请参阅 [Maven 插件的身份验证](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)。

运行下面的 Maven 命令来配置部署。 此命令将帮助你设置应用服务操作系统、Java 版本和 Tomcat 版本。

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.10.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. 出现提示时，请输入 `2` 来选择 Windows。
2. 按 Enter 使用默认的 Java 版本 1.8。
3. 最后，出现最后一个提示时按 Enter 来确认所做的选择。

    摘要输出将类似于下面所示的代码片段。

    ```
    Please confirm webapp properties
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : PremiumV2_P1v2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. 出现提示时，请输入 `2` 来选择 Windows。
1. 按 Enter 使用默认的 Java 版本 1.8。
1. 按 Enter 使用默认 Web 容器 Tomcat 8.5。
1. 最后，出现最后一个提示时按 Enter 来确认所做的选择。

    摘要输出将类似于下面所示的代码片段。

    ```
    Please confirm webapp properties
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : PremiumV2_P1v2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. 出现提示时，按 Enter 来选择 linux。
2. 按 Enter 使用默认的 Java 版本 1.8。
3. 最后，出现最后一个提示时按 Enter 来确认所做的选择。

    ```
    Please confirm webapp properties
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : PremiumV2_P1v2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. 出现提示时，按 Enter 来选择 linux。
1. 按 Enter 使用默认的 Java 版本 1.8。
1. 按 Enter 使用默认 Web 容器 Tomcat 8.5。
1. 最后，出现最后一个提示时按 Enter 来确认所做的选择。

    ```
    Please confirm webapp properties
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : PremiumV2_P1v2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

如果需要，可以直接在 `pom.xml` 中修改应用服务的配置。 下面列出了一些常见配置：

属性 | 必选 | 说明 | 版本
---|---|---|---
`<schemaVersion>` | false | 指定配置架构的版本。 支持的值是：`v1`、`v2`。 | 1.5.2
`<resourceGroup>` | true | 用于 Web 应用的 Azure 资源组。 | 0.1.0+
`<appName>` | true | Web 应用的名称。 | 0.1.0+
`<region>` | true | 指定将托管 Web 应用的区域；默认值为“westeurope”。 [支持的区域](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)部分中列出了所有有效区域。 | 0.1.0+
`<pricingTier>` | false | Web 应用的定价层。 默认值为 **P1V2**。| 0.1.0+
`<runtime>` | true | 运行时环境配置，可以在[此处](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)查看详细信息。 | 0.1.0+
`<deployment>` | true | 部署配置，可以在[此处](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)查看详细信息。 | 0.1.0+

请注意 `<appName>` 和 `<resourceGroup>` 的值（在演示中相应地为 `helloworld-1590394316693` 和 `helloworld-1590394316693-rg`），后面会使用它们。

> [!div class="nextstepaction"]
> [我遇到了问题](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>部署应用

Maven 插件会使用 Azure CLI 中的帐户凭据来部署到应用服务。 在继续操作之前[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

```azurecli
az login
```

然后你可使用以下命令将 Java 应用部署到 Azure。

```bash
mvn package azure-webapp:deploy
```

部署完成后，应用程序会在 `http://<appName>.azurewebsites.net/`（在演示中为 `http://helloworld-1590394316693.azurewebsites.net`）处准备就绪。 在本地 Web 浏览器中打开 url，你应该会看到

![在 Azure 应用服务中运行的示例应用](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

祝贺你！ 现已将第一个 Java 应用部署到应用服务。

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
> [使用 Java 连接到 Azure SQL 数据库](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)

> [!div class="nextstepaction"]
> [使用 Java 连接到 Azure DB for MySQL](../mysql/connect-java.md)

> [!div class="nextstepaction"]
> [使用 Java 连接到 Azure DB for PostgreSQL](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [面向 Java 开发人员的 Azure 资源](/java/azure/)

> [!div class="nextstepaction"]
> [配置 Java 应用](configure-language-java.md)

> [!div class="nextstepaction"]
> [将 CI/CD 与 Jenkins 配合使用](/azure/developer/jenkins/deploy-to-azure-app-service-using-plugin)

> [!div class="nextstepaction"]
> [映射自定义域](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [详细了解 Azure 的 Maven 插件](https://github.com/microsoft/azure-maven-plugins)
