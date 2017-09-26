---
title: "如何使用适用于 Azure Web 应用的 Maven 插件将 Azure 容器注册表中的 Spring Boot 应用部署到 Azure App Service"
description: "本教程介绍使用 Maven 插件将 Azure 容器注册表中的 Spring Boot 应用程序部署到 Azure App Service 的步骤。"
services: 
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/07/2017
ms.author: robmcm;kevinzha
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: f47ee59d72ea49d62be2cb435ebaf8bc841e4198
ms.contentlocale: zh-cn
ms.lasthandoff: 08/10/2017

---

# <a name="how-to-use-the-maven-plugin-for-azure-web-apps-to-deploy-a-spring-boot-app-in-azure-container-registry-to-azure-app-service"></a>如何使用适用于 Azure Web 应用的 Maven 插件将 Azure 容器注册表中的 Spring Boot 应用部署到 Azure App Service

[Spring Framework] 是一种常用的开源框架，可帮助 Java 开发人员创建 Web、移动和 API 应用程序。 本教程使用通过 [Spring Boot] 创建的示例应用，其为使用 Spring 进行快速入门的惯例方法。

本文演示如何将示例 Spring Boot 应用程序部署到 Azure 容器注册表以及在此之后如何使用适用于 Azure Web 应用的 Maven 插件将应用程序部署到 Azure App Service。

> [!NOTE]
>
> 适用于 Azure Web 应用的 Maven 插件当前提供预览版。 目前，仅支持 FTP 发布，但计划在未来支持其他功能。
>

## <a name="prerequisites"></a>先决条件

完成本教程中的步骤需要具备以下先决条件：

* Azure 订阅；如果没有 Azure 订阅，可激活 [MSDN 订阅者权益]或注册[免费 Azure 帐户]。
* [Azure 命令行接口 (CLI)]。
* 最新 [Java 开发工具包 (JDK)] 1.7 版或更高版本。
* Apache 的 [Maven] 生成工具（版本 3）。
* [Git] 客户端。
* [Docker] 客户端。

> [!NOTE]
>
> 由于本教程中的虚拟化要求，无法在虚拟机上执行本文中的步骤；必须使用启用了虚拟化功能的物理计算机。
>

## <a name="clone-the-sample-spring-boot-on-docker-web-app"></a>克隆 Docker Web 应用上的示例 Spring Boot

本部分将克隆容器化 Spring Boot 应用程序并进行本地测试。

1. 打开命令提示符或终端窗口，创建本地目录以存放 Spring Boot 应用程序，并更改为以下目录；例如：
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   - 或 -
   ```shell
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. 将 [Docker 上的 Spring Boot 入门]示例项目克隆到创建的目录中；例如：
   ```shell
   git clone -b private-registry https://github.com/Microsoft/gs-spring-boot-docker
   ```

1. 将目录更改为已完成项目；例如：
   ```shell
   cd gs-spring-boot-docker/complete
   ```

1. 使用 Maven 生成 JAR 文件；例如：
   ```shell
   mvn clean package
   ```

1. 创建 Web 应用后，使用 Maven 启动 Web 应用；例如：
   ```shell
   mvn spring-boot:run
   ```

1. 使用 Web 浏览器在本地浏览到 Web 应用并对其进行测试。 例如，如果有可用的 Curl，可以使用以下命令：
   ```shell
   curl http://localhost:8080
   ```

1. 应会显示以下消息：“Hello Docker World”

   ![本地浏览示例应用][SB01]

## <a name="create-an-azure-service-principal"></a>创建 Azure 服务主体

本部分将创建 Maven 插件在将容器部署到 Azure 时使用的 Azure 服务主体。

1. 打开命令提示符。

1. 通过使用 Azure CLI 登录到 Azure 帐户：
   ```azurecli
   az login
   ```
   按照说明完成登录过程。

1. 创建 Azure 服务主体：
   ```azurecli
   az ad sp create-for-rbac --name "uuuuuuuu" --password "pppppppp"
   ```
   其中，`uuuuuuuu` 是服务主体的用户名，`pppppppp` 是服务主体的密码。

1. Azure 使用与以下示例类似的 JSON 进行响应：
   ```json
   {
      "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "displayName": "uuuuuuuu",
      "name": "http://uuuuuuuu",
      "password": "pppppppp",
      "tenant": "tttttttt-tttt-tttt-tttt-tttttttttttt"
   }
   ```

   > [!NOTE]
   >
   > 当配置 Maven 插件以将容器部署到 Azure 时，会使用此 JSON 响应中的值。 `aaaaaaaa`、`uuuuuuuu`、`pppppppp` 和 `tttttttt` 是占位符值，在本示例中用于在下一部分中配置 Maven `settings.xml` 文件时，能够更加方便地将这些值映射到其各自的元素。
   >
   >

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>使用 Azure CLI 创建 Azure 容器注册表

1. 打开命令提示符。

1. 登录到 Azure 帐户：
   ```azurecli
   az login
   ```

1. 为本文中要使用的 Azure 资源创建资源组：
   ```azurecli
   az group create --name=wingtiptoysresources --location=westus
   ```
   将此示例中的 `wingtiptoysresources` 替换为资源组的唯一名称。

1. 为 Spring Boot 应用在资源组中创建私有 Azure 容器注册表： 
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoysresources --location westus --name wingtiptoysregistry --sku Basic
   ```
   将此示例中的 `wingtiptoysregistry` 替换为容器注册表的唯一名称。

1. 检索容器注册表的密码：
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```
   Azure 会响应密码；例如：
   ```json
   {
      "name": "password",
      "value": "xxxxxxxxxx"
   }
   ```

## <a name="add-your-azure-container-registry-and-azure-service-principal-to-your-maven-settings"></a>将 Azure 容器注册表和 Azure 服务主体添加到 Maven 设置

1. 在文本编辑器中打开 Maven `settings.xml` 文件；此文件可能位于如以下示例所示的路径中：
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

1. 将本文上一部分中的 Azure 容器注册表访问设置添加到 settings.xml 文件中的 `<servers>` 集合；例如：

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>xxxxxxxxxx</password>
      </server>
   </servers>
   ```
   其中：
   元素 | 说明
   ---|---|---
   `<id>` | 包含私有 Azure 容器注册表的名称。
   `<username>` | 包含私有 Azure 容器注册表的名称。
   `<password>` | 包含在本文上一部分中检索的密码。

1. 将本文先前部分中的 Azure 服务主体设置添加到 settings.xml 文件中的 `<servers>` 集合；例如：

   ```xml
   <servers>
      <server>
        <id>azure-auth</id>
         <configuration>
            <client>aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa</client>
            <tenant>tttttttt-tttt-tttt-tttt-tttttttttttt</tenant>
            <key>pppppppp</key>
            <environment>AZURE</environment>
         </configuration>
      </server>
   </servers>
   ```
   其中：
   元素 | 说明
   ---|---|---
   `<id>` | 指定在将 Web 应用部署到 Azure 时，Maven 用于查找安全设置的唯一名称。
   `<client>` | 包含服务主体的 `appId` 值。
   `<tenant>` | 包含服务主体的 `tenant` 值。
   `<key>` | 包含服务主体的 `password` 值。
   `<environment>` | 定义目标 Azure 云环境，此示例中为 `AZURE`。 （[适用于 Azure Web 应用的 Maven插件]文档中提供了完整的环境列表）

1. 保存并关闭 settings.xml 文件。

## <a name="build-your-docker-container-image-and-push-it-to-your-azure-container-registry"></a>生成 Docker 容器映像并将其推送到 Azure 容器注册表

1. 导航到 Spring Boot 应用程序的已完成项目目录（例如，“C:\SpringBoot\gs-spring-boot-docker\complete”或“/users/robert/SpringBoot/gs-spring-boot-docker/complete”），并使用文本编辑器打开 pom.xml 文件。

1. 使用本教程上一部分中的 Azure 容器注册表的登录服务器值更新 pom.xml 文件中的 `<properties>` 集合，例如：

   ```xml
   <properties>
      <azure.containerRegistry>wingtiptoysregistry</azure.containerRegistry>
      <docker.image.prefix>${azure.containerRegistry}.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <maven.build.timestamp.format>yyyyMMddHHmmssSSS</maven.build.timestamp.format>
   </properties>
   ```
   其中：
   元素 | 说明
   ---|---|---
   `<azure.containerRegistry>` | 指定私有 Azure 容器注册表的名称。
   `<docker.image.prefix>` | 指定私有 Azure 容器注册表的 URL，将“.azurecr.io”附加到私有容器注册表名称后即可派生为此 URL。

1. 确保 pom.xml 文件中 Docker 插件的 `<plugin>` 包含正确的登录服务器地址属性和本教程上一步骤中的注册表名称。 例如：

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <registryUrl>https://${docker.image.prefix}</registryUrl>
         <serverId>${azure.containerRegistry}</serverId>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
      </configuration>
   </plugin>
   ```
   其中：
   元素 | 说明
   ---|---|---
   `<serverId>` | 指定包含私有 Azure 容器注册表名称的属性。
   `<registryUrl>` | 指定包含私有 Azure 容器注册表 URL 的属性。

1. 导航到 Spring Boot 应用程序的已完成项目目录，然后运行以下命令以重新生成应用程序，并将容器推送到 Azure 容器注册表：

   ```
   mvn package docker:build -DpushImage 
   ```

1. 可选：浏览至 [Azure 门户]，验证容器注册表中是否具有名为 gs-spring-boot-docker 的 Docker 容器映像。

   ![验证 Azure 门户中的容器][CR01]

## <a name="customize-your-pomxml-then-build-and-deploy-your-container-to-azure"></a>自定义 pom.xml，然后生成容器并将容器部署到 Azure

在文本编辑器中打开 Spring Boot 应用程序的 `pom.xml` 文件，然后找到 `azure-webapp-maven-plugin` 的 `<plugin>` 元素。 该元素应类似于以下示例：

   ```xml
   <plugin>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-webapp-maven-plugin</artifactId>
      <version>0.1.3</version>
      <configuration>
         <authentication>
            <serverId>azure-auth</serverId>
         </authentication>
         <resourceGroup>wingtiptoysresources</resourceGroup>
         <appName>maven-linux-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <containerSettings>
            <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
            <registryUrl>https://${docker.image.prefix}</registryUrl>
            <serverId>${azure.containerRegistry}</serverId>
         </containerSettings>
         <appSettings>
            <property>
               <name>PORT</name>
               <value>8080</value>
            </property>
         </appSettings>
      </configuration>
   </plugin>
   ```

可以为 Maven 插件修改几个值，[适用于 Azure Web 应用的 Maven插件]文档中提供了这些元素各自的详细描述。 尽管如此，在本文中有仍几个值得注意的值：

元素 | 说明
---|---|---
`<version>` | 指定[适用于 Azure Web 应用的 Maven插件]的版本。 应检查 [Maven 中央存储库](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-webapp-maven-plugin%22)中列出的版本，确保使用最新版本。
`<authentication>` | 指定 Azure 的身份验证信息，该信息在本示例中含有包含 `azure-auth` 的 `<serverId>` 元素；Maven 使用该值查找在本文前面部分定义的 Maven settings.xml 文件中的 Azure 服务主体值。
`<resourceGroup>` | 指定目标资源组，在此示例中为 `wingtiptoysresources`。 如果资源组不存在，则会在部署过程中进行创建。
`<appName>` | 指定 Web 应用的目标名称。 在此示例中，目标名称为 `maven-linux-app-${maven.build.timestamp}`，此示例附加​​了 `${maven.build.timestamp}` 后缀以避免冲突。 （时间戳是可选项；可为应用名称指定任何唯一的字符串。）
`<region>` | 指定目标区域，在此示例中为 `westus`。 （[适用于 Azure Web 应用的 Maven插件]文档中提供了完整列表。）
`<containerSettings>` | 指定包含容器名称和 URL 的属性。
`<appSettings>` | 指定 Maven 在将 Web 应用部署到 Azure 时使用的任何唯一设置。 在此示例中，`<property>` 元素包含指定应用端口的子元素的名称/值对。

> [!NOTE]
>
> 仅当更改默认端口时，才需要执行此示例中更改端口号的设置。
>

1. 在之前使用的命令提示符或终端窗口中，如果对 pom.xml 文件进行了任何更改，请使用 Maven 重新生成 JAR 文件；例如：
   ```shell
   mvn clean package
   ```

1. 使用 Maven 将 Web 应用部署到 Azure；例如：
   ```shell
   mvn azure-webapp:deploy
   ```

Maven 会将 Web 应用部署到 Azure；如果 Web 应用不存在，则将创建一个。

> [!NOTE]
>
> 如果 pom.xml 文件的 `<region>` 元素中指定的区域在启动部署时没有足够的可用服务器，则可能会看到类似于以下示例的错误：
>
> ```
> [INFO] Start deploying to Web App maven-linux-app-20170804...
> [INFO] ------------------------------------------------------------------------
> [INFO] BUILD FAILURE
> [INFO] ------------------------------------------------------------------------
> [INFO] Total time: 31.059 s
> [INFO] Finished at: 2017-08-04T12:15:47-07:00
> [INFO] Final Memory: 51M/279M
> [INFO] ------------------------------------------------------------------------
> [ERROR] Failed to execute goal com.microsoft.azure:azure-webapp-maven-plugin:0.1.3:deploy (default-cli) on project gs-spring-boot-docker: null: MojoExecutionException: CloudException: OnError while emitting onNext value: retrofit2.Response.class
> ```
>
> 如果发生这种情况，可以指定另一个区域并重新运行 Maven 命令来部署应用程序。
>
>

Web 部署完成后即可使用 [Azure 门户]进行管理。

* Web 应用将会在“应用服务”中列出：

   ![Azure 门户应用服务中列出的 Web 应用][AP01]

* Web 应用的 URL 会在 Web 应用的“概述”中列出：

   ![确定 Web 应用的 URL][AP02]

## <a name="next-steps"></a>后续步骤

有关本文中讨论的各项技术的详细信息，请参阅以下文章：

* [适用于 Azure Web 应用的 Maven插件]

* [通过 Azure CLI 登录到 Azure](/azure/xplat-cli-connect)

* [使用 Azure CLI 2.0 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Maven 设置参考](https://maven.apache.org/settings.html)

* [适用于 Maven 的 Docker 插件]

<!-- URL List -->

[Azure 命令行接口 (CLI)]: /cli/azure/overview
[Azure Container Service (ACS)]: https://azure.microsoft.com/services/container-service/
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Azure 门户]: https://portal.azure.com/
[适用于 Azure Web 应用的 Maven插件]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Using a custom Docker image for Azure Web App on Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[适用于 Maven 的 Docker 插件]: https://github.com/spotify/docker-maven-plugin
[免费 Azure 帐户]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN 订阅者权益]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Docker 上的 Spring Boot 入门]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/SB01.png
[CR01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/CR01.png
[AP01]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/AP01.png
[AP02]: ./media/app-service-web-deploy-spring-boot-app-from-container-registry-using-maven-plugin/AP02.png

