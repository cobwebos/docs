---
title: "在 Mac OS X 上设置兼容 Azure Service Fabric 的开发环境 | Microsoft Docs"
description: "安装运行时、SDK 和工具并创建本地开发群集。 完成此设置后，就可以在 Mac OS X 上开始生成应用程序。"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/17/2017
ms.author: saysa
ms.openlocfilehash: 309fcb901a1a3878edbfbe06e12122615b74664e
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2017
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>在 Mac OS X 上设置开发环境
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

可以使用 Mac OS X 生成在 Linux 群集上运行的 Service Fabric 应用程序。本文档介绍了如何设置 Mac 以用于开发。

## <a name="prerequisites"></a>先决条件
Service Fabric 不在 OS X 本机上运行。为了运行本地 Service Fabric 群集，我们提供了预配置的 Docker 容器映像。 准备事项：

* 至少 4 GB RAM
* 最新版的 [Docker](https://www.docker.com/)
* 访问 Service Fabric 单机 Docker 容器[映像](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/)

>[!TIP]
> * 可以按照在官方的 Docker [文档](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install)中提到的步骤，在 Mac 上安装 Docker。 
> * 安装完以后，请按照[此处](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine)提到的步骤验证其是否已正确安装。


## <a name="create-a-local-container-and-setup-service-fabric"></a>创建本地容器和设置 Service Fabric
若要设置本地 Docker 容器并在其上运行 Service Fabric 群集，请执行以下步骤：

1. 从 Docker 中心存储库拉取映像：

    ```bash
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. 使用以下内容更新主机上的 Docker 守护程序配置并重启 Docker 守护程序： 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    可以直接在 docker 安装路径下的 daemon.json（其位置可能因计算机而异，例如 - ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json）中更新此内容。 建议的更新方法是：转到 Docker 图标 >“首选项”>“守护程序”>“高级”，在该处更新。

3. 启动带映像的 Service Fabric 单机容器实例：

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    > * 可通过指定容器实例的名称，以更具可读性的方式对其进行处理。 
    > * 如果应用程序正在侦听特定端口，则必须使用附加 -p 标记指定它。 例如，如果应用程序正在侦听端口 8080，则运行 docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox servicefabricoss/service-fabric-onebox

4. 以交互式 ssh 模式登录到 Docker 容器：

    ```bash
    docker exec -it sfonebox bash
    ```

5. 运行安装程序脚本，该脚本会提取所需的依赖项，然后启动容器中的群集。

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. 成功完成步骤 5 以后，即可从 Mac 转到 ``http://localhost:19080``，然后便会看到 Service Fabric 资源管理器。

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>在 Mac 上设置 Service Fabric CLI (sfctl)

按照 [Service Fabric CLI](service-fabric-cli.md#cli-mac) 中的说明在 Mac 上安装 Service Fabric CLI (`sfctl`)。
Azure CLI 命令，用来与 Service Fabric 实体（包括群集、应用程序和服务）交互。

## <a name="create-application-on-your-mac-using-yeoman"></a>使用 Yeoman 在 Mac 上创建应用程序

Service Fabric 提供基架工具，可以借助此类工具，使用 Yeoman 模板生成器从终端创建 Service Fabric 应用程序。 执行以下步骤，确保已经有可以在计算机上运行的 Service Fabric yeoman 模板生成器。

1. 需在 Mac 上安装 Node.js 和 NPM。 如果没有安装，可按照以下步骤通过 Homebrew 来安装 Node.js 和 NPM：

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. 通过 NPM 在计算机上安装 [Yeoman](http://yeoman.io/) 模板生成器。

    ```bash
    npm install -g yo
    ```
3. 请按入门[文档](service-fabric-get-started-linux.md)中的步骤，安装要使用的 Yeoman 生成器。 若要使用 Yeoman 来创建 Service Fabric 应用程序，请执行以下步骤：

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. 若要在 Mac 上生成 Service Fabric Java 应用程序，则需在主机上安装 JDK 1.8 和 Gradle。 如果该应用程序尚未安装，可以使用 [HomeBrew](https://brew.sh/) 进行安装。 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-application-on-your-mac-from-terminal"></a>通过 Terminal 在 Mac 上部署应用程序

创建和生成 Service Fabric 应用程序以后，即可使用 [Service Fabric CLI](service-fabric-cli.md#cli-mac) 部署该应用程序，只需执行以下步骤即可：

1. 连接到在 Mac 的容器实例中运行的 Service Fabric 群集。

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. 转到项目目录中，运行安装脚本。

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>设置 .NET Core 2.0 开发

安装[用于 Mac 的 .NET Core 2.0 SDK](https://www.microsoft.com/net/core#macos)，开始[创建 C# Service Fabric 应用程序](service-fabric-create-your-first-linux-application-with-csharp.md)。 .NET Core 2.0 Service Fabric 应用程序包托管在 NuGet.org 上，目前以预览版提供。

## <a name="install-the-service-fabric-plugin-for-eclipse-neon-on-your-mac"></a>在 Mac 上为 Eclipse Neon 安装 Service Fabric 插件

Service Fabric 为**适用于 Java IDE 的 Eclipse Neon** 提供了一个插件，可简化创建、生成和部署 Java 服务的过程。 可以按照通用[文档](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon)中提及的安装步骤，安装 Service Fabric Eclipse 插件或将其更新到最新版本。

[Service Fabric Eclipse 文档](service-fabric-get-started-eclipse.md)中提到的所有其他步骤（生成应用程序、向应用程序添加服务、安装/卸载应用程序，等等）在此处也会适用。

若要使 Service Fabric Eclipse 插件与 Mac 上的 Docker 容器兼容，则除了执行上述步骤，还应使用与主机共享的路径来实例化该容器，如下所示：
```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox servicefabricoss/service-fabric-onebox
```
其中，``/Users/sayantan/work/workspaces/mySFWorkspace`` 是 Mac 上工作区的完全限定路径，``/tmp/mySFWorkspace`` 是要映射到的容器中的路径。

> [!NOTE]
>1. 如果你的工作区名称/路径有所不同，请在上面的 ``docker run`` 命令中对同一项进行相应的更新。
>2. 如果所启动容器的名称不是 ``sfonebox``，请在 Service Fabric 执行组件 Java 应用程序的 ``testclient.sh`` 文件中更新同一项。

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