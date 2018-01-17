---
title: "在 Linux 上设置开发环境 | Microsoft Docs"
description: "在 Linux 上安装运行时和 SDK 并创建本地开发群集。 完成此设置后，便可以开始生成应用程序。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/5/2018
ms.author: subramar
ms.openlocfilehash: 3c59ad25294fe88f6145b6e55bb498cd958dbca2
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2018
---
# <a name="prepare-your-development-environment-on-linux"></a>在 Linux 上准备开发环境
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

若要在 Linux 开发计算机上部署和运行 [Azure Service Fabric 应用程序](service-fabric-application-model.md)，请安装运行时和常用 SDK。 还可以安装用于 Java 和 .NET Core 开发的可选 SDK。

## <a name="prerequisites"></a>系统必备

支持使用以下操作系统版本进行开发：

* Ubuntu 16.04 (`Xenial Xerus`)

## <a name="installation-methods"></a>安装方法

### <a name="1-script-installation"></a>1.脚本安装

为方便起见，我们提供了一个脚本用于连同 **sfctl** CLI 一起安装 Service Fabric 运行时和 Service Fabric 通用 SDK。 在以下部分中运行手动安装步骤，以确定正在安装的组件以及同意的许可证。 运行该脚本即认为你同意所要安装的所有软件的许可条款。 

成功执行脚本后，可以直接跳到[设置本地群集](#set-up-a-local-cluster)。

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

### <a name="2-manual-installation"></a>2.手动安装
若要手动安装 Service Fabric 运行时和通用 SDK，请遵照本指南的余下部分。

## <a name="update-your-apt-sources"></a>更新 APT 源
若要通过 apt-get 命令行工具安装 SDK 和关联的运行时包，必须首先更新高级打包工具 (APT) 源。

1. 打开终端。
2. 将 Service Fabric 存储库添加到源列表。

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. 将 `dotnet` 存储库添加到源列表。

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. 向 APT Keyring 添加新的 Gnu 隐私防护（GnuPG 或 GPG）密钥。

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. 向 APT Keyring 添加官方的 Docker GPG 密钥。

    ```bash
    sudo apt-get install curl
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. 设置 Docker 存储库。

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. 根据新添加的存储库刷新包列表。

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-local-cluster-setup"></a>为本地群集设置安装并设置 Service Fabric SDK

源进行更新后，即可安装 SDK。 安装 Service Fabric SDK 包，确认安装，并同意许可协议。

```bash
sudo apt-get install servicefabricsdkcommon
```

>   [!TIP]
>   以下命令自动接受 Service Fabric 包的许可证：
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

## <a name="set-up-a-local-cluster"></a>设置本地群集
  完成安装后，应该能够启动本地群集。

  1. 运行群集安装程序脚本。

      ```bash
      sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
      ```

  2. 打开 Web 浏览器，转到 [Service Fabric Explorer](http://localhost:19080/Explorer)。 如果群集已开始，应看到 Service Fabric Explorer 仪表板。

      ![Linux 上的 Service Fabric Explorer][sfx-linux]

  此时，你就能够部署预建的 Service Fabric 应用程序包或基于来宾容器或来宾可执行文件的新应用程序包。 若要使用 Java 或 .NET Core SDK 构建新服务，请遵循后续部分中的可选设置步骤操作。


  > [!NOTE]
  > Linux 不支持独立的群集。
  >


>   [!TIP]
    如果有可用的 SSD 磁盘，建议配合使用 `--clusterdataroot` 与 devclustersetup.sh 来传递 SSD 文件夹路径，以实现优异的性能。

## <a name="set-up-the-service-fabric-cli"></a>设置 Service Fabric CLI

[Service Fabric CLI](service-fabric-cli.md) 包含用来与 Service Fabric 实体（包括群集和应用程序）交互的命令。
按照 [Service Fabric CLI](service-fabric-cli.md) 中的说明安装 CLI。


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>为容器和来宾可执行文件安装 Yeoman 生成器
Service Fabric 提供基架工具，可以借助此类工具，使用 Yeoman 模板生成器从终端创建 Service Fabric 应用程序。 遵循以下步骤安装 Service Fabric Yeoman 模板生成器：

1. 在计算机上安装 nodejs 和 NPM

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. 通过 NPM 在计算机上安装 [Yeoman](http://yeoman.io/) 模板生成器

  ```bash
  sudo npm install -g yo
  ```
3. 通过 NPM 安装 Service Fabric Yeo 容器生成器和来宾可执行文件生成器

  ```bash
  sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
  sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
  ```

安装生成器后，应该可以分别运行 `yo azuresfguest` 或 `yo azuresfcontainer`，创建来宾可执行文件或容器服务。

## <a name="set-up-net-core-20-development"></a>设置 .NET Core 2.0 开发

安装[用于 Ubuntu 的 .NET Core 2.0 SDK](https://www.microsoft.com/net/core#linuxubuntu)，开始[创建 C# Service Fabric 应用程序](service-fabric-create-your-first-linux-application-with-csharp.md)。 .NET Core 2.0 Service Fabric 应用程序包托管在 NuGet.org 上，目前以预览版提供。

## <a name="set-up-java-development"></a>设置 Java 开发

若要使用 Java 生成 Service Fabric 服务，请安装 JDK 1.8 和 Gradle 以运行生成任务。 以下代码片段安装 Open JDK 1.8 和 Gradle。 Service Fabric Java 库是从 Maven 拉取的。

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

## <a name="install-the-eclipse-neon-plug-in-optional"></a>安装 Eclipse Neon 插件（可选）

可以在面向 Java 开发人员的 Eclipse IDE 中安装用于 Service Fabric 的 Eclipse 插件。 可以使用 Eclipse，在 Service Fabric Java 应用程序的基础上创建 Service Fabric 来宾可执行应用程序和容器应用程序。

1. 在 Eclipse 中，请确保已安装最新 Eclipse Neon 和最新 Buildship（1.0.17 或更高版本）。 可以通过“帮助” > “安装详细信息”检查已安装组件的版本。 可以按 [Eclipse Buildship：适用于 Gradle 的 Eclipse 插件][buildship-update]中的说明更新 Buildship。

2. 若要安装 Service Fabric 插件，请选择“帮助” > “安装新软件”。

3. 在“使用”框中，键入“http://dl.microsoft.com/eclipse”。

4. 单击 **“添加”**。

    ![“可用软件”页][sf-eclipse-plugin]

5. 选择 Service Fabric 插件，然后单击“下一步”。

6. 完成安装步骤，然后接受最新用户许可协议。

如果已安装 Service Fabric Eclipse 插件，请确保使用最新版本。 可以通过选择“帮助” > “安装详细信息”并在已安装插件的列表中搜索 Service Fabric 来进行检查。如果可以使用更新的版本，请选择“更新”。

有关详细信息，请参阅[使用适用于 Eclipse 的 Service Fabric 插件开发 Java 应用程序](service-fabric-get-started-eclipse.md)。

## <a name="update-the-sdk-and-runtime"></a>更新 SDK 和运行时

若要更新到最新版本的 SDK 和运行时，请运行以下命令：

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
若要从 Maven 更新 Java SDK 二进制文件，需在 ``build.gradle`` 文件中更新相应的二进制文件的版本详细信息，使之指向最新 版本。 若要确切地了解需要在何处更新版本，可参阅[此处](https://github.com/Azure-Samples/service-fabric-java-getting-started)提供的 Service Fabric 入门示例中的任何 ``build.gradle`` 文件。

> [!NOTE]
> 更新这些包可能导致本地开发群集停止运行。 按本页说明操作，在升级后重新启动本地群集。

## <a name="remove-the-sdk"></a>删除 SDK
若要删除 Service Fabric SDK，请运行以下命令：

```bash
sudo apt-get remove servicefabric servicefabicsdkcommon
sudo npm uninstall generator-azuresfcontainer
sudo npm uninstall generator-azuresfguest
sudo apt-get install -f
```

## <a name="next-steps"></a>后续步骤

* [使用 Yeoman 在 Linux 上创建和部署第一个 Service Fabric Java 应用程序](service-fabric-create-your-first-linux-application-with-java.md)
* [使用适用于 Eclipse 的 Service Fabric 插件在 Linux 上创建和部署第一个 Service Fabric Java 应用程序](service-fabric-get-started-eclipse.md)
* [在 Linux 上创建第一个 CSharp 应用程序](service-fabric-create-your-first-linux-application-with-csharp.md)
* [在 OSX 上准备开发环境](service-fabric-get-started-mac.md)
* [在 Windows 上准备 Linux 开发环境](service-fabric-local-linux-cluster-windows.md)
* [使用 Service Fabric CLI 管理应用程序](service-fabric-application-lifecycle-sfctl.md)
* [Service Fabric Windows/Linux 差异](service-fabric-linux-windows-differences.md)
* [Service Fabric CLI 入门](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
