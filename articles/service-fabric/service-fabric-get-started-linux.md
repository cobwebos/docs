---
title: "在 Linux 上设置开发环境 | Microsoft Docs"
description: "在 Linux 上安装运行时和 SDK 并创建本地开发群集。 完成此设置后，你就可以开始生成应用程序。"
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: ff2e3c27f9fc02ebe30380798be00211113208e3
ms.openlocfilehash: 4f3c0cee8fe686c5355ce460064a7188d35f3e44


---
# <a name="prepare-your-development-environment-on-linux"></a>在 Linux 上准备开发环境
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
>  

 若要在 Linux 开发计算机上部署和运行 [Azure Service Fabric 应用程序](service-fabric-application-model.md)，请安装运行时和常用 SDK。 还可以安装适用于 Java 和 .NET Core 的可选 SDK。

## <a name="prerequisites"></a>先决条件
### <a name="supported-operating-system-versions"></a>支持的操作系统版本
支持使用以下操作系统版本进行开发：

* Ubuntu 16.04 (Xenial Xerus)

## <a name="update-your-apt-sources"></a>更新 apt 源
若要通过 apt-get 安装 SDK 和关联的运行时包，必须首先更新 apt 源。

1. 打开终端。
2. 将 Service Fabric 存储库添加到源列表。
   
    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```
3. 将新的 GPG 密钥添加到你 apt keyring。
   
    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```
4. 根据新添加的存储库刷新包列表。
   
    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk"></a>安装和设置 SDK
源进行更新后，可以安装 SDK。

1. 安装 Service Fabric SDK 包。 系统将要求你确认安装并同意许可协议。
   
    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```
2. 运行 SDK 安装程序脚本。
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```


## <a name="set-up-the-azure-cross-platform-cli"></a>设置 Azure 跨平台 CLI
[Azure 跨平台 CLI][azure-xplat-cli-github] 包含用来与 Service Fabric 实体（包括群集和应用程序）交互的命令。 它基于 Node.js，因此，请[务必先安装 Node][install-node]，然后继续遵照下面的说明操作。

1. 将 github 存储库克隆到你的开发计算机。
   
    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```
2. 切换到克隆存储库并使用节点包管理器 (npm) 安装 CLI 的依赖项。
   
    ```bash
    cd azure-xplat-cli
    npm install
    ```
3. 从克隆存储库的 bin/azure 文件夹创建到 /usr/bin/azure 的符号链接，以便将其添加到你的路径，并且可从任何目录获取命令。
   
    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```
4. 最后，启用自动完成 Service Fabric 命令。
   
    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

## <a name="set-up-a-local-cluster"></a>设置本地群集
如果所有内容都已成功安装，则应该能够启动本地群集。

1. 运行群集安装程序脚本。
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
2. 打开 Web 浏览器并导航到 http://localhost:19080/Explorer。 如果群集已开始，你应看到 Service Fabric Explorer 仪表板。
   
    ![Linux 上的 Service Fabric Explorer][sfx-linux]

此时，你就能够部署预建的 Service Fabric 应用程序包或基于来宾容器或来宾可执行文件的新应用程序包。 若要使用 Java 或 .NET Core SDK 生成新的服务，请按照下面的可选安装程序步骤操作。

## <a name="install-the-java-sdk-and-eclipse-neon-plugin-optional"></a>安装 Java SDK 和 Eclipse Neon 插件（可选）
Java SDK 提供使用 Java 生成 Service Fabric 服务所需的库和模板。

1. 安装 Java SDK 包。
   
    ```bash
    sudo apt-get install servicefabricsdkjava
    ```
2. 运行 SDK 安装程序脚本。
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

可以从 Eclipse Neon IDE 内安装用于 Service Fabric 的 Eclipse 插件。

1. 在 Eclipse 中，确保已安装了 Buildship 版本 1.0.17 或更高版本。 可以通过“帮助”>“安装详细信息”检查已安装组件的版本。 可以使用[此处][buildship-update]的说明更新 Buildship。
2. 若要安装 Service Fabric 插件，请选择“帮助”>“安装新软件...”
3. 在“使用”文本框中，输入：http://dl.windowsazure.com/eclipse/servicefabric
4. 单击“添加”。
   
    ![Eclipse 插件][sf-eclipse-plugin]
5. 选择 Service Fabric 插件，然后单击“下一步”。
6. 继续执行安装并接受最终用户许可协议。

## <a name="install-the-net-core-sdk-optional"></a>安装 .NET Core SDK（可选）
.NET Core SDK 提供使用跨平台 .NET Core 生成 Service Fabric 服务所需的库和模板。

1. 安装 .NET Core SDK 包。
   
    ```bash
    sudo apt-get install servicefabricsdkcsharp
    ```
2. 运行 SDK 安装程序脚本。
   
    ```bash
    sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
    ```
## <a name="updating-the-sdk-and-runtime"></a>更新 SDK 和运行时

若要更新到最新版本的 SDK 和运行时，请运行以下步骤（从列表中删除不想要更新或安装的 SDK）：

    ```bash
    sudo apt-get update
    sudo apt-get install servicefabric, servicefabricsdkcommon, servicefabricsdkcsharp, servicefabricsdkjava
    ```

若要更新 CLI，请导航到 CLI 克隆到的目录，然后运行 `git pull` 进行更新。 

## <a name="next-steps"></a>后续步骤
* [在 Linux 上创建第一个 Java 应用程序](service-fabric-create-your-first-linux-application-with-java.md)
* [在 OSX 上准备开发环境](service-fabric-get-started-mac.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png



<!--HONumber=Dec16_HO1-->


