---
title: "在 Linux 上使用 Java 创建第一个 Azure 微服务应用 | Microsoft 文档"
description: "使用 Java 创建和部署 Service Fabric 应用程序"
services: service-fabric
documentationcenter: java
author: rwike77
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/02/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 4ffab9eb858b05d3f5894e8753fb0fd4f38c4087
ms.contentlocale: zh-cn
ms.lasthandoff: 06/03/2017


---
# <a name="create-your-first-service-fabric-java-application-on-linux"></a>在 Linux 上创建第一个 Service Fabric Java 应用程序
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java-Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

借助本快速入门，只需几分钟即可在 Linux 开发环境中创建第一个 Azure Service Fabric Java 应用程序。  完成后，即拥有一个在本地开发群集上运行的简单 Java 单一服务应用。  

## <a name="prerequisites"></a>先决条件
开始之前，请安装 Service Fabric SDK、Azure CLI，并在 [Linux 开发环境](service-fabric-get-started-linux.md)中设置开发群集。 如果使用 Mac OS X，则可[使用 Vagrant 在虚拟机中设置 Linux 开发环境](service-fabric-get-started-mac.md)。

## <a name="create-the-application"></a>创建应用程序
Service Fabric 应用程序包含一个或多个服务，每个服务都在提供应用程序功能时具有特定角色。 用于 Linux 的 Service Fabric SDK 包括 [Yeoman](http://yeoman.io/) 生成器，利用它可以轻松地创建第一个服务和在以后添加其他服务。  此外，还可使用适用于 Eclipse 的插件创建、生成和部署 Service Fabric Java 应用程序。 请参阅[使用 Eclipse 创建和部署第一个 Java 应用程序](service-fabric-get-started-eclipse.md)。 对于本快速入门，使用 Yeoman 创建具有单项服务的应用程序，该服务用于存储和获取获取计数器值。

1. 在终端中，键入 ``yo azuresfjava``。
2. 命名应用程序。 
3. 选择第一个服务的类型并将其命名。 对于本教程，请选择“可靠角色服务”。 有关其他服务类型的详细信息，请参阅 [Service Fabric 编程模型概述](service-fabric-choose-framework.md)。
   ![适用于 Java 的 Service Fabric Yeoman 生成器][sf-yeoman]

## <a name="build-the-application"></a>构建应用程序
Service Fabric Yeoman 模板包含 [Gradle](https://gradle.org/) 的生成脚本，可用于从终端生成应用。 若要生成和打包应用，请运行以下命令：

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

2. 运行模板中提供的安装脚本可将应用程序包复制到群集的映像存储区、注册应用程序类型和创建应用程序实例。

    ```bash
    ./install.sh
    ```

3. 打开浏览器并导航到 http://localhost:19080/Explorer 处的 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)（如果在 Mac OS X 上使用 Vagrant，则使用 VM 的专用 IP 替换 localhost）。

4. 展开“应用程序”节点，注意现在有一个条目用于应用程序类型，另一个条目用于该类型的第一个实例。

## <a name="start-the-test-client-and-perform-a-failover"></a>启动测试客户端并执行故障转移
角色自身未执行任何操作，它们需要其他服务或客户端向其发送消息。 Actor 模板包括可用于与 actor 服务进行交互的简单测试脚本。

1. 使用监视实用程序运行该脚本来查看 actor 服务的输出。  测试脚本对角色调用 `setCountAsync()` 方法来递增计数器，对角色调用 `getCountAsync()` 方法来获取新的计数器值，并向控制台显示该值。

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. 在 Service Fabric Explorer 中，找到托管角色服务的主要副本的节点。 以下屏幕截图中显示的是节点 3。 主要服务副本处理读写操作。  然后将服务状态更改向外复制到次要副本，该副本在下方屏幕截图的节点 0 和 1 上运行。

    ![在 Service Fabric Explorer 中查找主副本][sfx-primary]

3. 在“节点”中，单击上一步找到的节点，然后从“操作”菜单中选择“停用(重启)”。 此操作重启运行主要服务副本的节点，并强制故障转移到在其他节点上运行的其中一个次要副本。  该次要副本将提升为主要副本，并在其他节点上创建另一次要副本，然后主要副本开始执行读/写操作。 节点重启时，请注意来自测试客户端的输出，并注意虽然发生故障转移，但计数器仍继续递增。

## <a name="add-another-service-to-the-application"></a>向应用程序添加另一服务
若要使用 `yo` 将其他服务添加到现有应用程序，请执行以下步骤：
1. 将目录更改为现有应用程序的根目录。  例如，如果 `MyApplication` 是 Yeoman 创建的应用程序，则使用 `cd ~/YeomanSamples/MyApplication`。
2. 运行 `yo azuresfjava:AddService`
3. 按照前述步骤，生成并部署应用。

## <a name="remove-the-application"></a>删除应用程序
使用模板中提供的卸载脚本可从群集的映像存储区删除应用实例、注销应用程序包并删除应用程序包。

```bash
./uninstall.sh
```

在 Service Fabric Explorer 中，可看到应用程序和应用程序类型不再显示在“应用程序”节点中。

## <a name="next-steps"></a>后续步骤
* [使用 Eclipse 在 Linux 上创建第一个 Service Fabric Java 应用程序](service-fabric-get-started-eclipse.md)
* [了解有关 Reliable Actors 的详细信息](service-fabric-reliable-actors-introduction.md)
* [使用 Azure CLI 与 Service Fabric 群集交互](service-fabric-azure-cli.md)
* [排查部署问题](service-fabric-azure-cli.md#troubleshooting)
* 了解 [Service Fabric 支持选项](service-fabric-support.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

