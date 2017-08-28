---
title: "在 Azure 容器服务中将 Spring Boot 应用部署于 Kubernetes | Microsoft Docs"
description: "本教程将指导用户完成在 Microsoft Azure 的 Kubernetes 群集中部署 Spring Boot 应用程序的步骤。"
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
ms.openlocfilehash: 7f726436b2d459b8c16abb02e07de099abfd8974
ms.contentlocale: zh-cn
ms.lasthandoff: 08/12/2017

---

# <a name="deploy-a-spring-boot-application-on-a-kubernetes-cluster-in-the-azure-container-service"></a>在 Azure 容器服务中将 Spring Boot 应用程序部署于 Kubernetes 群集上

[Spring Framework] 是一种常用的开源框架，可帮助 Java 开发人员创建 Web、移动和 API 应用程序。 本教程使用通过 [Spring Boot] 创建的示例应用，其为使用 Spring 进行快速入门的惯例方法。

[Kubernetes] 和 [Docker] 是开源解决方案，可帮助开发人员自动部署、扩展和管理在容器中运行的应用程序。

本教程将指导用户将这两种常用的开源技术进行结合，从而将 Spring Boot 应用程序开发和部署到 Microsoft Azure。 具体而言，将使用 [Spring Boot]进行应用程序开发，使用 [Kubernetes]进行容器部署，然后使用 [Azure 容器服务 (ACS)] 托管你的应用程序。

### <a name="prerequisites"></a>先决条件

* Azure 订阅；若尚未拥有 Azure 订阅，可激活 [MSDN 订阅者权益]或注册获取[免费 Azure 帐户]。
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

以下步骤将指导用户构建 Spring Boot web 应用程序并在本地进行测试。

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

1. 将 [Docker 上的 Spring Boot 启动入门]示例项目克隆到目录。
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. 将目录更改为已完成项目。
   ```
   cd gs-spring-boot-docker
   cd complete
   ```

1. 使用 Maven 生成和运行示例应用。
   ```
   mvn package spring-boot:run
   ```

1. 通过浏览到 http://localhost:8080 或使用以下 `curl` 命令测试 Web 应用：
   ```
   curl http://localhost:8080
   ```

1. 应会显示以下消息：“Hello Docker World”

   ![本地浏览示例应用][SB01]

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>使用 Azure CLI 创建 Azure 容器注册表

1. 打开命令提示符。

1. 登录到 Azure 帐户：
   ```azurecli
   az login
   ```

1. 为本教程中使用的 Azure 资源创建资源组。
   ```azurecli
   az group create --name=wingtiptoys-kubernetes --location=eastus
   ```

1. 在资源组中创建私有 Azure 容器注册表。 本教程的后续步骤会将示例应用作为 Docker 映像推送到此注册表。 用注册表的唯一名称替换 `wingtiptoysregistry`。
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoys-kubernetes--location eastus \
    --name wingtiptoysregistry --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry"></a>将应用推送到容器注册表

1. 导航到 Maven 安装的配置目录 (default ~/.m2/ or C:\Users\username\.m2)，并使用文本编辑器打开 settings.xml 文件。

1. 从 Azure CLI 检索容器注册表的密码。
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```

   ```json
   {
  "name": "password",
  "value": "AbCdEfGhIjKlMnOpQrStUvWxYz"
   }
   ```

1. 将你的 Azure 容器注册表 ID 和密码添加到 settings.xml 文件的 `<server>` 集合。
`id` 和 `username` 是注册表的名称。 使用上一个命令中的 `password` 值（不带引号）。

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

1. 使用 Azure 容器注册表的登录服务器值更新 pom.xml 文件中的 `<properties>` 集合。

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. 更新 pom.xml 文件中的 `<plugins>` 集合，使 `<plugin>` 包含 Azure 容器注册表的登录服务器地址和注册表名称。

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

1. 导航到 Spring Boot 应用程序的完成项目目录，然后运行以下命令以生成 Docker 容器并将映像推送到注册表：

   ```
   mvn package docker:build -DpushImage
   ```

> [!NOTE]
>
>  Maven 将映像推送到 Azure 时，你可能会收到类似于以下内容的错误消息：
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> 如果收到此错误消息，请从 Docker 命令行登录到 Azure。
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> 然后推送你的容器：
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`

## <a name="create-a-kubernetes-cluster-on-acs-using-the-azure-cli"></a>使用 Azure CLI 在 ACS 上创建 Kubernetes 群集

1. 在 Azure 容器服务中创建 Kubernetes 群集。 以下命令在 wingtiptoys-kubernetes 资源组中创建 kubernetes 群集，将 wingtiptoys-containerservice 作为群集名称，wingtiptoys-kubernetes 作为 DNS 前缀：
   ```azurecli
   az acs create --orchestrator-type=kubernetes --resource-group=wingtiptoys-kubernetes \ 
    --name=wingtiptoys-containerservice --dns-prefix=wingtiptoys-kubernetes
   ```
   此命令可能需要一段时间才能完成。

1. 使用 Azure CLI 安装 `kubectl`。 Linux 用户可能必须将 `sudo` 作为此命令的前缀，因为它将 Kubernetes CLI 部署到 `/usr/local/bin`。
   ```azurecli
   az acs kubernetes install-cli
   ```

1. 下载群集配置信息，以便从 Kubernetes Web 界面和 `kubectl` 管理群集。 
   ```azurecli
   az acs kubernetes get-credentials --resource-group=wingtiptoys-kubernetes  \ 
    --name=wingtiptoys-containerservice
   ```

## <a name="deploy-the-image-to-your-kubernetes-cluster"></a>将映像部署到 Kubernetes 群集

本教程使用 `kubectl` 部署应用，以便让你通过 Kubernetes Web 界面浏览部署。

### <a name="deploy-with-the-kubernetes-web-interface"></a>使用 Kubernetes Web 界面进行部署

1. 打开命令提示符。

1. 在默认浏览器中打开 Kubernetes 群集的配置网站：
   ```
   az acs kubernetes browse --resource-group=wingtiptoys-kubernetes --name=wingtiptoys-containerservice
   ```

1. 在浏览器中打开 Kubernetes 配置网站后，单击“部署容器化应用”的链接：

   ![Kubernetes 配置网站][KB01]

1. 显示“部署容器化应用”页面后，请指定以下选项：

   a. 选择“指定以下应用详细信息”。

   b. 在“应用名称”中输入 Spring Boot 应用程序名称；例如：gs-spring-boot-docker。

   c. 在容器映像中输入之前的登录服务器和容器映像；例如：“wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest”。

   d. 选择“外部”服务。

   e. 在“端口”和“目标端口”文本框中指定外部和内部端口。

   ![Kubernetes 配置网站][KB02]


1. 单击“部署”对容器进行部署。

   ![部署容器][KB05]

1. 部署应用程序后，Spring Boot 应用程序将列在“服务”下方。

   ![Kubernetes 服务][KB06]

1. 若单击“外部终结点”的链接，则可看见 Spring Boot 应用程序在 Azure 上运行。

   ![Kubernetes 服务][KB07]

   ![在 Azure 上浏览示例应用][SB02]


### <a name="deploy-with-kubectl"></a>使用 kubectl 进行部署

1. 打开命令提示符。

1. 使用 `kubectl run` 命令在 Kubernetes 群集中运行容器。 指定 Kubernetes 中应用的服务名称和完整映像名称。 例如：
   ```
   kubectl run gs-spring-boot-docker --image=wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest
   ```
   在此命令中：

   * 容器名称 `gs-spring-boot-docker` 会立即在 `run` 命令后指定

   * `--image` 参数将组合的登录服务器和映像名称指定为 `wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest`

1. 使用 `kubectl expose` 命令外部公开你的 Kubernetes 群集。 指定你的服务名称、用于访问应用的公开 TCP 端口以及应用侦听的内部目标端口。 例如：
   ```
   kubectl expose deployment gs-spring-boot-docker --type=LoadBalancer --port=80 --target-port=8080
   ```
   在此命令中：

   * 容器名称 `gs-spring-boot-docker` 会立即在 `expose deployment` 命令后指定

   * `--type` 参数指定此群集使用负载均衡器

   * `--port` 参数指定面向公众的 TCP 端口 80。 在此端口访问应用。

   * `--target-port` 参数指定内部 TCP 端口 8080。 负载均衡器在此端口将请求转发到你的应用。

1. 将应用部署到群集后，请查询外部 IP 地址并在 Web 浏览器中打开：

   ```
   kubectl get services -o jsonpath={.items[*].status.loadBalancer.ingress[0].ip} --namespace=${namespace}
   ```

   ![在 Azure 上浏览示例应用][SB02]


## <a name="next-steps"></a>后续步骤

有关使用 Azure 上的 Spring Boot 的详细信息，请参阅以下文章：

* [将 Spring Boot 应用程序部署到 Azure App Service](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)
* [在 Azure 容器服务中将 Spring Boot 应用程序部署于 Linux 上](container-service-deploy-spring-boot-app-on-linux.md)

有关将 Azure 与 Java 配合使用的详细信息，请参阅 [Azure Java 开发人员中心]和[用于 Visual Studio Team Services 的 Java 工具]。

有关 Docker 上的 Spring Boot 示例项目的详细信息，请参阅[Docker 上的 Spring Boot 启动入门]。

以下链接提供了创建 Spring Boot 应用程序的详细信息：

* 有关创建简单 Spring Boot 应用程序的详细信息，请参阅 Spring Initializr at https://start.spring.io/。

以下链接提供了将 Kubernetes 与 Azure 配合使用的详细信息：

* [容器服务中的 Kubernetes 群集入门](https://docs.microsoft.com/azure/container-service/container-service-kubernetes-walkthrough)
* [在 Azure 容器服务中使用 Kubernetes Web UI](https://docs.microsoft.com/azure/container-service/container-service-kubernetes-ui)

有关使用 Kubernetes 命令行接口的详细信息，请在 < https://kubernetes.io/docs/user-guide/kubectl/ > 处参阅 **kubectl** 用户指南。

Kubernetes 网站中有多篇文章讨论有关在私有注册表中使用映像的信息：

* [为 Pod 配置服务帐户]
* [命名空间]
* [从私有注册表拉取映像]

有关如何使用 Azure 的自定义 Docker 映像的其他示例，请参阅[使用 Linux 上 Azure Web 应用的自定义 Docker 映像]。

<!-- URL List -->

[Azure 命令行接口 (CLI)]: /cli/azure/overview
[Azure 容器服务 (ACS)]: https://azure.microsoft.com/services/container-service/
[Azure Java 开发人员中心]: https://azure.microsoft.com/develop/java/
[Azure portal]: https://portal.azure.com/
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[使用 Linux 上 Azure Web 应用的自定义 Docker 映像]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[免费 Azure 帐户]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java 开发人员工具包 (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[用于 Visual Studio Team Services 的 Java 工具]: https://java.visualstudio.com/
[Kubernetes]: https://kubernetes.io/
[Kubernetes Command-Line Interface (kubectl)]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[Maven]: http://maven.apache.org/
[MSDN 订阅者权益]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Docker 上的 Spring Boot 启动入门]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/
[为 Pod 配置服务帐户]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/
[命名空间]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[从私有注册表拉取映像]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/SB02.png

[AR01]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR01.png
[AR02]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR02.png
[AR03]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR03.png
[AR04]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/AR04.png

[KB01]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB01.png
[KB02]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB02.png
[KB03]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB03.png
[KB04]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB04.png
[KB05]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB05.png
[KB06]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB06.png
[KB07]: ./media/container-service-deploy-spring-boot-app-on-kubernetes/KB07.png

