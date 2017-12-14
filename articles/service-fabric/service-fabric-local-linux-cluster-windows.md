---
title: "设置 Windows 上的 Azure Service Fabric Linux 群集 | Microsoft Docs"
description: "本文介绍如何设置在 Windows 开发计算机上运行的 Service Fabric Linux 群集。 这对于跨平台开发尤其有用。"
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: f21561269e90e3643ef5d8d48ee28712ee7f611c
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>设置 Windows 开发人员计算机上的 Linux Service Fabric 群集

本文档介绍如何在 Windows 开发计算机上设置本地 Linux Service Fabric。 设置本地 Linux 群集有助于快速测试在 Windows 计算机上开发的面向 Linux 群集的应用程序。

## <a name="prerequisites"></a>先决条件
基于 Linux 的 Service Fabric 群集不在 Windows 本机上运行。 为了运行本地 Service Fabric 群集，我们提供了预配置的 Docker 容器映像。 准备事项：

* 至少 4 GB RAM
* 最新版的 [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* 访问 Service Fabric 单机 Docker 容器[映像](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/)

>[!TIP]
> * 可以按照官方 Docker [文档](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions)中提及的步骤，在 Windows 上安装 Docker。 
> * 安装完以后，请按照[此处](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)提到的步骤验证其是否已正确安装。


## <a name="create-a-local-container-and-setup-service-fabric"></a>创建本地容器和设置 Service Fabric
要设置本地 Docker 容器并在其上运行 Service Fabric 群集，请执行以下步骤：

1. 从 Docker 中心存储库拉取映像：

    ```powershell
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. 使用以下内容更新主机上的 Docker 守护程序配置并重启 Docker 守护程序： 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    建议的更新方法是：转到 Docker 图标 >“设置”>“守护程序”>“高级”，并在该处更新。 接下来，请重启 Docker 守护程序来使更改生效。 

3. 启动带映像的 Service Fabric 单机容器实例：

    ```powershell
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    > * 可通过指定容器实例的名称，以更具可读性的方式对其进行处理。 
    > * 如果应用程序正在侦听特定端口，则必须使用附加 -p 标记指定它。 例如，如果应用程序正在侦听端口 8080，则运行 docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox servicefabricoss/service-fabric-onebox

4. 以交互式 ssh 模式登录到 Docker 容器：

    ```powershell
    docker exec -it sfonebox bash
    ```

5. 运行安装程序脚本，该脚本会提取所需的依赖项，然后启动容器中的群集。

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. 成功完成步骤 5 之后，即可从 Windows 转到 ``http://localhost:19080``，然后便会看到 Service Fabric 资源管理器。 此时，可使用 Windows 开发人员计算机上的任何工具连接到此群集，并部署面向 Linux Service Fabric 群集的应用程序。 

    > [!NOTE]
    > Windows 当前不支持 Eclipse 插件。 

## <a name="next-steps"></a>后续步骤
* [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse) 入门
* 查看其他 [Java 示例](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png