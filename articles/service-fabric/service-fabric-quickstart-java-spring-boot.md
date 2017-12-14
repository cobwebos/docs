---
title: "将 Spring Boot 应用程序部署到 Azure Service Fabric | Microsoft Docs"
description: "使用 Spring Boot 入门教程为 Azure Service Fabric 部署 Spring Boot 应用程序。"
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: d34862d96744e038d7c1890f703ead79c416ddfa
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-a-spring-boot-application"></a>部署 Spring Boot 应用程序
Azure Service Fabric 是一款分布式系统平台，可用于部署和管理微服务和容器。 

本快速入门演示如何将 Spring Boot 应用程序部署到 Service Fabric。 本快速入门使用 Spring 网站中的[入门](https://spring.io/guides/gs/spring-boot/)示例。 本快速入门逐步讲解如何使用熟悉的命令行工具，将 Spring Boot 示例部署为 Service Fabric 应用程序。 完成后，Spring Boot 入门示例将在 Service Fabric 上正常运行。 

![应用程序屏幕截图](./media/service-fabric-quickstart-java-spring-boot/springbootsf.png)

此快速入门介绍如何：

> [!div class="checklist"]
> * 将 Spring Boot 应用程序部署到 Service Fabric
> * 将应用程序部署到本地群集 
> * 将应用程序部署到 Azure 中的群集
> * 跨多个节点横向扩展应用程序
> * 在不影响可用性的情况下执行服务故障转移

## <a name="prerequisites"></a>先决条件
完成本快速入门教程：
1. [安装 Service Fabric SDK 和 Service Fabric 命令行接口 (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [安装 Git](https://git-scm.com/)
3. [安装 Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
4. [设置 Java 环境](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>下载示例
在命令窗口中运行以下命令，将 Spring Boot 入门示例应用克隆到本地计算机。
```
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="package-the-spring-boot-application"></a>打包 Spring Boot 应用程序 
1. 在刚刚克隆的 `gs-spring-boot` 目录中运行 `yo azuresfguest` 命令。 

2. 每次出现提示时，请输入以下详细信息。 

    ![Yeoman 输入内容](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. 在 `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code` 文件夹中，创建名为 `entryPoint.sh` 的文件。 将以下内容添加到该文件中。 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

现已创建 Spring Boot 入门示例的 Service Fabric 应用程序，可将它部署到 Service Fabric。

## <a name="run-the-application-locally"></a>在本地运行应用程序
1. 通过运行以下命令来启动本地群集：

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    启动本地群集需要一些时间。 若要确认群集是否完全正常，请访问 Service Fabric Explorer（网址：http://localhost:19080）。 5 个节点均正常即表示本地群集运行正常。 
    
    ![本地群集正常运行](./media/service-fabric-quickstart-java/localclusterup.png)

2. 导航到 `gs-spring-boot/SpringServiceFabric` 文件夹。
3. 运行以下命令连接到本地群集。 

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. 运行 `install.sh` 脚本。 

    ```bash
    ./install.sh
    ```

5. 打开喜欢的 Web 浏览器并访问应用程序（网址：http://localhost:8080）。 

    ![本地应用程序前端](./media/service-fabric-quickstart-java-spring-boot/springbootsf.png)
    
现在可以访问已部署到 Service Fabric 群集的 Spring Boot 应用程序。  

## <a name="deploy-the-application-to-azure"></a>将应用程序部署到 Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>设置 Azure Service Fabric 群集
若要将应用程序部署到 Azure 中的群集，可创建自己的群集。

合作群集是 Azure 上托管的免费限时 Service Fabric 群集。 这些群集由 Service Fabric 团队运行，任何人均可在其中部署应用程序和了解平台。 若要使用合作群集，请[按照说明操作](http://aka.ms/tryservicefabric)。 

若要了解如何创建自己的群集，请参阅[在 Azure 上创建 Service Fabric 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)。

> [!Note]
> Spring Boot 服务配置为侦听端口 8080 上的传入流量。 请确保此端口在群集中处于打开状态。 如果使用的是合作群集，此端口已处于打开状态。
>

### <a name="deploy-the-application-using-cli"></a>使用 CLI 部署应用程序
应用程序和群集准备就绪后，可通过命令行将应用程序直接部署到群集。

1. 导航到 `gs-spring-boot/SpringServiceFabric` 文件夹。
2. 运行以下命令连接到 Azure 群集。 

    ```bash
    sfctl cluster select --endpoint http://<ConnectionIPOrURL>:19080
    ```
    
    如果群集受自签名证书的保护，请运行以下命令： 

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. 运行 `install.sh` 脚本。 

    ```bash
    ./install.sh
    ```

4. 打开偏好的 Web 浏览器并通过 **http://\<ConnectionIPOrUrl>:8080** 访问该应用程序。 

    ![本地应用程序前端](./media/service-fabric-quickstart-java-spring-boot/springsfazure.png)
    
现在可以访问已部署到 Service Fabric 群集的 Spring Boot 应用程序。  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>在群集中缩放应用程序和服务
可跨群集缩放服务来适应服务负载的变化。 可以通过更改群集中运行的实例数量来缩放服务。 存在多种服务缩放方式，可使用 Service Fabric CLI (sfctl) 脚本/命令。 在此示例中，我们使用的是 Service Fabric Explorer。

Service Fabric Explorer 在所有 Service Fabric 群集中运行，并能通过浏览器进行访问，访问方法是转到群集 HTTP 管理端口 19080（例如，`http://demolinuxsecure.westus.cloudapp.azure.com:19080`）。

若要缩放 Web 前端服务，请按照以下步骤操作：

1. 在群集中打开 Service Fabric Explorer - 例如 `http://demolinuxsecure.westus.cloudapp.azure.com:19080`。
2. 在树视图中单击“fabric:/SpringServiceFabric/SpringGettingStarted”节点旁边的省略号（三个点），选择“缩放服务”。

    ![Service Fabric Explorer 缩放服务](./media/service-fabric-quickstart-java-spring-boot/springbootsfhowtoscale.png)

    现在可以选择缩放服务的实例数量。

3. 将数字更改为 **5**，单击“缩放服务”。

    下面显示了使用命令行缩放服务的另一种方法。

    ```bash 
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 5 --stateless 
    ``` 

4. 在树视图中单击“fabric:/SpringServiceFabric/SpringGettingStarted”节点，展开分区节点（由 GUID 表示）。

    ![Service Fabric Explorer 缩放服务完成](./media/service-fabric-quickstart-java-spring-boot/springsfscaled.png)

    现在可以看到，服务有五个实例。在树视图中可以查看运行实例的节点。

通过这一简单的管理任务，我们增加了前端服务用来处理用户负载的资源数量。 有必要了解的是，服务无需有多个实例，便能可靠运行。 如果服务出现故障，Service Fabric 可确保在群集中运行新的服务实例。

## <a name="failover-services-in-a-cluster"></a>故障转移群集中的服务 
为了演示服务故障转移，我们可以使用 Service Fabric Explorer 来模拟节点重启。 请确保只有 1 个服务实例在运行。 

1. 在群集中打开 Service Fabric Explorer - 例如 `http://demolinuxsecure.westus.cloudapp.azure.com:19080`。
2. 单击运行服务实例的节点旁边的省略号（三个点），并重启节点。 

    ![Service Fabric Explorer - 重启节点](./media/service-fabric-quickstart-java-spring-boot/springbootsfrestart.png)
3. 服务实例现已转移到另一个节点，且应用程序并未关闭。 

    ![Service Fabric Explorer - 重启节点成功](./media/service-fabric-quickstart-java-spring-boot/springbootsfrestartsucceed.png)

## <a name="next-steps"></a>后续步骤
在此快速入门中，读者学习了如何：

> [!div class="checklist"]
> * 将 Spring Boot 应用程序部署到 Service Fabric
> * 将应用程序部署到本地群集 
> * 将应用程序部署到 Azure 中的群集
> * 跨多个节点横向扩展应用程序
> * 在不影响可用性的情况下执行服务故障转移

* 详细了解如何[使用 Service Fabric 编程模型生成 Java 微服务](service-fabric-quickstart-java-reliable-services.md)
* 了解如何[使用 Jenkins 设置连续集成和部署](service-fabric-cicd-your-linux-applications-with-jenkins.md)
* 查看其他 [Java 示例](https://github.com/Azure-Samples/service-fabric-java-getting-started)