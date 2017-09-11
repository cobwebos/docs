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
ms.date: 08/21/2017
ms.author: saysa
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 8b4fc0ab9034263418cac42ced203035e0a8fcad
ms.contentlocale: zh-cn
ms.lasthandoff: 08/23/2017

---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>在 Mac OS X 上设置开发环境
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

可以使用 Mac OS X 生成在 Linux 群集上运行的 Service Fabric 应用程序。本文介绍了如何设置 Mac 以用于开发。

## <a name="prerequisites"></a>先决条件
Service Fabric 不在 OS X 本机上运行。为了运行本地 Service Fabric 群集，我们使用 Vagrant 和 VirtualBox 提供了预配置的 Ubuntu 虚拟机。 准备事项：

* [Vagrant（v1.8.4 或更高版本）](http://www.vagrantup.com/downloads.html)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

>[!NOTE]
> 需使用 Vagrant 和 VirtualBox 的相互支持的版本。 在不受支持的 VirtualBox 版本上，Vagrant 的行为可能不稳定。
>

## <a name="create-the-local-vm"></a>创建本地 VM
若要创建包含 5 节点 Service Fabric 群集的本地 VM，请执行以下步骤：

1. 克隆 `Vagrantfile` 存储库

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
    此步骤获取包含 VM 配置和 VM 下载位置的文件 `Vagrantfile`。

2. 导航到本地克隆存储库

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. （可选）修改默认的 VM 设置

    默认情况下，本地 VM 采用如下配置：

   * 分配 3 GB 内存
   * 在 IP 192.168.50.50 配置专用主机网络，以便能够从 Mac 主机传递流量

     可以更改这些设置或向 `Vagrantfile` 中的 VM 添加其他配置。 请参阅 [Vagrant 文档](http://www.vagrantup.com/docs)，获得配置选项的完整列表。
4. 创建 VM

    ```bash
    vagrant up
    ```

   此步骤包括：下载预配置的 VM 映像，在本地启动它，并在其中设置本地 Service Fabric 群集。 完成此步骤预计需要几分钟的时间。 如果成功完成安装，输出中会出现一条消息，指示群集正在启动。

    ![群集安装会在 VM 设置后启动][cluster-setup-script]

    >[!TIP]
    > 如果 VM 下载耗时过长，可以使用 wget 或 curl 下载，也可通过浏览器下载，只需导航到文件 `Vagrantfile` 中通过 **config.vm.box_url** 指定的链接即可。 将其下载到本地以后，请编辑 `Vagrantfile`，以便指向下载了映射的本地路径。 例如，如果已将映像下载到 /home/users/test/azureservicefabric.tp8.box，则请将 **config.vm.box_url** 设置为该路径。
    >

5. 导航到 http://192.168.50.50:19080/Explorer 的 Service Fabric Explorer（假设保留了默认的专用网络 IP 地址），测试是否已正确安装群集。

    ![从主机 Mac 查看的 Service Fabric Explorer][sfx-mac]


## <a name="create-application-on-mac-using-yeoman"></a>使用 Yeoman 在 Mac 上创建应用程序
Service Fabric 提供基架工具，可以借助此类工具，使用 Yeoman 模板生成器从终端创建 Service Fabric 应用程序。 请执行以下步骤，确保已经有可以在计算机上运行的 Service Fabric yeoman 模板生成器。

1. 需在 Mac 上安装 Node.js 和 NPM。 如果没有安装，可使用以下命令通过 Homebrew 来安装 Node.js 和 NPM。 若要检查安装在 Mac 上的 Node.js 和 NPM 的版本，可以使用 ``-v`` 选项。

  ```bash
  brew install node
  node -v
  npm -v
  ```
2. 通过 NPM 在计算机上安装 [Yeoman](http://yeoman.io/) 模板生成器

  ```bash
  npm install -g yo
  ```
3. 请按入门[文档](service-fabric-get-started-linux.md)中的步骤，安装要使用的 Yeoman 生成器。 若要使用 Yeoman 来创建 Service Fabric 应用程序，请执行以下步骤：

  ```bash
  npm install -g generator-azuresfjava       # for Service Fabric Java Applications
  npm install -g generator-azuresfguest      # for Service Fabric Guest executables
  npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
  ```
4. 若要在 Mac 上生成 Service Fabric Java 应用程序，则需在计算机上安装 JDK 1.8 和 Gradle。


## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>为 Eclipse Neon 安装 Service Fabric 插件

Service Fabric 为**适用于 Java IDE 的 Eclipse Neon** 提供了一个插件，可简化创建、生成和部署 Java 服务的过程。 可以按照通用[文档](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon)中提及的安装步骤，安装或更新 Service Fabric Eclipse 插件。

>[!TIP]
> 默认情况下，我们支持默认 IP，如所生成应用程序的 ``Local.json`` 中的 ``Vagrantfile`` 所述。 考虑到你可能更改了该设置并使用另一 IP 部署了 Vagrant，也请在应用程序的 ``Local.json`` 中更新相应的 IP。

## <a name="next-steps"></a>后续步骤
<!-- Links -->
* [使用 Yeoman 在 Linux 上创建和部署第一个 Service Fabric Java 应用程序](service-fabric-create-your-first-linux-application-with-java.md)
* [使用适用于 Eclipse 的 Service Fabric 插件在 Linux 上创建和部署第一个 Service Fabric Java 应用程序](service-fabric-get-started-eclipse.md)
* [在 Azure 门户中创建 Service Fabric 群集](service-fabric-cluster-creation-via-portal.md)
* [使用 Azure Resource Manager 创建 Service Fabric 群集](service-fabric-cluster-creation-via-arm.md)
* [了解 Service Fabric 应用程序模型](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

