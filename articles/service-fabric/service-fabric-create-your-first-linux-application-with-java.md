---
title: "在 Linux 上使用 Java 创建第一个 Azure 微服务应用 | Microsoft 文档"
description: "使用 Java 创建和部署 Service Fabric 应用程序"
services: service-fabric
documentationcenter: java
author: seanmck
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 88b16b0b7c951ab0350649de05c00263ec76e630
ms.lasthandoff: 03/11/2017


---
# <a name="create-your-first-azure-service-fabric-application"></a>创建第一个 Azure Service Fabric 应用程序
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java-Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric 提供用于在 Linux 上使用 .NET Core 和 Java 构建服务的 SDK。 本教程使用 Java 创建适用于 Linux 的应用程序并构建服务。  

> [!NOTE]
> 只有 Linux 预览版才支持先进的 Java 内置编程语言（Windows 支持已做好规划）。 但是，任何应用程序（包括 Java 应用程序）都可以在 Windows 或 Linux 上作为来宾可执行文件运行或者在容器中运行。 有关详细信息，请参阅[将现有可执行文件部署到 Azure Service Fabric](service-fabric-deploy-existing-app.md) 和[将容器部署到 Service Fabric](service-fabric-deploy-container.md)。
>

## <a name="video-tutorial"></a>视频教程

以下 Microsoft 虚拟大学视频逐步讲解了在 Linux 上创建 Java 应用的过程：  
<center><a target="\_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-create-your-first-linux-application-with-java/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>


## <a name="prerequisites"></a>先决条件
开始之前，请确保已[设置 Linux 开发环境](service-fabric-get-started-linux.md)。 如果使用的是 Mac OS X，则可以[使用 Vagrant 在虚拟机中设置 Linux 单机环境](service-fabric-get-started-mac.md)。

## <a name="create-the-application"></a>创建应用程序
Service Fabric 应用程序可以包含一个或多个服务，每个服务都在提供应用程序功能时具有特定角色。 用于 Linux 的 Service Fabric SDK 包括 [Yeoman](http://yeoman.io/) 生成器，利用它可以轻松地创建第一个服务和在以后添加其他服务。 让我们使用 Yeoman 来创建具有单项服务的应用程序。

1. 在终端中，键入 ``yo azuresfjava``。
2. 命名应用程序。
3. 选择第一个服务的类型并将其命名。 对于本教程，我们会选择“可靠角色服务”。

   ![适用于 Java 的 Service Fabric Yeoman 生成器][sf-yeoman]

> [!NOTE]
> 有关选项的详细信息，请参阅 [Service Fabric 编程模型概述](service-fabric-choose-framework.md)。
>

## <a name="build-the-application"></a>构建应用程序
Service Fabric Yeoman 模板包含 [Gradle](https://gradle.org/) 的生成脚本，可用于从终端生成应用。

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>部署应用程序
生成应用程序后，可以使用 Azure CLI 将其部署到本地群集。

1. 连接到本地 Service Fabric 群集。

    ```bash
    azure servicefabric cluster connect
    ```

2. 使用模板中提供的安装脚本可将应用程序包复制到群集的映像存储、注册应用程序类型和创建应用程序的实例。

    ```bash
    ./install.sh
    ```

3. 打开浏览器并导航到 http://localhost:19080/Explorer 的 Service Fabric Explorer（如果在 Mac OS X 上使用 Vagrant，则使用 VM 的专用 IP 替换 localhost）。

4. 展开应用程序节点，注意现在有一个条目是用于你的应用程序类型，另一个条目用于该类型的第一个实例。

## <a name="start-the-test-client-and-perform-a-failover"></a>启动测试客户端并执行故障转移
Actor 项目自身未执行任何操作。 它们需要另一个服务或客户端向其发送消息。 Actor 模板包括可用于与 actor 服务进行交互的简单测试脚本。

1. 使用监视实用程序运行该脚本来查看 actor 服务的输出。

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. 在 Service Fabric Explorer 中，找到承载 actor 服务的主副本的节点。 以下屏幕截图中显示的是节点 3。

    ![在 Service Fabric Explorer 中查找主副本][sfx-primary]

3. 单击上一步找到的节点，然后在“操作”菜单中选择“停用(重启)”。 此操作将在本地群集中重新启动五个节点中的一个，并强制故障转移到在另一个节点上运行的其中一个辅助副本。 在执行此操作时，请注意来自测试客户端的输出，并注意虽然发生故障转移，但是计数器仍将继续递增。

## <a name="create-and-deploy-an-application-with-the-eclipse-neon-plugin"></a>使用 Eclipse Neon 插件创建和部署应用程序

Service Fabric 还为你提供预配，用于通过 Eclipse 创建、生成和部署 Service Fabric Java 应用程序。 安装 Eclipse 时，请选择“适用于 Java 开发人员的 Eclipse IDE”。 另外，Service Fabric 目前还支持适用于 Eclipse **Neon** 的插件。 请参阅详细文档 - [使用适用于 Eclipse 的 Service Fabric 插件在 Linux 上创建和部署第一个 Service Fabric Java 应用程序](service-fabric-get-started-eclipse.md)

## <a name="adding-more-services-to-an-existing-application"></a>将更多服务添加到现有应用程序

### <a name="using-command-line-utility"></a>使用命令行实用程序
若要将其他服务添加到使用 `yo` 创建的应用程序，请执行以下步骤：
1. 将目录更改为现有应用程序的根目录。  例如，如果 `MyApplication` 是 Yeoman 创建的应用程序，则使用 `cd ~/YeomanSamples/MyApplication`。
2. 运行 `yo azuresfjava:AddService`

### <a name="using-service-fabric-eclipse-plugin-for-java-on-linux"></a>在 Linux 上使用适用于 Java 的 Service Fabric Eclipse 插件
若要将服务添加到使用适用于 Service Fabric 的 Eclipse 插件创建的现有应用程序，请参阅[此处](service-fabric-get-started-eclipse.md#add-new-service-fabric-service-to-your-service-fabric-application)的文档。

## <a name="next-steps"></a>后续步骤
* [使用适用于 Eclipse 的 Service Fabric 插件在 Linux 上创建和部署第一个 Service Fabric Java 应用程序](service-fabric-get-started-eclipse.md)
* [了解有关 Reliable Actors 的详细信息](service-fabric-reliable-actors-introduction.md)
* [使用 Azure CLI 与 Service Fabric 群集交互](service-fabric-azure-cli.md)
* [排查部署问题](service-fabric-azure-cli.md#troubleshooting)
* 了解 [Service Fabric 支持选项](service-fabric-support.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

