---
title: "使用 Fabric8 Maven 插件部署 Spring Boot 应用"
description: "本教程介绍使用适用于 Apache Maven 的 Fabric8 插件在 Microsoft Azure 中部署 Spring Boot 应用程序的步骤。"
services: container-service
documentationcenter: java
author: rmcmurray
manager: routlaw
editor: 
ms.assetid: 
ms.service: container-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 09/15/2017
ms.author: yuwzho;robmcm
ms.custom: 
ms.openlocfilehash: 6ec48caef0eebeacf260b0dbc91b175368faea69
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2017
---
# <a name="deploy-a-spring-boot-app-using-the-fabric8-maven-plugin"></a>使用 Fabric8 Maven 插件部署 Spring Boot 应用

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

**[Fabric8]** 是一种基于 **[Kubernetes]** 构建的开源解决方案，可帮助开发人员在 Linux 容器中创建应用程序。

本教程介绍如何使用适用于 Maven 的 Fabric8 插件在 [Azure 容器服务 (ACS)] 中开发应用程序并将其部署到 Linux 主机。

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

以下步骤将指导用户构建 Spring Boot web 应用程序并在本地进行测试。

1. 打开命令提示符，创建本地目录以存放应用程序，并更改为以下目录；例如：
   ```shell
   md /home/GenaSoto/SpringBoot
   cd /home/GenaSoto/SpringBoot
   ```
   - 或 -
   ```shell
   md C:\SpringBoot
   cd C:\SpringBoot
   ```

1. 将 [Docker 上的 Spring Boot 启动入门]示例项目克隆到目录。
   ```shell
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. 将目录更改为已完成项目；例如：
   ```shell
   cd gs-spring-boot-docker/complete
   ```
   - 或 -
   ```shell
   cd gs-spring-boot-docker\complete
   ```

1. 使用 Maven 生成和运行示例应用。
   ```shell
   mvn clean package spring-boot:run
   ```

1. 通过浏览到 http://localhost:8080 或使用以下 `curl` 命令测试 Web 应用：
   ```shell
   curl http://localhost:8080
   ```

   应该会显示“Hello Docker World”消息。

   ![在本地浏览示例应用程序][SB01]


## <a name="install-the-kubernetes-command-line-interface-and-create-an-azure-resource-group-using-the-azure-cli"></a>安装 Kubernetes 命令行接口并使用 Azure CLI 创建 Azure 资源组

1. 打开命令提示符。

1. 键入以下命令登录到 Azure 帐户：
   ```azurecli
   az login
   ```
   按照说明完成登录过程
   
   Azure CLI 将显示帐户列表；例如：

   ```json
   [
     {
       "cloudName": "AzureCloud",
       "id": "00000000-0000-0000-0000-000000000000",
       "isDefault": false,
       "name": "Windows Azure MSDN - Visual Studio Ultimate",
       "state": "Enabled",
       "tenantId": "00000000-0000-0000-0000-000000000000",
       "user": {
         "name": "Gena.Soto@wingtiptoys.com",
         "type": "user"
       }
     }
   ]
   ```

1. 如果尚未安装 Kubernetes 命令行接口 (`kubectl`)，可以使用 Azure CLI 安装；例如：
   ```azurecli
   az acs kubernetes install-cli
   ```

   > [!NOTE]
   >
   > Linux 用户可能必须将 `sudo` 作为此命令的前缀，因为它将 Kubernetes CLI 部署到 `/usr/local/bin`。
   >
   > 如果已经安装 `kubectl`，但 `kubectl` 的版本太旧，那么在尝试完成本文后面列出的步骤时，可能会显示类似于以下示例的错误消息：
   >
   > ```
   > error: group map[autoscaling:0x0000000000 batch:0x0000000000 certificates.k8s.io
   > :0x0000000000 extensions:0x0000000000 storage.k8s.io:0x0000000000 apps:0x0000000
   > 000 authentication.k8s.io:0x0000000000 policy:0x0000000000 rbac.authorization.k8
   > s.io:0x0000000000 federation:0x0000000000 authorization.k8s.io:0x0000000000 comp
   > onentconfig:0x0000000000] is already registered
   > ```
   >
   > 如果发生这种情况，则需重新安装 `kubectl` 以更新版本。
   >

1. 为本教程中要使用的 Azure 资源创建资源组；例如：
   ```azurecli
   az group create --name=wingtiptoys-kubernetes --location=westeurope
   ```
   其中：  
      * *wingtiptoys-kubernetes* 是资源组的唯一名称  
      * *westeurope* 是应用程序的相应地理位置  

   Azure CLI 将显示资源组创建的结果；例如：  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes",
     "location": "westeurope",
     "managedBy": null,
     "name": "wingtiptoys-kubernetes",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "tags": null
   }
   ```


## <a name="create-a-kubernetes-cluster-using-the-azure-cli"></a>使用 Azure CLI 创建 Kubernetes 群集

1. 在新资源组中创建 Kubernetes 群集；例如：  
   ```azurecli 
   az acs create --orchestrator-type kubernetes --resource-group wingtiptoys-kubernetes --name wingtiptoys-cluster --generate-ssh-keys --dns-prefix=wingtiptoys
   ```
   其中：  
      * *wingtiptoys-kubernetes* 是本文前面的资源组的名称  
      * *wingtiptoys-cluster* 是 Kubernetes 群集的唯一名称
      * *wingtiptoys* 是应用程序的唯一名称 DNS 名称

   Azure CLI 将显示群集创建的结果；例如：  

   ```json
   {
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.Resources/deployments/azurecli0000000000.00000000000",
     "name": "azurecli0000000000.00000000000",
     "properties": {
       "correlationId": "00000000-0000-0000-0000-000000000000",
       "debugSetting": null,
       "dependencies": [],
       "mode": "Incremental",
       "outputs": {
         "masterFQDN": {
           "type": "String",
           "value": "wingtiptoysmgmt.westeurope.cloudapp.azure.com"
         },
         "sshMaster0": {
           "type": "String",
           "value": "ssh azureuser@wingtiptoysmgmt.westeurope.cloudapp.azure.com -A -p 22"
         }
       },
       "parameters": {
         "clientSecret": {
           "type": "SecureString"
         }
       },
       "parametersLink": null,
       "providers": [
         {
           "id": null,
           "namespace": "Microsoft.ContainerService",
           "registrationState": null,
           "resourceTypes": [
             {
               "aliases": null,
               "apiVersions": null,
               "locations": [
                 "westeurope"
               ],
               "properties": null,
               "resourceType": "containerServices"
             }
           ]
         }
       ],
       "provisioningState": "Succeeded",
       "template": null,
       "templateLink": null,
       "timestamp": "2017-09-15T01:00:00.000000+00:00"
     },
     "resourceGroup": "wingtiptoys-kubernetes"
   }
   ```

1. 下载新 Kubernetes 群集的凭据；例如：  
   ```azurecli 
   az acs kubernetes get-credentials --resource-group=wingtiptoys-kubernetes --name wingtiptoys-cluster
   ```

1. 使用以下命令验证连接：
   ```shell 
   kubectl get nodes
   ```

   应显示节点和状态列表，如以下示例所示：

   ```shell
   NAME                    STATUS                     AGE       VERSION
   k8s-agent-00000000-0    Ready                      5h        v1.6.6
   k8s-agent-00000000-1    Ready                      5h        v1.6.6
   k8s-agent-00000000-2    Ready                      5h        v1.6.6
   k8s-master-00000000-0   Ready,SchedulingDisabled   5h        v1.6.6
   ```


## <a name="create-a-private-azure-container-registry-using-the-azure-cli"></a>使用 Azure CLI 创建专用 Azure 容器注册表

1. 在资源组中创建专用 Azure 容器注册表来承载 Docker 映像；例如：
   ```azurecli
   az acr create --admin-enabled --resource-group wingtiptoys-kubernetes --location westeurope --name wingtiptoysregistry --sku Basic
   ```
   其中：  
      * *wingtiptoys-kubernetes* 是本文前面的资源组的名称  
      * *wingtiptoysregistry* 是专用注册表的唯一名称
      * *westeurope* 是应用程序的相应地理位置  

   Azure CLI 将显示注册表创建的结果；例如：  

   ```json
   {
     "adminUserEnabled": true,
     "creationDate": "2017-09-15T01:00:00.000000+00:00",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/wingtiptoys-kubernetes/providers/Microsoft.ContainerRegistry/registries/wingtiptoysregistry",
     "location": "westeurope",
     "loginServer": "wingtiptoysregistry.azurecr.io",
     "name": "wingtiptoysregistry",
     "provisioningState": "Succeeded",
     "resourceGroup": "wingtiptoys-kubernetes",
     "sku": {
       "name": "Basic",
       "tier": "Basic"
     },
     "storageAccount": {
       "name": "wingtiptoysregistr000000"
     },
     "tags": {},
     "type": "Microsoft.ContainerRegistry/registries"
   }
   ```

1. 从 Azure CLI 检索容器注册表的密码。
   ```azurecli
   az acr credential show --name wingtiptoysregistry --query passwords[0]
   ```

   Azure CLI 将显示注册表密码；例如：  

   ```json
   {
     "name": "password",
     "value": "AbCdEfGhIjKlMnOpQrStUvWxYz"
   }
   ```

1. 导航到 Maven 安装的配置目录 (default ~/.m2/ or C:\Users\username\.m2)，并使用文本编辑器打开 settings.xml 文件。

1. 将 Azure 容器注册表 URL、用户名和密码添加到 *settings.xml* 文件中新的 `<server>` 集合。
`id` 和 `username` 是注册表的名称。 使用上一个命令中的 `password` 值（不带引号）。

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry.azurecr.io</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

1. 导航到 Spring Boot 应用程序的已完成项目目录（例如，“*C:\SpringBoot\gs-spring-boot-docker\complete*”或“*/home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete*”），并使用文本编辑器打开 *pom.xml* 文件。

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
     <artifactId>dockerfile-maven-plugin</artifactId>
     <version>1.3.4</version>
     <configuration>
       <repository>${docker.image.prefix}/${project.artifactId}</repository>
       <serverId>${docker.image.prefix}</serverId>
       <registryUrl>https://${docker.image.prefix}</registryUrl>
     </configuration>
   </plugin>
   ```

1. 导航到 Spring Boot 应用程序的已完成项目目录，然后运行以下 Maven 命令生成 Docker 容器并将映像推送到注册表：

   ```shell
   mvn package dockerfile:build -DpushImage
   ```

   Maven 将显示生成结果；例如：  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 38.113 s
   [INFO] Finished at: 2017-09-15T02:00:00-07:00
   [INFO] Final Memory: 47M/338M
   [INFO] ----------------------------------------------------
   ```


## <a name="configure-your-spring-boot-app-to-use-the-fabric8-maven-plugin"></a>将 Spring Boot 应用配置为使用 Fabric8 Maven 插件

1. 导航到 Spring Boot 应用程序的已完成项目目录（例如，“*C:\SpringBoot\gs-spring-boot-docker\complete*”或“*/home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete*”），并使用文本编辑器打开 *pom.xml* 文件。

1. 更新 *pom.xml* 文件中的 `<plugins>` 集合以添加 Fabric8 Maven 插件：

   ```xml
   <plugin>
     <groupId>io.fabric8</groupId>
     <artifactId>fabric8-maven-plugin</artifactId>
     <version>3.5.30</version>
     <configuration>
       <ignoreServices>false</ignoreServices>
       <registry>${docker.image.prefix}</registry>
     </configuration>
   </plugin>
   ```

1. 导航到 Spring Boot 应用程序的主要源目录（例如：“*C:\SpringBoot\gs-spring-boot-docker\complete\src\main*”或“*/home/GenaSoto/SpringBoot/gs-spring-boot-docker/complete/src/main*”），并创建一个名为“*fabric8*”的新文件夹。

1. 在新的 *fabric8* 文件夹中创建三个 YAML 片段文件：

   a. 使用以下内容创建名为 **deployment.yml** 的文件：
      ```yaml
      apiVersion: extensions/v1beta1
      kind: Deployment
      metadata:
        name: ${project.artifactId}
        labels:
          run: gs-spring-boot-docker
      spec:
        replicas: 1
        selector:
          matchLabels:
            run: gs-spring-boot-docker
        strategy:
          rollingUpdate:
            maxSurge: 1
            maxUnavailable: 1
          type: RollingUpdate
        template:
          metadata:
            labels:
              run: gs-spring-boot-docker
          spec:
            containers:
            - image: ${docker.image.prefix}/${project.artifactId}:latest
              name: gs-spring-boot-docker
              imagePullPolicy: Always
              ports:
              - containerPort: 8080
            imagePullSecrets:
              - name: mysecrets
      ```

   b. 使用以下内容创建名为 **secrets.yml** 的文件：
      ```yaml
      apiVersion: v1
      kind: Secret
      metadata:
        name: mysecrets
        namespace: default
        annotations:
          maven.fabric8.io/dockerServerId:  ${docker.image.prefix}
      type: kubernetes.io/dockercfg
      ```

   c. 使用以下内容创建名为 **service.yml** 的文件：
      ```yaml
      apiVersion: v1
      kind: Service
      metadata:
        name: ${project.artifactId}
        labels:
          expose: "true"
      spec:
        ports:
        - port: 80
          targetPort: 8080
        type: LoadBalancer
      ```

1. 运行以下 Maven 命令，生成 Kubernetes 资源列表文件：

   ```shell
   mvn fabric8:resource
   ```

   此命令会将 *src/main/fabric8* 文件夹下的所有 Kubernetes 资源 yaml 文件合并到一个包含 Kubernetes 资源列表的 YAML 文件，该文件可以直接应用于 Kubernetes 群集或导出到 Helm 图表。

   Maven 将显示生成结果；例如：  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 16.744 s
   [INFO] Finished at: 2017-09-15T02:35:00-07:00
   [INFO] Final Memory: 30M/290M
   [INFO] ----------------------------------------------------
   ```

1. 运行以下 Maven 命令，将资源列表文件应用于 Kubernetes 群集：

   ```shell
   mvn fabric8:apply
   ```

   Maven 将显示生成结果；例如：  

   ```shell
   [INFO] ----------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ----------------------------------------------------
   [INFO] Total time: 14.814 s
   [INFO] Finished at: 2017-09-15T02:41:00-07:00
   [INFO] Final Memory: 35M/288M
   [INFO] ----------------------------------------------------
   ```

1. 将应用部署到群集后，使用 `kubectl` 应用程序查询外部 IP 地址；例如：
   ```shell
   kubectl get svc -w
   ```

   `kubectl` 将显示内部和外部 IP 地址；例如：
   
   ```shell
   NAME                    CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
   kubernetes              10.0.0.1     <none>        443/TCP        19h
   gs-spring-boot-docker   10.0.242.8   13.65.196.3   80:31215/TCP   3m
   ```
   
   外部 IP 地址可用于在 Web 浏览器中打开应用程序。

   ![从外部浏览示例应用程序][SB02]

## <a name="delete-your-kubernetes-cluster"></a>删除 Kubernetes 群集

不再需要 Kubernetes 群集时，可以使用 `az group delete` 命令删除资源组，这将删除其所有相关资源；例如：

   ```azurecli
   az group delete --name wingtiptoys-kubernetes --yes --no-wait
   ```

## <a name="next-steps"></a>后续步骤

有关使用 Azure 上的 Spring Boot 应用程序的详细信息，请参阅以下文章：

* [将 Spring Boot 应用程序部署到 Azure 应用服务](../../app-service/app-service-deploy-spring-boot-web-app-on-azure.md)
* [在 Azure 容器服务中将 Spring Boot 应用程序部署于 Linux 上](container-service-deploy-spring-boot-app-on-linux.md)
* [在 Azure 容器服务中将 Spring Boot 应用程序部署于 Kubernetes 群集上](container-service-deploy-spring-boot-app-on-kubernetes.md)

有关将 Azure 与 Java 配合使用的详细信息，请参阅 [Azure Java 开发人员中心]和[用于 Visual Studio Team Services 的 Java 工具]。

有关 Docker 上的 Spring Boot 示例项目的详细信息，请参阅 [Docker 上的 Spring Boot 启动入门]。

若要获取 Spring Boot 应用程序入门的相关帮助，请参阅 <https://start.spring.io/> 中的 **Spring Initializr**。

有关创建简单 Spring Boot 应用程序入门的详细信息，请参阅 <https://start.spring.io/> 中的 Spring Initializr。

有关如何使用 Azure 的自定义 Docker 映像的其他示例，请参阅[使用 Linux 上 Azure Web 应用的自定义 Docker 映像]。

<!-- URL List -->

[Azure 命令行接口 (CLI)]: /cli/azure/overview
[Azure 容器服务 (ACS)]: https://azure.microsoft.com/services/container-service/
[Azure Java 开发人员中心]: https://azure.microsoft.com/develop/java/
[Azure portal]: https://portal.azure.com/
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[使用 Linux 上 Azure Web 应用的自定义 Docker 映像]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[Fabric8]: https://fabric8.io/
[免费 Azure 帐户]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java 开发人员工具包 (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[用于 Visual Studio Team Services 的 Java 工具]: https://java.visualstudio.com/
[Kubernetes]: https://kubernetes.io/
[Maven]: http://maven.apache.org/
[MSDN 订阅者权益]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Docker 上的 Spring Boot 启动入门]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-using-fabric8-maven-plugin/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-using-fabric8-maven-plugin/SB02.png
