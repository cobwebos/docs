---
title: "将 Azure Service Fabric 应用程序部署到合作群集 | Microsoft Docs"
description: "了解如何将应用程序部署到合作群集。"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/03/2017
ms.author: mikhegn
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 1fde062d9673a48698b269d79e009c7b179e5934
ms.contentlocale: zh-cn
ms.lasthandoff: 07/15/2017

---

# <a name="deploy-an-application-to-a-party-cluster-in-azure"></a>将应用程序部署到 Azure 中的合作群集
本教程介绍如何将 Azure Service Fabric 应用程序部署到 Azure 中的合作群集。

本教程部分介绍如何：
> [!div class="checklist"]
> * 使用 Visual Studio 将应用程序部署到远程群集
> * 使用 Service Fabric Explorer 从群集中删除应用程序

本教程已拆分为三篇文章，本文是教程系列中的第二篇文章。

## <a name="prerequisites"></a>先决条件
在开始学习本教程之前：
- 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [安装 Visual Studio 2017](https://www.visualstudio.com/)，并安装 **Azure 开发**以及 **ASP.NET 和 Web 开发**工作负荷。
- [安装 Service Fabric SDK](service-fabric-get-started.md)

## <a name="set-up-a-party-cluster"></a>设置合作群集
合作群集是在 Azure 上托管的、由 Service Fabric 团队运行的免费限时 Service Fabric 群集，任何人都可以在其中部署应用程序及了解平台的情况。 免费！

若要获取合作群集的访问权限，请浏览到此站点：http://aka.ms/tryservicefabric，并遵照说明获取群集访问权限。 需要一个 Facebook 或 GitHub 帐户才能获取合作群集的访问权限。

> [!NOTE]
> 合作群集不受保护，因此，在其中放置的应用程序和任何数据可能会被其他人看到。 请勿部署不希望其他人看到的任何内容。 请务必仔细阅读我们的使用条款，了解所有详细信息。

## <a name="make-your-application-ready-for-deployment"></a>让应用程序做好部署准备
由于我们的 ASP.NET Core Web API 服务充当此应用程序的前端并接受外部流量，因此，我们要将此服务绑定到已知的固定端口。 在服务的 **ServiceManifest.xml** 文件中指定端口。

1. 在解决方案资源管理器中，打开“WebAPIFrontEnd”->“PackageRoot”->“ServiceManifest.xml”。
2. 将现有 **Endpoint** 元素的 **Port** 属性更改为 **80**，保存更改。

## <a name="deploy-the-app-to-the-azure"></a>将应用部署到 Azure
应用程序准备就绪后，可以直接从 Visual Studio 将其部署到合作群集。

1. 在解决方案资源管理器中右键单击“MyApplication”，选择“发布”。

    ![发布对话框](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

2. 在“连接终结点”字段中键入合作群集的连接终结点，单击“发布”。

    完成发布后，应该可以通过浏览器向应用程序发送请求。

3. 打开首选的浏览器，键入群集地址（不带端口信息的连接终结点 - 例如 win1kw5649s.westus.cloudapp.azure.com），将 `/api/values` 添加到 URL。

    现在，应会看到在本地运行该应用程序时所看到的相同结果。

    ![来自群集的 API 响应](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="remove-the-application-from-a-cluster-using-service-fabric-explorer"></a>使用 Service Fabric Explorer 从群集中删除应用程序
Service Fabric Explorer 是用于浏览和管理 Service Fabric 群集中的应用程序的图形用户界面。

若要删除已部署到合作群集的应用程序，请执行以下操作：

1. 使用合作群集注册页提供的链接浏览到 Service Fabric Explorer。 例如 http://win1kw5649s.westus.cloudapp.azure.com:19080/Explorer/index.html。

2. 在 Service Fabric Explorer 中，导航到左侧树视图中的“fabric://MyApplication”节点。

3. 在右侧的“概要”窗格中单击“操作”按钮，选择“删除应用程序”。 确认删除该应用程序实例，随即会删除群集中运行的应用程序实例。

![在 Service Fabric Explorer 中删除应用程序](./media/service-fabric-tutorial-deploy-app-to-party-cluster/delete-application.png)

应用程序在 Service Fabric 群集中部署为应用程序类型，因此，我们可以在群集中运行该应用程序的多个实例和版本。 删除正在运行的应用程序实例后，还可以删除类型，以完成部署的清理。

有关 Service Fabric 中的应用程序模型的详细信息，请参阅[在 Service Fabric 中为应用程序建模](service-fabric-application-model.md)。

1. 在树视图中导航到“MyApplicationType”节点。

2. 在右侧的“概要”窗格中单击“操作”按钮，选择“取消预配类型”。 确认取消预配应用程序类型。

![在 Service Fabric Explorer 中取消预配应用程序类型](./media/service-fabric-tutorial-deploy-app-to-party-cluster/unprovision-type.png)

本教程到此结束。

## <a name="next-steps"></a>后续步骤
在本教程中，你已学习了如何执行以下操作：

> [!div class="checklist"]
> * 使用 Visual Studio 将应用程序部署到远程群集
> * 使用 Service Fabric Explorer 从群集中删除应用程序

转到下一教程：
> [!div class="nextstepaction"]
> [使用 Visual Studio Team Services 设置持续集成](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
