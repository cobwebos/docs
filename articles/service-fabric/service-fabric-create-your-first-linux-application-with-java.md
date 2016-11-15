---
title: "在 Linux 上使用 Java 创建你的第一个 Service Fabric 应用程序 | Microsoft Docs"
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
ms.date: 10/04/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 288d504b44fd7588a03a31171da1bfb332e2429f


---
# <a name="create-your-first-azure-service-fabric-application"></a>创建第一个 Azure Service Fabric 应用程序
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java-Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
> 
> 

Service Fabric 提供用于在 Linux 上使用 .NET Core 和 Java 构建服务的 SDK。 本教程介绍了如何使用 Java 创建适用于 Linux 的应用程序并构建服务。

## <a name="prerequisites"></a>先决条件
开始之前，请确保已[设置 Linux 开发环境](service-fabric-get-started-linux.md)。 如果使用的是 Mac OS X，则可以[使用 Vagrant 在虚拟机中设置 Linux 单机环境](service-fabric-get-started-mac.md)。

## <a name="create-the-application"></a>创建应用程序
Service Fabric 应用程序可以包含一个或多个服务，每个服务都在提供应用程序功能时具有特定角色。 用于 Linux 的 Service Fabric SDK 包括 [Yeoman](http://yeoman.io/) 生成器，利用它可以轻松地创建第一个服务和在以后添加其他服务。 让我们使用 Yeoman 来创建具有单项服务的新应用程序。

1. 在终端中，键入 **yo azuresfjava**。
2. 命名应用程序。
3. 选择第一个服务的类型并将其命名。 对于本教程，我们会选择“可靠角色服务”。
   
   ![适用于 Java 的 Service Fabric Yeoman 生成器][sf-yeoman]

> [!NOTE]
> 有关选项的详细信息，请参阅 [Service Fabric 编程模型概述](service-fabric-choose-framework.md)。
> 
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
3. 单击上一步找到的节点，然后在“操作”菜单中选择“停用(重启)”。 此操作将在本地群集中重新启动五个节点中的一个，并强制故障转移到在另一个节点上运行的其中一个辅助副本。 执行此操作时，请注意来自测试客户端的输出，并注意虽然发生故障转移，但是计数器仍将继续递增。

## <a name="build-and-deploy-an-application-with-the-eclipse-neon-plugin"></a>使用 Eclipse Neon 插件生成和部署应用程序
如果安装了 Eclipse Neon 的服务插件，则可以将其用于创建、生成和部署使用 Java 生成的 Service Fabric 应用程序。  安装 Eclipse 时，请选择“适用于 Java 开发人员的 Eclipse IDE”。

### <a name="create-the-application"></a>创建应用程序
Service Fabric 插件可通过 Eclipse 扩展性获得。

1. 在 Eclipse 中，选择“文件”>“其他”>“Service Fabric”。 然后将出现一组选项，其中包括“角色”和“容器”。
   
    ![Eclipse 中的 Service Fabric 模板][sf-eclipse-templates]
2. 在此例中，选择“无状态服务”。
3. 系统会要求确认使用 Service Fabric，这样可以优化 Eclipse 以用于 Service Fabric 项目。 选择“是”。

### <a name="deploy-the-application"></a>部署应用程序
Service Fabric 模板包括一组用于生成和部署应用程序的 Gradle 任务，该任务可以通过 Eclipse 触发。

1. 选择“运行”>“运行配置”。
2. 展开“Gradle 项目”并选择“ServiceFabricDeployer”。
3. 单击“**运行**”。

你的应用将在几分钟内进行生成和部署。 可以从 Service Fabric Explorer 监视其状态。

## <a name="next-steps"></a>后续步骤
* [了解有关 Reliable Actors 的详细信息](service-fabric-reliable-actors-introduction.md)
* [使用 Azure CLI 与 Service Fabric 群集交互](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png



<!--HONumber=Nov16_HO2-->


