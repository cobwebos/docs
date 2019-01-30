---
title: 本教程介绍如何使用模板创建 Azure Stack VM | Microsoft Docs
description: 介绍如何使用 ASDK 通过预定义模板和 GitHub 自定义模板来创建 VM。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 11/13/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 9ccdea6ca0bf46bbc19e5a7e19ac2d3527138345
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241774"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>教程：使用社区模板创建 VM

作为 Azure Stack 操作员或用户，你可以使用[自定义 GitHub 快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)来创建 VM，不需通过 Azure Stack 市场来手动进行部署。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 Azure Stack 快速入门模板 
> * 使用自定义 GitHub 模板创建 VM
> * 启动 minikube 并安装应用程序

## <a name="azure-stack-quickstart-templates"></a>Azure Stack 快速入门模板

Azure Stack 快速入门模板存储在 GitHub 上的[公共 Azure Stack 快速入门模板存储库](https://github.com/Azure/AzureStack-QuickStart-Templates)中。 此存储库包含已经过测试与 Microsoft Azure Stack 开发工具包 (ASDK) 的 Azure 资源管理器部署模板。 可以通过这些模板更轻松地评估 Azure Stack 并使用 ASDK 环境。 

许多 GitHub 用户不断地向存储库贡献模板，集合中的部署模板数已超过 400 个。 可以从此存储库着手，更好地了解如何将各种环境部署到 Azure Stack。 

>[!IMPORTANT]
> 这些模板中的部分模板是由社区成员创建的，不是由 Microsoft 创建的。 每个模板按照其所有者而非 Microsoft 的许可协议进行许可。 Microsoft 不对这些模板负责，也不检查其安全性、符合性或性能方面的问题。 社区模板不受任何 Microsoft 支持计划或服务，并提供"按原样，"无任何形式的保证。

若要将你的 Azure 资源管理器模板贡献给 GitHub，请将其添加到 [AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates) 存储库。 若要详细了解此存储库，以及如何向其贡献内容，请参阅[自述文件](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md)。 

## <a name="create-a-vm-using-a-custom-github-template"></a>使用自定义 GitHub 模板创建 VM

在本示例教程中，[101-vm-linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) Azure Stack 快速入门模板用于将 Ubuntu 16.04 虚拟机部署到运行 Minikube 来管理 Kubernetes 群集的 AzureStack。

Minikube 是一种工具，方便用户在本地运行 Kubernetes。 Minikube 在 VM 中运行单节点 Kubernetes 群集，使你可以试用 Kubernetes 或使用它进行日常开发。 它支持在 Linux VM 上运行的简单的单节点 Kubernetes 群集。 Minikube 是运行功能齐全的 Kubernetes 群集的最快、最直接的方法。 开发人员可以使用它在本地计算机上开发和测试基于 Kubernetes 的应用程序部署。 从体系结构来说，Minikube VM 同时在本地运行主节点组件和代理节点组件：

- 主节点组件（例如 API 服务器、计划程序和 [etcd 服务器](https://coreos.com/etcd/)）是在名为 LocalKube 的单个 Linux 进程中运行。
- 代理节点组件是在 Docker 容器中运行，运行方式与在正常代理节点上的运行方式一样。 从应用程序部署角度来看，将应用程序部署在 Minikube 上与部署在常规 Kubernetes 群集上并无区别。

此模板安装以下组件：

- Ubuntu 16.04 LTS VM
- [Docker-CE](https://download.docker.com/linux/ubuntu) 
- [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
- [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
- xFCE4
- xRDP

> [!IMPORTANT]
> 在执行这些步骤之前，必须已将 Ubuntu VM 映像（在此示例中为 Ubuntu Server 16.04 LTS）添加到 Azure Stack 市场。

1.  依次选择“+ 创建资源”、“自定义”、“模板部署”。

    ![](media/azure-stack-create-vm-template/1.PNG) 

2. 选择“编辑模板”。

    ![](media/azure-stack-create-vm-template/2.PNG) 

3.  选择“快速入门模板”。

    ![](media/azure-stack-create-vm-template/3.PNG)

4. 使用“选择模板”下拉列表从可用模板中选择 **101-vm-linux-minikube**，然后单击“确定”。  

    ![](media/azure-stack-create-vm-template/4.PNG)

5. 若要对模板 JSON 进行修改，可以这样做。 如果不想要进行修改或者已完成操作，请选择“保存”关闭“编辑模板”对话框。

    ![](media/azure-stack-create-vm-template/5.PNG) 

6.  选择“参数”，根据需要填充或修改适用的字段，然后单击“确定”。 选择要使用的订阅，创建一个资源组名称或选择一个现有的名称，然后选择“创建”以启动模板部署。

    ![](media/azure-stack-create-vm-template/6.PNG)

7. 选择要使用的订阅，创建一个资源组名称或选择一个现有的名称，然后选择“创建”以启动模板部署。

    ![](media/azure-stack-create-vm-template/7.PNG)

8. 资源组部署需要数分钟的时间来创建基于模板的自定义 VM。 可以通过通知和资源组属性来监视安装状态。 

    ![](media/azure-stack-create-vm-template/8.PNG)

    >[!NOTE]
    > 部署完成后，VM 将处于运行状态。 

## <a name="start-minikube-and-install-an-application"></a>启动 Minikube 并安装应用程序

成功创建 Linux VM 后，可以登录，以便启动 Minikube 并安装应用程序。 

1. 部署完成后，请选择“连接”，以查看要用来连接到 Linux VM 的公共 IP 地址。 

    ![](media/azure-stack-create-vm-template/9.PNG)

2. 在提升的命令提示符下运行 **mstsc.exe**，打开远程桌面连接并连接到在上一步发现的 Linux VM 的公共 IP 地址。 当系统提示你登录 xRDP 时，请使用在创建 VM 时指定的凭据。

    ![](media/azure-stack-create-vm-template/10.PNG)

3. 打开终端仿真器并输入以下命令，以启动 Minikube：

    ```shell
    sudo minikube start --vm-driver=none
    sudo minikube addons enable dashboard
    sudo minikube dashboard --url
    ```

    ![](media/azure-stack-create-vm-template/11.PNG)

4. 打开 Web 浏览器，访问 Kubernetes 仪表板地址。 祝贺你，现已使用 Minikube 完成了一个功能完全正常的 Kubernetes 安装！

    ![](media/azure-stack-create-vm-template/12.PNG)

5. 若要部署示例应用程序，请访问 Kubernetes 的官方文档页，跳过“创建 Minikube 群集”部分，因为已在上面创建了一个。 跳到“创建 Node.js 应用程序”部分，网址为 https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 了解 Azure Stack 快速入门模板 
> * 使用自定义 GitHub 模板创建 VM
> * 启动 minikube 并安装应用程序

