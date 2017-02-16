---
title: "Linux 中的 Service Fabric 和部署容器 | Microsoft 文档"
description: "介绍 Service Fabric，以及如何使用 Docker 容器部署微服务应用程序。 本文介绍 Service Fabric 为容器提供的功能，以及如何在群集中部署容器映像"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4ba99103-6064-429d-ba17-82861b6ddb11
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/24/2016
ms.author: mfussell
translationtype: Human Translation
ms.sourcegitcommit: f31c8ab3d2b4fd871c92ac0e7d04bc8d5ab86830
ms.openlocfilehash: 963ca79f83d9ae4b3c5a0e5da6c5fb7985e9ca77


---
# <a name="deploy-a-docker-container-to-service-fabric"></a>将 Docker 容器部署到 Service Fabric
> [!div class="op_single_selector"]
> * [部署 Windows 容器](service-fabric-deploy-container.md)
> * [部署 Docker 容器](service-fabric-deploy-container-linux.md)
>
>

本文将指导你完成在 Linux 上的 Docker 容器中构建容器化的服务。

Service Fabric 提供多种容器功能，可帮助你构建由容器化的微服务组成的应用程序。 这些服务称为容器化服务。

功能包括：

* 容器映像部署和激活
* 资源调控
* 存储库身份验证
* 容器端口到主机端口的映射
* 容器到容器的发现和通信
* 能够配置和设置环境变量

## <a name="packaging-a-docker-container-with-yeoman"></a>使用 yeoman 打包 docker 容器
打包 Linux 上的容器时，可以选择使用 yeoman 项目模板，或者[手动创建应用程序包](service-fabric-deploy-container.md#manually)。

Service Fabric 应用程序可以包含一个或多个容器，每个容器在提供应用程序功能时都具有特定角色。 用于 Linux 的 Service Fabric SDK 包括 [Yeoman](http://yeoman.io/) 生成器，利用它可以轻松地创建第一个服务应用程序和添加容器映像。 让我们使用 Yeoman 创建有一个名为 *SimpleContainerApp* 的 Docker 容器的新应用程序。 稍后，你可以通过编辑生成的清单文件添加更多服务。

## <a name="create-the-application"></a>创建应用程序
1. 在终端中，键入 **yo azuresfguest**。
2. 对于框架，选择“容器”。
3. 为应用程序命名，例如 SimpleContainerApp
4. 提供 DockerHub 存储库中容器映像的 URL。 此映像参数采用的格式为 [repo]/[image name]

![适用于容器的 Service Fabric Yeoman 生成器][sf-yeoman]

## <a name="deploy-the-application"></a>部署应用程序
生成应用程序后，可以使用 Azure CLI 将其部署到本地群集。

1. 连接到本地 Service Fabric 群集。

    ```bash
    azure servicefabric cluster connect
    ```
2. 使用模板中提供的安装脚本可将应用程序包复制到群集的映像存储、注册应用程序类型和创建应用程序的实例。

    ```bash
    ./install.sh
    ```
3. 打开浏览器并导航到 http://localhost:19080/Explorer 的 Service Fabric Explorer（如果在 Mac OS X 上使用 Vagrant，则使用 VM 的专用 IP 替换 localhost）。
4. 展开应用程序节点，注意现在有一个条目是用于你的应用程序类型，另一个条目用于该类型的第一个实例。
5. 使用模板中提供的卸载脚本删除应用程序实例并取消注册应用程序类型。

    ```bash
    ./uninstall.sh
    ```

## <a name="adding-more-services-to-an-existing-application"></a>将更多服务添加到现有应用程序

若要将其他容器服务添加到使用 `yo` 创建的应用程序，请执行以下步骤： 
1. 将目录更改为现有应用程序的根目录。  例如，如果 `MyApplication` 是 Yeoman 创建的应用程序，则使用 `cd ~/YeomanSamples/MyApplication`。
2. 运行 `yo azuresfguest:AddService`



## <a name="next-steps"></a>后续步骤
* [Service Fabric 和容器概述](service-fabric-containers-overview.md)
* [使用 Azure CLI 与 Service Fabric 群集交互](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png



<!--HONumber=Dec16_HO1-->


