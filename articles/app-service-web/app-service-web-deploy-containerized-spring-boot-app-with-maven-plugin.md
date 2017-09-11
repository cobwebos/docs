---
title: "如何使用适用于 Azure Web 应用的 Maven 插件将容器化 Spring Boot 应用部署到 Azure"
description: "了解如何使用适用于 Azure Web 应用的 Maven 插件将 Spring Boot 应用部署到 Azure。"
services: app-service\web
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/07/2017
ms.author: robmcm;kevinzha
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: 883040590291cee94daa227fbc6715ad4be0b393
ms.contentlocale: zh-cn
ms.lasthandoff: 08/10/2017

---

# <a name="how-to-use-the-maven-plugin-for-azure-web-apps-to-deploy-a-containerized-spring-boot-app-to-azure"></a>如何使用适用于 Azure Web 应用的 Maven 插件将容器化 Spring Boot 应用部署到 Azure

用于 [Apache Maven](http://maven.apache.org/) 的[适用于 Azure Web 应用的 Maven 插件](https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin)提供了 Azure App Service 到 Maven 项目的无缝集成，并简化了开发人员将 Web 应用部署到 Azure App Service 的过程。

本文演示如何使用适用于 Azure Web 应用的 Maven 插件将 Docker 容器中的示例 Spring Boot 应用程序部署到 Azure App Service。

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
   git clone https://github.com/microsoft/gs-spring-boot-docker
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

## <a name="create-an-azure-service-principal"></a>创建 Azure 服务主体

本部分将创建 Maven 插件在将容器部署到 Azure 时使用的 Azure 服务主体。

1. 打开命令提示符。

1. 通过使用 Azure CLI 登录到 Azure 帐户：
   ```shell
   az login
   ```
   按照说明完成登录过程。

1. 创建 Azure 服务主体：
   ```shell
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

## <a name="configure-maven-to-use-your-azure-service-principal"></a>配置 Maven 以使用 Azure 服务主体

在本部分中，将使用 Azure 服务主体中的值配置 Maven 在将容器部署到 Azure 时要使用的身份验证。

1. 在文本编辑器中打开 Maven `settings.xml` 文件；此文件可能位于如以下示例所示的路径中：
   * `/etc/maven/settings.xml`
   * `%ProgramFiles%\apache-maven\3.5.0\conf\settings.xml`
   * `$HOME/.m2/settings.xml`

1. 将本教程上一部分中的 Azure 服务主体设置添加到 settings.xml 文件中的 `<servers>` 集合；例如：

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

## <a name="optional-deploy-your-local-docker-file-to-docker-hub"></a>可选：将本地 Docker 文件部署到 Docker 中心

如果拥有 Docker 帐户，则可以在本地构建 Docker 容器映像，并将其推送到 Docker 中心。 为此，请按照以下步骤操作。

1. 在文本编辑器中打开 Spring Boot 应用程序的 `pom.xml` 文件。

1. 找到 `<containerSettings>` 元素的 `<imageName>` 子元素。

1. 使用你的 Docker 帐户名更新 `${docker.image.prefix}` 值：
   ```xml
   <containerSettings>
      <imageName>mydockeraccountname/${project.artifactId}</imageName>
   </containerSettings>
   ```

1. 选择以下部署方法之一：

   * 使用 Maven 在本地构建容器映像，然后使用 Docker 将容器推送到 Docker 中心：
      ```shell
      mvn clean package docker:build
      docker push
      ```
   
   * 如果安装了[适用于 Maven 的 Docker 插件]，则可以使用 `-DpushImage` 参数自动将容器映像构建到 Docker 中心：
      ```shell
      mvn clean package docker:build -DpushImage
      ```

## <a name="optional-customize-your-pomxml-before-deploying-your-container-to-azure"></a>可选：在将容器部署到 Azure 之前自定义 pom.xml

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
         <resourceGroup>maven-plugin</resourceGroup>
         <appName>maven-linux-app-${maven.build.timestamp}</appName>
         <region>westus</region>
         <containerSettings>
            <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
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
`<resourceGroup>` | 指定目标资源组，在此示例中为 `maven-plugin`。 如果资源组不存在，则会在部署过程中进行创建。
`<appName>` | 指定 Web 应用的目标名称。 在此示例中，目标名称为 `maven-linux-app-${maven.build.timestamp}`，此示例附加​​了 `${maven.build.timestamp}` 后缀以避免冲突。 （时间戳是可选项；可为应用名称指定任何唯一的字符串。）
`<region>` | 指定目标区域，在此示例中为 `westus`。 （[适用于 Azure Web 应用的 Maven插件]文档中提供了完整列表。）
`<appSettings>` | 指定 Maven 在将 Web 应用部署到 Azure 时使用的任何唯一设置。 在此示例中，`<property>` 元素包含指定应用端口的子元素的名称/值对。

> [!NOTE]
>
> 仅当更改默认端口时，才需要执行此示例中更改端口号的设置。
>

## <a name="build-and-deploy-your-container-to-azure"></a>构建容器并将其部署到 Azure

配置了本文前面部分中的所有设置后，就可以将容器部署到 Azure。 为此，请按照以下步骤操作：

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

<!--
##  OPTIONAL: Configure the embedded Tomcat server to run on a different port

The embedded Tomcat server in the sample Spring Boot application is configured to run on port 8080 by default. However, if you want to run the embedded Tomcat server to run on a different port, such as port 80 for local testing, you can configure the port by using the following steps.

1. Go to the *resources* directory (or create the directory if it does not exist); for example:
   ```shell
   cd src/main/resources
   ```

1. Open the *application.yml* file in a text editor if it exists, or create a new YAML file if it does not exist.

1. Modify the **server** setting so that the server runs on port 80; for example:
   ```yaml
   server:
      port: 80
   ```

1. Save and close the *application.yml* file.
-->

## <a name="next-steps"></a>后续步骤

有关本文中讨论的各项技术的详细信息，请参阅以下文章：

* [适用于 Azure Web 应用的 Maven插件]

* [通过 Azure CLI 登录到 Azure](/azure/xplat-cli-connect)

* [如何使用适用于 Azure Web 应用的 Maven 插件将 Spring Boot 应用部署到 Azure App Service](app-service-web-deploy-spring-boot-app-with-maven-plugin.md)

* [使用 Azure CLI 2.0 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)

* [Maven 设置参考](https://maven.apache.org/settings.html)

* [适用于 Maven 的 Docker 插件]

<!-- URL List -->

[Azure 命令行接口 (CLI)]: /cli/azure/overview
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Azure 门户]: https://portal.azure.com/
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
[适用于 Azure Web 应用的 Maven插件]: https://github.com/Microsoft/azure-maven-plugins/tree/master/azure-webapp-maven-plugin

<!-- IMG List -->

[AP01]: ./media/app-service-web-deploy-containerized-spring-boot-app-with-maven-plugin/AP01.png
[AP02]: ./media/app-service-web-deploy-containerized-spring-boot-app-with-maven-plugin/AP02.png

