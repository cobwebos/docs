---
title: "在本教程中，你创建 Azure 堆栈 VM 使用模板 |Microsoft 文档"
description: "描述如何使用 ASDK 使用 predfined 模板和 GitHub 自定义模板创建 VM。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: ec44fc879abfe2a457e80f27db972ac4d7570dbd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>教程： 创建使用社区模板的 VM
作为 Azure 堆栈操作员或用户，你可以创建 VM using[自定义的 GitHub 快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates)而不是一个从 Azure 堆栈应用商店中手动部署。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 了解 Azure 堆栈快速入门模板 
> * 使用自定义的 GitHub 模板创建 VM
> * 启动 minikube 并安装应用程序

## <a name="learn-about-azure-stack-quickstart-templates"></a>了解 Azure 堆栈快速入门模板
Azure 堆栈快速入门模板存储在[公共 AzureStack 快速入门模板存储库](https://github.com/Azure/AzureStack-QuickStart-Templates)GitHub 上。 此存储库包含已经过测试与 Microsoft Azure 堆栈开发工具包 (ASDK) 的 Azure 资源管理器部署模板。 你可以使用它们便于你评估 Azure 堆栈并使用 ASDK 环境。 

随着时间的推移许多 GitHub 用户提供到存储库，从而导致了内容丰富的 400 多个部署模板。 此存储库是一个很好的起始点，以更好地了解如何将各种类型的环境部署到 Azure 堆栈。 

>[!IMPORTANT]
> 某些这些模板创建的是由社区的成员而不是 Microsoft。 每个模板是由其所有者，非 Microsoft 的许可许可协议。 Microsoft 不负责这些模板，并且不会不承担安全性、 兼容性或性能。 社区模板不支持在任何 Microsoft 的支持计划或服务下,，提供"按原样"没有任何形式的保证。

如果你想要参与到 GitHub 你 Azure 资源管理器模板，请对你贡献[azure 快速入门模板存储库](https://github.com/Azure/AzureStack-QuickStart-Templates)。

若要了解有关 GitHub 存储库，以及如何参与到它的详细信息，请参阅[存储库的自述文件](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md)。 


## <a name="create-a-vm-using-a-custom-github-template"></a>使用自定义的 GitHub 模板创建 VM
在此示例教程中， [101 vm linux minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube)使用 Azure 堆栈快速入门模板来部署 Ubuntu 16.04 虚拟机上 AzureStack 运行 Minikube kubenetes 群集进行管理。

Minikube 是一个工具，可以轻松地在本地运行 Kubernetes。 Minikube 运行用户想要试用 Kubernetes 或开发它的 VM 内的单节点 Kubernetes 群集日常。 它支持很简单，Linux VM 上运行的一个节点 Kubernetes 群集。 它是最快和最直截了当方法获取完全正常运行 Kubernetes 群集运行。 它允许开发人员开发和测试在他们的本地计算机上其 Kubernetes 基于应用程序部署。 在体系结构方面，Minikube VM Master 和代理节点组件将在本地运行：
- 例如 API Server、 计划程序，以及 etcd 服务器的主节点组件在单个调用 LocalKube Linux 进程运行。
- 代理节点组件是在 docker 容器内运行，这是完全按照它们将在正常的代理节点上运行。 从应用程序的部署角度，没有任何区别 Minikube 或正则 Kubernetes 群集上部署应用程序时。

此模板将安装以下组件：

- Ubuntu 16.04 LTS VM
- [Docker-CE](https://download.docker.com/linux/ubuntu) 
- [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
- [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
- xFCE4
- xRDP

> [!IMPORTANT]
> Ubuntu VM 映像 (Ubuntu Server 16.04 LTS 在此示例中) 必须已被[添加到 Azure 堆栈 marketplace](asdk-marketplace-item.md)在开始这些步骤之前。

1.  单击**+ 新建** > **自定义** > **模板部署**。

    ![](media/asdk-create-vm-template/1.PNG) 

2. 单击**编辑模板**。

   ![](media/asdk-create-vm-template/2.PNG) 

3.  单击**快速入门模板**。

       ![](media/asdk-create-vm-template/3.PNG)

4. 选择**101 vm linux minikube**从可用模板使用**选择模板**下拉列表，，然后单击**确定**。  

   ![](media/asdk-create-vm-template/4.PNG)

5. 如果你想要对模板 JSON 可以这样，如果没有，也可以完成后，单击进行修改**保存**以关闭编辑模板对话框。

   ![](media/asdk-create-vm-template/5.PNG) 

6.  单击**参数**，填写或根据需要，修改可用的字段，然后单击**确定**。 选择要使用，创建或选择一个现有的资源组名称，，然后单击的订阅**创建**启动模板部署。

       ![](media/asdk-create-vm-template/6.PNG)

7. 选择要使用，创建或选择一个现有的资源组名称，，然后单击的订阅**创建**启动模板部署。

   ![](media/asdk-create-vm-template/7.PNG)

8. 资源组部署需要几分钟才能创建自定义的基于模板的 VM。 你可以监视通过通知和资源组属性的安装状态。 

   ![](media/asdk-create-vm-template/8.PNG)

   >[!NOTE]
   > 部署完成后，VM 将运行。 

## <a name="start-minikube-and-install-an-application"></a>启动 minikube 并安装应用程序
现在，已成功创建 Linux VM，你可以在登录以启动 minikube 并安装应用程序。 

1. 在部署完成后，单击**连接**以查看可用于连接到 Linux VM 的公共 IP 地址。 

   ![](media/asdk-create-vm-template/9.PNG)

2. 从提升的命令提示符，运行**mstsc.exe**若要打开远程桌面连接并连接到 Linux VM 的公共 IP 地址在上一步中发现。 当系统提示登录到 xRDP，使用创建 VM 时指定的凭据。

   ![](media/asdk-create-vm-template/10.PNG)

3. 打开终端仿真程序并输入以下命令以启动 minikube:

    >    `sudo minikube start --vm-driver=none`
    >   
    >    `sudo minikube addons enable dashboard`
    >    
    >    `sudo minikube dashboard --url`

   ![](media/asdk-create-vm-template/11.PNG)

4. 打开 web 浏览器并访问 kubernetes 仪表板地址。 祝贺你，你现在具有完全 kubernetes 安装有效使用 minikube ！

   ![](media/asdk-create-vm-template/12.PNG)

5. 如果你想要部署示例应用程序，请访问 kubernetes 的正式文档页，跳过"创建 Minikube 群集"部分，因为你已创建一个更高版本。 只需跳转到部分中"创建 Node.js 应用程序"在https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 了解 Azure 堆栈快速入门模板 
> * 使用自定义的 GitHub 模板创建 VM
> * 启动 minikube 并安装应用程序


> [!div class="nextstepaction"]
> [了解高级的评估任务](asdk-advanced-eval.md)