---
title: 在 Mac OS X 上设置兼容 Azure Service Fabric 的开发环境 | Microsoft Docs
description: 安装运行时、SDK 和工具并创建本地开发群集。 完成此设置后，就可以在 Mac OS X 上开始生成应用程序了。
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: timlt
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: linux
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/17/2017
ms.author: suhuruli
ms.openlocfilehash: 87721428e1cd8a5360dcecc5f29225f813705a4f
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344750"
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>在 Mac OS X 上设置开发环境
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

可以使用 Mac OS X 生成在 Linux 群集上运行的 Azure Service Fabric 应用程序。本文档介绍了如何设置用于开发的 Mac。

## <a name="prerequisites"></a>先决条件
Azure Service Fabric 不在 Mac OS X 本机上运行。为了运行本地 Service Fabric 群集，我们提供了预配置的 Docker 容器映像。 准备事项：

* 至少 4 GB 的 RAM。
* 最新版的 [Docker](https://www.docker.com/)。

>[!TIP]
>
>若要在 Mac 上安装 Docker，请按 [Docker 文档](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install)中的步骤操作。 安装之后，请[验证安装](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine)。
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>创建本地容器和设置 Service Fabric
若要设置本地 Docker 容器并在其上运行 Service Fabric 群集，请执行以下步骤：

1. 使用以下设置更新主机上的 Docker 守护程序配置并重启 Docker 守护程序： 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    可以在 Docker 安装路径的 daemon.json 文件中直接更新这些设置。
    
    >[!NOTE]
    >
    >daemon.json 文件的位置因计算机而异。 例如，~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json。
    >
    >建议直接在 Docker 中修改守护程序配置设置。 选择 **Docker 图标**，然后选择“首选项” > “守护程序” > “高级”。
    >
    >测试大型应用程序时，我们建议增加分配给 Docker 的资源。 为此，可以选择 **Docker 图标**，然后选择“高级”来调整核心数量和内存量。

2. 在新目录中创建名为 `Dockerfile` 的文件，以生成 Service Fabric 映像：

    ```dockerfile
    FROM microsoft/service-fabric-onebox
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >可以修改此文件，以便在容器中添加更多程序或依赖项。
    >例如，添加 `RUN apt-get install nodejs -y` 可以支持将 `nodejs` 应用程序用作来宾可执行文件。
    
    >[!TIP]
    > 默认情况下，这样会拉取具有最新 Service Fabric 版本的映像。 如需特定的修订版本，请访问 [Docker 中心](https://hub.docker.com/r/microsoft/service-fabric-onebox/)页。

3. 若要通过 `Dockerfile` 生成可重用的映像，请打开终端并运行 `cd` 切换到 `Dockerfile` 所在的目录，然后运行：

    ```bash 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >此操作需要一段时间，但只需执行一次。

4. 现在，每当有需要时，都可以运行以下命令，快速启动 Service Fabric 的本地副本：

    ```bash 
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >为容器实例提供一个名称，以更具可读性的方式对其进行处理。 
    >
    >如果应用程序正在侦听特定端口，则必须使用附加的 `-p` 标记指定这些端口。 例如，如果应用程序正在侦听端口 8080，请添加下面的 `-p` 标记：
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. 群集需要一小段时间来启动，可以使用以下命令查看日志，或者通过 [http://localhost:19080](http://localhost:19080) 跳转到仪表板来查看群集运行状况：

    ```bash 
    docker logs sftestcluster
    ```



6. 完成后，可以使用以下命令来停止并清理容器：

    ```bash 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>已知限制 
 
 以下是在 Mac 的容器中运行的本地群集的已知限制： 
 
 * DNS 服务无法运行且不受支持 [问题 #132](https://github.com/Microsoft/service-fabric/issues/132)

 * 目前无法将容器应用程序部署到此本地群集

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>在 Mac 上设置 Service Fabric CLI (sfctl)

按照 [Service Fabric CLI](service-fabric-cli.md#cli-mac) 中的说明在 Mac 上安装 Service Fabric CLI (`sfctl`)。
CLI 命令支持与 Service Fabric 实体（包括群集、应用程序和服务）交互。

1. 若要在部署应用程序之前连接到群集，请运行以下命令。 

```bash
sfctl cluster select --endpoint http://localhost:19080
```

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>使用 Yeoman 在 Mac 上创建应用程序

Service Fabric 提供基架工具，可以借助此类工具，使用 Yeoman 模板生成器从终端创建 Service Fabric 应用程序。 执行以下步骤，确保已经有可以在计算机上运行的 Service Fabric Yeoman 模板生成器：

1. Node.js 和 Node 包管理器 (NPM) 必须安装在 Mac 上。 此软件可以使用 [HomeBrew](https://brew.sh/) 进行安装，如下所示：

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. 通过 NPM 在计算机上安装 [Yeoman](http://yeoman.io/) 模板生成器：

    ```bash
    npm install -g yo
    ```
3. 请按入门[文档](service-fabric-get-started-linux.md)中的步骤，安装首选的 Yeoman 生成器。 若要使用 Yeoman 来创建 Service Fabric 应用程序，请执行以下步骤：

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. 若要在 Mac 上生成 Service Fabric Java 应用程序，必须在主机上安装 JDK 1.8 和 Gradle。 此软件可以使用 [HomeBrew](https://brew.sh/) 进行安装，如下所示： 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>通过 Terminal 在 Mac 上部署应用程序

创建和生成 Service Fabric 应用程序以后，即可使用 [Service Fabric CLI](service-fabric-cli.md#cli-mac) 部署该应用程序：

1. 连接到在 Mac 的容器实例中运行的 Service Fabric 群集：

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. 从项目目录中运行安装脚本：

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>设置 .NET Core 2.0 开发

安装[用于 Mac 的 .NET Core 2.0 SDK](https://www.microsoft.com/net/core#macos)，开始[创建 C# Service Fabric 应用程序](service-fabric-create-your-first-linux-application-with-csharp.md)。 .NET Core 2.0 Service Fabric 应用程序包托管在目前以预览版形式推出的 NuGet.org 上。

## <a name="install-the-service-fabric-plug-in-for-eclipse-on-your-mac"></a>在 Mac 上为 Eclipse 安装 Service Fabric 插件

Azure Service Fabric 为适用于 Java IDE 的 Eclipse Neon（或更高版本）提供插件。 该插件可简化创建、生成和部署 Java 服务的过程。 若要为 Eclipse 安装 Service Fabric 插件或将其更新到最新版本，请执行[这些步骤](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse)。 [适用于 Eclipse 的 Service Fabric 文档](service-fabric-get-started-eclipse.md)中的其他步骤也适用：生成应用程序、向应用程序添加服务、卸载应用程序，等等。

最后一步是使用与主机共享的路径实例化该容器。 该插件需要此类实例化才能与 Mac 上的 Docker 容器配合使用。 例如：

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox microsoft/service-fabric-onebox
```

这些属性定义如下：
* `/Users/sayantan/work/workspaces/mySFWorkspace` 是 Mac 上的工作区的完全限定路径。
* `/tmp/mySFWorkspace` 是容器内部的路径，应将工作区映射到此路径。

>[!NOTE]
> 
>如果为工作区设置了其他名称/路径，请在 `docker run` 命令中更新这些值。
> 
>如果所启动容器的名称不是 `sfonebox`，请在 Service Fabric 执行组件 Java 应用程序的 testclient.sh 文件中更新名称值。
>

## <a name="next-steps"></a>后续步骤
<!-- Links -->
* [使用 Yeoman 在 Linux 上创建和部署第一个 Service Fabric Java 应用程序](service-fabric-create-your-first-linux-application-with-java.md)
* [使用适用于 Eclipse 的 Service Fabric 插件在 Linux 上创建和部署第一个 Service Fabric Java 应用程序](service-fabric-get-started-eclipse.md)
* [在 Azure 门户中创建 Service Fabric 群集](service-fabric-cluster-creation-via-portal.md)
* [使用 Azure 资源管理器创建 Service Fabric 群集](service-fabric-cluster-creation-via-arm.md)
* [了解 Service Fabric 应用程序模型](service-fabric-application-model.md)
* [使用 Service Fabric CLI 管理应用程序](service-fabric-application-lifecycle-sfctl.md)
* [在 Windows 上准备 Linux 开发环境](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
