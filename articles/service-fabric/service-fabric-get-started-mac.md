---
title: "在 Mac OS X 上设置开发环境 | Microsoft Docs"
description: "安装运行时、SDK 和工具并创建本地开发群集。 完成此设置后，就可以在 Mac OS X 上开始生成应用程序。"
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: d5a04e34a3f46097b3ad1b04ac6b2b845fc946af
ms.openlocfilehash: beb869fd5c1fcc19101b16149244187c37a6d6a3


---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>在 Mac OS X 上设置开发环境
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

你可以使用 Mac OS X 生成在 Linux 群集上运行的 Service Fabric 应用程序。本文介绍了如何设置 Mac 以用于开发。

## <a name="prerequisites"></a>先决条件
Service Fabric 不在 OS X 本机上运行。为了运行本地 Service Fabric 群集，我们使用 Vagrant 和 VirtualBox 提供了预配置的 Ubuntu 虚拟机。 准备事项：

* [Vagrant（v1.8.4 或更高版本）](http://www.vagrantup.com/downloads.html)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## <a name="create-the-local-vm"></a>创建本地 VM
若要创建包含 5 节点 Service Fabric 群集的本地 VM，请执行以下步骤：

1. 克隆 **Vagrantfile** 存储库
   
    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
2. 导航到本地克隆存储库

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. （可选）修改默认的 VM 设置

    默认情况下，本地 VM 采用如下配置：

   * 分配 3 GB 内存
   * 在 IP 192.168.50.50 配置专用主机网络，以便能够从 Mac 主机传递流量

     可以更改这些设置或向 Vagrantfile 中的 VM 添加其他配置。 请参阅 [Vagrant 文档](http://www.vagrantup.com/docs)，获得配置选项的完整列表。
4. 创建 VM

    ```bash
    vagrant up
    ```
  
   此步骤包括：下载预配置的 VM 映像，在本地启动它，然后在其中设置本地 Service Fabric 群集。 完成此步骤预计需要几分钟的时间。 如果成功完成安装，输出中会出现一条消息，指示群集正在启动。
   
    ![群集安装将在 VM 设置后启动][cluster-setup-script]
    
5. 导航到 http://192.168.50.50:19080/Explorer 的 Service Fabric Explorer（假设保留了默认的专用网络 IP 地址），测试是否已正确安装群集。

    ![从主机 Mac 查看的 Service Fabric Explorer][sfx-mac]

## <a name="install-the-service-fabric-plugin-for-eclipse-neon-optional"></a>为 Eclipse Neon 安装 Service Fabric 插件（可选）
Service Fabric 为 Eclipse Neon IDE 提供了一个可简化生成和部署 Java 服务的过程的插件。

1. 在 Eclipse 中，确保已安装了 Buildship 版本 1.0.17 或更高版本。 可以通过“帮助”>“安装详细信息”检查已安装组件的版本。 可以使用[此处][buildship-update]的说明更新 Buildship。
2. 若要安装 Service Fabric 插件，请选择“帮助”>“安装新软件...”
3. 在“使用”文本框中，输入：http://dl.windowsazure.com/eclipse/servicefabric。
4. 单击“添加”。

    ![适用于 Service Fabric 的 Eclipse Neon 插件][sf-eclipse-plugin-install]
5. 选择 Service Fabric 插件，然后单击“下一步”。
6. 继续执行安装并接受最终用户许可协议。

## <a name="next-steps"></a>后续步骤
<!-- Links -->

* [创建用于 Linux 的第一个 Service Fabric 应用程序](service-fabric-create-your-first-linux-application-with-java.md)
* [在 Azure 门户中创建 Service Fabric 群集](service-fabric-cluster-creation-via-portal.md)
* [使用 Azure Resource Manager 创建 Service Fabric 群集](service-fabric-cluster-creation-via-arm.md)
* [了解 Service Fabric 应用程序模型](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship



<!--HONumber=Jan17_HO1-->


