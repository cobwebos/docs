---
title: 将微聚焦企业服务器5.0 部署到 AKS |Microsoft Docs
description: 在 Azure 虚拟机 (Vm) 上使用微焦点开发和测试环境 Rehost IBM z/OS 大型机工作负荷。
services: virtual-machines-linux
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 6780942d922f885c7afebd8e64f4f28654c3800e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87042547"
---
# <a name="deploy-micro-focus-enterprise-server-50-to-aks"></a>将微聚焦企业服务器5.0 部署到 AKS

在另一 [篇文章](./run-enterprise-server-container.md)中，我介绍了在 Docker 容器中运行微聚焦企业服务器5.0 的步骤。 为此，我要向您展示如何进一步执行此操作，并将创建的 Docker 映像部署到 Azure Kubernetes Service (AKS) 。

Azure Kubernetes 服务是基于 Kubernetes 的托管业务流程服务。 它使你能够在容器主机的群集中部署、缩放和管理 (和其他基于容器的应用程序) 的 Docker 容器。

这是一个三步过程。 你需要：

1.  创建用于存储 Docker 映像的 Azure 容器注册表。

2.  创建 Azure Kubernetes 群集以运行 Docker 映像。

3.  运行该应用程序。

这使你可以在 Azure 中横向扩展 (和缩减) 你的大型机现代化工作负载，从而真正利用云平台。

准备好? 让我们开始吧！

## <a name="create-the-azure-container-registry"></a>创建 Azure 容器注册表

在 Azure 门户中，选择左上角的 " **创建资源** "。 在 Marketplace 面板中，依次选择 " **容器"、"** **容器注册表**"。 这会转到 " **创建容器注册表** " 窗格，需要在其中填写 **注册表名称**、 **Azure 订阅**、 **资源组**和 **位置**。 需要解析 **注册表名称** ，因此该名称必须是唯一的。 选择以前的博客文章和相同的相应**位置**中使用的**资源组**。 为 "**管理员用户**" 选择 "**启用**"，为**SKU**选择 "**基本**"。 填写全部内容后，选择 " **创建**"。

![创建容器注册表接口](media/deploy-image-1.png)

部署注册表后，选择 " **前往资源**"。 这会转到 Azure 容器注册表的主边栏选项卡。 下面是 **快速入门** 菜单选项的一项不错的功能。 选择它，您将看到有关在注册表中推送和提取图像需要执行的操作的说明。 我们来看看以下内容：

1.  **安装 Docker** –您无需担心这一点，因为它已经完成。

2.  再次**运行 "hello – world" 基本映像**，这并不是必需的，但可随时试用。

3.  **登录到容器注册表** -需要从虚拟机 (VM) 执行此操作。 将命令复制到剪贴板或记事本。

    对于创建的注册表，命令为： **docker login acrmf50.azurecr.io**

4.  **推送到注册表** -如果已登录到 VM，则需要执行此操作。

5.  **从注册表提取** –此演练并不相关，但要了解是否需要运行另一个 Docker 映像。

离开门户之前，需要获取注册表的凭据，以便登录。 退出 " **快速入门** " 边栏选项卡，然后从 "注册表" 菜单中选择 " **访问密钥** "。 复制 **用户名** 和某个 **密码** (剪贴板或记事本有两个) 。 稍后你将需要它们来登录。

现在，你已了解需要执行的操作，请登录到该 VM。

## <a name="rdp-to-the-virtual-machine-you-used-to-create-the-docker-image"></a>使用 RDP 连接到用于创建 Docker 映像的虚拟机

由于已在 Windows 2016 服务器上创建 Docker 映像，因此需要登录到该 VM。 通过此 VM，你可以将映像推送到刚刚创建的 Azure 容器注册表。 导航到 Azure 门户中的 VM，然后选择 " **概述** "，然后单击 " **连接**"。 这会通过远程桌面协议 (RDP) 将你连接到 VM。 你需要使用创建 VM 时使用的凭据。

## <a name="log-in-and-push-the-image-to-the-registry"></a>登录并将映像推送到注册表

登录后，打开命令提示符并启动以下 Docker 命令：

-   **docker 映像** –此列表显示 VM 上所有当前已安装的映像的列表。 记下 **microfocus/acctdemo** ，因为这是你将使用的那个。

-   **docker login acrmf50.azurecr.io** –下面是*docker 登录名 \<registry name\> *的正确格式。 替换在创建注册表时使用的任何名称。

    -   需要从 Azure 门户复制的 **用户名** 和 **密码** 。 显示的内容应与下图类似。

    ![管理员命令提示符屏幕截图](media/deploy-image-2.png)

-   **docker 标记 microfocus/es-acctdemo acrmf50.azurecr.io/es-acctdemo** –这会用存储库名称标记相应的映像。 **注意**：如果名称不 \<microfocus/es-acctdemo\> 起作用，请尝试使用完整映像 ID。 执行命令后，键入 **docker images – trunc**。 应会看到下图所示的内容。 请注意，该图像已正确标记。

    ![选择管理员命令提示屏幕](media/deploy-image-3.png)

-   **docker push acrmf50.azurecr.io/es-acctdemo** –这将启动到存储库的实际推送。 由于大小为 15 GB，因此运行需要几分钟时间。 如果一切正常，应会看到类似于下图的内容。

    ![管理员命令提示屏幕](media/deploy-image-4.png)

现在，返回到 "Azure 门户"，具体来说就是 **存储库**。 在 **存储库**的菜单中，选择 " **存储库**"，应会看到 " **acctdemo** " 列。 现在创建 AKS 群集。

## <a name="create-the-azure-kubernetes-aks-cluster"></a>创建 Azure Kubernetes (AKS) 群集

从 "Azure 门户" 中，从 " **Marketplace** " 菜单中选择 "**创建资源**"，然后选择 "**容器/Kubernetes 服务**"。 接下来，需要填写 " **创建 Kubernetes 群集** " 边栏选项卡。 请确保在使用的同一区域和资源组中保留群集。 除了 **节点计数之外，** 还可以接受其余默认值，只需为1。 完成操作后，选择“查看 + 创建”。

![创建 Kubernetes 群集屏幕](media/deploy-image-5.png)

完成后，部署会将 **Kubernetes 服务** 项目放在你在边栏选项卡上选择的 **资源组** 中。 但是，在部署过程中，实际群集会创建自己的资源组。 如果从左侧菜单中选择 " **资源组** "，则可以基于命名约定找到它。 下面是 "我的图片"，它是列表中的最后一个映像。

![资源组的屏幕截图](media/deploy-image-6.png)

**运行映像**

现在是时候拉动映像并在 AKS 中运行它了。 从 Azure 门户执行此操作的最简单方法是使用 Cloud Shell。 你会在 Azure 门户的右上角找到图标。 请注意，在本演练中，我使用的是 Bash Shell。

!["Cloud Shell" 图标的屏幕截图](media/deploy-image-7.png)

加载 shell 后，键入以下命令：

**kubectl 运行 acctdemo--acrmf50.azurecr.io/es-acctdemo--port = 9040**

这会从 **Acrmf50.azurecr.io** 存储库中提取映像，并将其加载到 AKS 中。 然后，它会运行打开了端口9040的映像。 你可能会想起这是你为 Docker 映像打开的端口。 你需要访问企业服务器。

Kubernetes 应使用已创建部署的消息进行响应。

![部署消息的屏幕截图](media/deploy-image-8.jpg)

若要查看容器是否确实正在运行，请键入： **kubectl get**pod。

你应看到 acctdemo 作为正在运行的 pod，如下图所示。

![将 acctdemo 作为正在运行的 pod 屏幕截图](media/deploy-image-9.png)

祝贺你！ 你现在正在 Azure Kubernetes 服务中运行企业服务器。 在下一篇文章中，我将向您演示如何访问企业服务器管理控制台，以及如何利用 Kubernetes 来扩展部署。
