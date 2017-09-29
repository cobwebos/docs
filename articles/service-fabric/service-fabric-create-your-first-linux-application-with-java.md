---
title: "在 Linux 上创建 Azure Service Fabric Reliable Actors 应用程序 | Microsoft Docs"
description: "了解如何在五分钟内创建并部署一个 Java Service Fabric Reliable Actors 应用程序。"
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
ms.date: 09/20/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: 68f9492231d367b1ede6ab032ec1c66c75150957
ms.contentlocale: zh-cn
ms.lasthandoff: 09/21/2017

---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>在 Linux 上创建第一个 Java Service Fabric Reliable Actors 应用程序
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java-Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

借助本快速入门，只需几分钟即可在 Linux 开发环境中创建第一个 Azure Service Fabric Java 应用程序。  完成后，即拥有一个在本地开发群集上运行的简单 Java 单一服务应用程序。  

## <a name="prerequisites"></a>先决条件
开始之前，请安装 Service Fabric SDK、Service Fabric CLI，并在 [Linux 开发环境](service-fabric-get-started-linux.md)中设置开发群集。 如果使用 Mac OS X，则可[使用 Vagrant 在虚拟机中设置 Linux 开发环境](service-fabric-get-started-mac.md)。

还需要安装 [Service Fabric CLI](service-fabric-cli.md)。

### <a name="install-and-set-up-the-generators-for-java"></a>为 Java 安装和设置生成器
Service Fabric 提供基架工具，可以借助此类工具，使用 Yeoman 模板生成器从终端创建 Service Fabric Java 应用程序。 请执行以下步骤，确保已经有可以在计算机上运行的适用于 Java 的 Service Fabric yeoman 模板生成器。
1. 在计算机上安装 nodejs 和 NPM

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. 通过 NPM 在计算机上安装 [Yeoman](http://yeoman.io/) 模板生成器

  ```bash
  sudo npm install -g yo
  ```
3. 通过 NPM 安装 Service Fabric Yeo Java 应用程序生成器

  ```bash
  sudo npm install -g generator-azuresfjava
  ```

## <a name="create-the-application"></a>创建应用程序
Service Fabric 应用程序包含一个或多个服务，每个服务都在提供应用程序功能时具有特定角色。 有了在上一部分安装的生成器，就可以轻松地创建第一个服务，并在以后添加更多服务。  此外，还可使用适用于 Eclipse 的插件创建、生成和部署 Service Fabric Java 应用程序。 请参阅[使用 Eclipse 创建和部署第一个 Java 应用程序](service-fabric-get-started-eclipse.md)。 对于本快速入门，使用 Yeoman 创建具有单项服务的应用程序，该服务用于存储和获取获取计数器值。

1. 在终端中，键入 ``yo azuresfjava``。
2. 命名应用程序。
3. 选择第一个服务的类型并将其命名。 对于本教程，请选择“可靠角色服务”。 有关其他服务类型的详细信息，请参阅 [Service Fabric 编程模型概述](service-fabric-choose-framework.md)。
   ![适用于 Java 的 Service Fabric Yeoman 生成器][sf-yeoman]

## <a name="build-the-application"></a>构建应用程序
Service Fabric Yeoman 模板包含 [Gradle](https://gradle.org/) 的生成脚本，可用于从终端生成应用程序。
Service Fabric Java 依赖项从 Maven 提取。 若要生成和使用 Service Fabric Java 应用程序，需确保已安装 JDK 和 Gradle。 如果尚未安装，可运行以下命令来安装 JDK (openjdk-8-jdk) 和 Gradle：

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

若要生成并打包应用程序，请运行以下命令：

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>部署应用程序
生成应用程序后，可以将其部署到本地群集。

1. 连接到本地 Service Fabric 群集。

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. 运行模板中提供的安装脚本可将应用程序包复制到群集的映像存储区、注册应用程序类型和创建应用程序实例。

    ```bash
    ./install.sh
    ```

部署生成的应用程序时，其方式与部署任何其他 Service Fabric 应用程序相同。 如需详细的说明，请参阅相关文档，了解如何[使用 Service Fabric CLI 管理 Service Fabric 应用程序](service-fabric-application-lifecycle-sfctl.md)。

这些命令的参数可以在应用程序包内的已生成清单中找到。

应用程序部署完以后，请打开浏览器并导航到 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)，其地址为 [http://localhost:19080/Explorer](http://localhost:19080/Explorer)。
然后，展开“应用程序”节点，注意现在有一个条目是用于你的应用程序类型，另一个条目用于该类型的第一个实例。

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

## <a name="remove-the-application"></a>删除应用程序
使用模板中提供的卸载脚本可从群集的映像存储区删除应用程序实例、注销应用程序包并删除应用程序包。

```bash
./uninstall.sh
```

在 Service Fabric Explorer 中，可看到应用程序和应用程序类型不再显示在“应用程序”节点中。

## <a name="service-fabric-java-libraries-on-maven"></a>Maven 上的 Service Fabric Java 库
Service Fabric Java 库已托管在 Maven 中。 可以在项目的 ``pom.xml`` 或 ``build.gradle`` 中添加依赖项，以便使用 mavenCentral 提供的 Service Fabric Java 库。 

### <a name="actors"></a>执行组件

针对应用程序的 Service Fabric Reliable Actor 支持。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors-preview:0.12.0'
  }
  ```

### <a name="services"></a>服务

针对应用程序的 Service Fabric Reliable Services 支持。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services-preview:0.12.0'
  }
  ```

### <a name="others"></a>其他
#### <a name="transport"></a>传输

针对 Service Fabric Java 应用程序的传输层支持。 不需向 Reliable Actor 或 Service 应用程序显式添加此依赖项，除非在传输层编程。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport-preview:0.12.0'
  }
  ```

#### <a name="fabric-support"></a>Fabric 支持

针对 Service Fabric（与本机 Service Fabric 运行时通信）的系统级支持。 不需向 Reliable Actor 或 Service 应用程序显式添加此依赖项。 当你包括上述其他依赖项时，会自动从 Maven 提取此依赖项。

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-preview:0.12.0'
  }
  ```

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>迁移要与 Maven 配合使用的旧式 Service Fabric Java 应用程序
我们最近已将 Service Fabric Java 库从 Service Fabric Java SDK 移至 Maven 存储库。 虽然使用 Yeoman 或 Eclipse 生成的新应用程序会生成最近更新的项目（会兼容 Maven），但你可以对现有的 Service Fabric 无状态或执行组件 Java 应用程序（此前使用 Service Fabric Java SDK）进行更新，使之能够使用 Maven 提供的 Service Fabric Java 依赖项。 请按[此处](service-fabric-migrate-old-javaapp-to-use-maven.md)提及的步骤操作，确保旧版应用程序兼容 Maven。

## <a name="next-steps"></a>后续步骤

* [使用 Eclipse 在 Linux 上创建第一个 Service Fabric Java 应用程序](service-fabric-get-started-eclipse.md)
* [了解有关 Reliable Actors 的详细信息](service-fabric-reliable-actors-introduction.md)
* [使用 Service Fabric CLI 与 Service Fabric 群集交互](service-fabric-cli.md)
* 了解 [Service Fabric 支持选项](service-fabric-support.md)
* [Service Fabric CLI 入门](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

