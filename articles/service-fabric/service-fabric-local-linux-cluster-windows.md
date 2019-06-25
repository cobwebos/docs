---
title: 设置 Windows 上的 Azure Service Fabric Linux 群集 | Microsoft Docs
description: 本文介绍如何设置在 Windows 开发计算机上运行的 Service Fabric Linux 群集。 这对于跨平台开发尤其有用。
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: e700250a6ebcdb82f99c1b460a510811d7ceb96c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60719934"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>设置 Windows 开发人员计算机上的 Linux Service Fabric 群集

本文档介绍如何在 Windows 开发计算机上设置本地 Linux Service Fabric。 设置本地 Linux 群集有助于快速测试在 Windows 计算机上开发的面向 Linux 群集的应用程序。

## <a name="prerequisites"></a>必备组件
基于 Linux 的 Service Fabric 群集不在 Windows 本机上运行。 为了运行本地 Service Fabric 群集，我们提供了预配置的 Docker 容器映像。 准备事项：

* 至少 4 GB RAM
* 最新版的 [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker 必须在 Linux 模式下运行

>[!TIP]
> * 可以按照官方 Docker [文档](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions)中提及的步骤，在 Windows 上安装 Docker。 
> * 安装完以后，请按照[此处](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)提到的步骤验证其是否已正确安装。


## <a name="create-a-local-container-and-setup-service-fabric"></a>创建本地容器和设置 Service Fabric
若要设置本地 Docker 容器并在其上运行 Service Fabric 群集，请在 PowerShell 执行以下步骤：


1. 使用以下内容更新主机上的 Docker 守护程序配置并重启 Docker 守护程序： 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    建议的更新方法是：转到 Docker 图标 >“设置”>“守护程序”>“高级”，并在该处更新。 接下来，请重启 Docker 守护程序来使更改生效。 

2. 在新目录中创建名为 `Dockerfile` 的文件，以生成 Service Fabric 映像：

    ```Dockerfile
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

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >此操作需要一段时间，但只需执行一次。

4. 现在，每当有需要时，都可以运行以下命令，快速启动 Service Fabric 的本地副本：

    ```powershell 
    docker run --name sftestcluster -d -v //var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >为容器实例提供一个名称，以更具可读性的方式对其进行处理。 
    >
    >如果应用程序正在侦听特定端口，则必须使用附加的 `-p` 标记指定这些端口。 例如，如果应用程序正在侦听端口 8080，请添加下面的 `-p` 标记：
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. 群集需要一小段时间来启动，可以使用以下命令查看日志，或者通过 [http://localhost:19080](http://localhost:19080) 跳转到仪表板来查看群集运行状况：

    ```powershell 
    docker logs sftestcluster
    ```

6. 成功完成步骤 5 之后，即可从 Windows 转到 ``http://localhost:19080``，然后便会看到 Service Fabric 资源管理器。 此时，可使用 Windows 开发人员计算机上的任何工具连接到此群集，并部署面向 Linux Service Fabric 群集的应用程序。 

    > [!NOTE]
    > Windows 当前不支持 Eclipse 插件。 

7. 完成后，可使用以下命令来停止并清理容器：

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>已知限制 
 
 以下是在 Mac 的容器中运行的本地群集的已知限制： 
 
 * DNS 服务无法运行且不受支持 [问题 #132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>后续步骤
* [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse) 入门
* 查看其他 [Java 示例](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
