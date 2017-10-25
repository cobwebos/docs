---
title: "在 Azure 容器服务中将 Spring Boot Web 应用部署于 Linux 上 | Microsoft Docs"
description: "本教程将指导用户完成在 Microsoft Azure 中将 Spring Boot 应用程序部署为 Linux Web 应用的步骤。"
services: container-service
documentationcenter: java
author: rmcmurray
manager: cfowler
editor: 
ms.assetid: 
ms.service: container-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 08/04/2017
ms.author: asirveda;robmcm
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 5f0b470bd46cfeaf00b3092dbe9db507ed50f622
ms.contentlocale: zh-cn
ms.lasthandoff: 08/12/2017

---

# <a name="deploy-a-spring-boot-application-on-linux-in-the-azure-container-service"></a>在 Azure 容器服务中将 Spring Boot 应用程序部署于 Linux 上

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

[Spring Framework] 是一种开放源代码解决方案，可帮助 Java 开发人员创建企业级应用程序。 基于该平台构建的其中一个更常用的项目是 [Spring Boot]，该项目提供了一种用于创建独立 Java 应用程序的简化方法。

**[Docker]** 是一种开放源代码解决方案，可帮助开发人员自动部署、扩展和管理在容器中运行的应用程序。

本教程将指导用户完成以下步骤：使用 Docker 在 [Azure 容器服务 (ACS)] 中开发 Spring Boot 应用程序并将其部署到 Linux 主机。

## <a name="prerequisites"></a>先决条件

完成本教程中的步骤需要具备以下先决条件：

* Azure 订阅；如果没有 Azure 订阅，可激活 [MSDN 订阅者权益]或注册[免费 Azure 帐户]。
* [Azure 命令行接口 (CLI)]。
* 最新的 [Java 开发人员工具包 (JDK)]。
* Apache 的 [Maven] 生成工具（版本 3）。
* [Git] 客户端。
* [Docker] 客户端。

> [!NOTE]
>
> 由于本教程中的虚拟化要求，无法在虚拟机上执行本文中的步骤；必须使用启用了虚拟化功能的物理计算机。
>

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>创建 Docker 上的 Spring Boot 入门 Web 应用

以下步骤将引导你完成以下步骤：创建一个简单的 Spring Boot Web 应用程序并对其进行本地测试。

1. 打开命令提示符，创建本地目录以存放应用程序，并更改为以下目录；例如：
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   - 或 -
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. 将 [Docker 上的 Spring Boot 入门]示例项目克隆到创建的目录中；例如：
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. 将目录更改为已完成项目；例如：
   ```
   cd gs-spring-boot-docker/complete
   ```

1. 使用 Maven 生成 JAR 文件；例如：
   ```
   mvn package
   ```

1. 创建 Web 应用后，将目录更改为 JAR 文件所在的 `target` 目录并启动 Web 应用；例如：
   ```
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar
   ```

1. 使用 Web 浏览器在本地浏览到 Web 应用并对其进行测试。 例如，如果你有可用的 curl，并且已将 Tomcat 服务器配置为在端口 80 上运行：
   ```
   curl http://localhost
   ```

1. 应该会显示以下消息：Hello Docker World!

   ![本地浏览示例应用][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>创建 Azure 容器注册表以用作专用 Docker 注册表

以下步骤将引导你完成使用 Azure 门户来创建 Azure 容器注册表的步骤。

> [!NOTE]
>
> 如果你想要使用 Azure CLI（而不是 Azure 门户），请按照[使用 Azure CLI 2.0 创建专用 Docker 容器注册表](../../container-registry/container-registry-get-started-azure-cli.md)中的步骤操作。
>

1. 浏览到 [Azure 门户]并登录。

   登录到你在 Azure 门户的帐户后，可以按照[使用 Azure 门户创建专用 Docker 容器注册表]一文中的步骤操作，为方便起见，在以下步骤中进行了解释。

1. 单击“+ 新建”菜单图标，然后依次单击“容器”、“Azure 容器注册表”。
   
   ![创建新的 Azure 容器注册表][AR01]

1. 显示 Azure 容器注册表模板的信息页面时，请单击“创建”。 

   ![创建新的 Azure 容器注册表][AR02]

1. 当显示“创建容器注册表”页时，输入你的“注册表名称”和“资源组”，为“管理员用户”选择“启用”，然后单击“创建”。

   ![配置 Azure 容器注册表设置][AR03]

1. 创建容器注册表后，在 Azure 门户中导航到你的容器注册表，然后单击“访问密钥”。 记下用户名和密码，以供后续步骤中使用。

   ![Azure 容器注册表访问密钥][AR04]

## <a name="configure-maven-to-use-your-azure-container-registry-access-keys"></a>配置 Maven 以使用你的 Azure 容器注册表访问密钥

1. 导航到 Maven 安装的配置目录，并使用文本编辑器打开 settings.xml 文件。

1. 将本教程上一部分中的 Azure 容器注册表访问设置添加到 settings.xml 文件中的 `<servers>` 集合；例如：

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

1. 导航到 Spring Boot 应用程序的完整项目目录（例如，“C:\SpringBoot\gs-spring-boot-docker\complete”或“/users/robert/SpringBoot/gs-spring-boot-docker/complete”），并使用文本编辑器打开 pom.xml 文件。

1. 使用本教程上一部分中的 Azure 容器注册表的登录服务器值更新 pom.xml 文件中的 `<properties>` 集合，例如：

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. 更新 pom.xml 文件中的 `<plugins>` 集合，使 `<plugin>` 包含本教程上一部分中 Azure 容器注册表的登录服务器地址和注册表名称。 例如：

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
         <serverId>wingtiptoysregistry</serverId>
         <registryUrl>https://wingtiptoysregistry.azurecr.io</registryUrl>
      </configuration>
   </plugin>
   ```

1. 导航到 Spring Boot 应用程序的已完成项目目录，然后运行以下命令以重新生成应用程序，并将容器推送到 Azure 容器注册表：

   ```
   mvn package docker:build -DpushImage 
   ```

> [!NOTE]
>
> 将 Docker 容器推送到 Azure 时，即使已成功创建 Docker 容器，也可能会收到类似于以下内容的错误消息：
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> 如果发生这种情况，可能需要从 Docker 命令行登录到 Azure 帐户，例如：
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> 然后可以从命令行推送容器；例如：
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-using-your-container-image"></a>在 Azure App Service 中使用容器映像创建 Linux 上的 Web 应用

1. 浏览到 [Azure 门户]并登录。

1. 单击“+ 新建”菜单图标，然后依次单击“Web + 移动”、“Linux 上的 Web 应用”。
   
   ![在 Azure 门户中创建新的 Web 应用][LX01]

1. 当显示“Linux 上的 Web 应用”页时，输入以下信息：

   a. 为“应用名称”输入唯一名称；例如：“wingtiptoyslinux”。

   b. 从下拉列表中选择一个订阅。

   c. 选择现有资源组，或指定名称以创建新资源组。

   d. 单击“配置容器”边栏选项卡，然后输入以下信息：

      * 选择“专用注册表”。

      * “映像和可选标记”：根据上文指定容器名称；例如：“wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest”

      * “服务器 URL”：根据上文指定注册表 URL；例如：“https://wingtiptoysregistry.azurecr.io”

      * “登录用户名”和“密码”：根据先前步骤中使用的“访问密钥”指定登录凭据。
   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 输入上述所有信息后，请单击“确定”。

   ![配置 Web 应用设置][LX02]

1. 单击“创建” 。

> [!NOTE]
>
> Azure 会将 Internet 请求自动映射到在标准端口 80 或 8080 上运行的嵌入式 Tomcat 服务器。 但是，如果已将嵌入式 Tomcat 服务器配置为在自定义端口上运行，则需要将一个环境变量添加到为嵌入式 Tomcat 服务器定义端口的 Web 应用。 为此，请按照以下步骤操作：
>
> 1. 浏览到 [Azure 门户]并登录。
> 
> 2. 单击“应用程序服务”图标。 （请参阅下图中的第 1 项。）
>
> 3. 从列表中选择你的 Web 应用。 （请参阅下图中的第 2 项。）
>
> 4. 单击“应用程序设置”。 （请参阅下图中的第 3 项。）
>
> 5. 在“应用设置”部分中，添加一个名为 **PORT** 的新环境变量，并为该值输入自定义端口号。 （请参阅下图中的第 4 项。）
>
> 6. 单击“保存” 。 （请参阅下图中的第 5 项。）
>
> ![在 Azure 门户中保存自定义端口号][LX03]
>

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

有关使用 Azure 上的 Spring Boot 应用程序的详细信息，请参阅以下文章：

* [将 Spring Boot 应用程序部署到 Azure App Service](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)
* [在 Azure 容器服务中将 Spring Boot 应用程序部署于 Kubernetes 群集上](container-service-deploy-spring-boot-app-on-kubernetes.md)

有关将 Azure 与 Java 配合使用的详细信息，请参阅 [Azure Java 开发人员中心]和[用于 Visual Studio Team Services 的 Java 工具]。

有关 Docker 上的 Spring Boot 示例项目的详细信息，请参阅 [Docker 上的 Spring Boot 入门]。

若要获取 Spring Boot 应用程序入门的相关帮助，请参阅 https://start.spring.io/ 中的“Spring Initializr”。

有关创建简单 Spring Boot 应用程序入门的详细信息，请参阅 https://start.spring.io/ 中的 Spring Initializr。

有关如何使用 Azure 的自定义 Docker 映像的其他示例，请参阅[使用 Linux 上 Azure Web 应用的自定义 Docker 映像]。

<!-- URL List -->

[Azure 命令行接口 (CLI)]: /cli/azure/overview
[Azure 容器服务 (ACS)]: https://azure.microsoft.com/services/container-service/
[Azure Java 开发人员中心]: https://azure.microsoft.com/develop/java/
[Azure 门户]: https://portal.azure.com/
[使用 Azure 门户创建专用 Docker 容器注册表]: /azure/container-registry/container-registry-get-started-portal
[使用 Linux 上 Azure Web 应用的自定义 Docker 映像]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[免费 Azure 帐户]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java 开发人员工具包 (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[用于 Visual Studio Team Services 的 Java 工具]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN 订阅者权益]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Docker 上的 Spring Boot 入门]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-on-linux/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-on-linux/SB02.png

[AR01]: ./media/container-service-deploy-spring-boot-app-on-linux/AR01.png
[AR02]: ./media/container-service-deploy-spring-boot-app-on-linux/AR02.png
[AR03]: ./media/container-service-deploy-spring-boot-app-on-linux/AR03.png
[AR04]: ./media/container-service-deploy-spring-boot-app-on-linux/AR04.png

[LX01]: ./media/container-service-deploy-spring-boot-app-on-linux/LX01.png
[LX02]: ./media/container-service-deploy-spring-boot-app-on-linux/LX02.png
[LX03]: ./media/container-service-deploy-spring-boot-app-on-linux/LX03.png

