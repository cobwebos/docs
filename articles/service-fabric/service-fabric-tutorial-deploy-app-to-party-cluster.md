---
title: 将 Azure Service Fabric 应用程序从 Visual Studio 部署到群集 | Microsoft Docs
description: 了解如何将应用程序从 Visual Studio 部署到群集
services: service-fabric
documentationcenter: .net
-author: mikkelhegn
-manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: 1d8f8d903046f1d471f7abbe08a957b81522e391
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-deploy-an-application-to-a-service-fabric-cluster-in-azure"></a>教程：将应用程序部署到 Azure 中的 Service Fabric 群集
本教程是一个系列的第二部分，介绍如何将 Azure Service Fabric 应用程序直接从 Visual Studio 部署到 Azure 中的新群集。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 通过 Visual Studio 创建群集
> * 使用 Visual Studio 将应用程序部署到远程群集


此教程系列介绍了如何：
> [!div class="checklist"]
> * [构建 .NET Service Fabric 应用程序](service-fabric-tutorial-create-dotnet-app.md)
> * 将应用程序部署到远程群集
> * [使用 Visual Studio Team Services 配置 CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [设置监视和诊断应用程序](service-fabric-tutorial-monitoring-aspnet.md)


## <a name="prerequisites"></a>先决条件
在开始学习本教程之前：
- 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [安装 Visual Studio 2017](https://www.visualstudio.com/)，并安装 **Azure 开发**以及 **ASP.NET 和 Web 开发**工作负荷。
- [安装 Service Fabric SDK](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>下载投票示例应用程序
如果未生成[本教程系列的第一部分](service-fabric-tutorial-create-dotnet-app.md)中的投票示例应用程序，可以下载它。 在命令窗口中，运行以下命令，将示例应用程序存储库克隆到本地计算机。

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="deploy-the-sample-application"></a>部署示例应用程序

### <a name="select-a-service-fabric-cluster-to-which-to-publish"></a>选择要向其发布内容的 Service Fabric 群集
至此，应用程序已准备就绪，可以直接通过 Visual Studio 将它部署到群集了。

有两个部署选项：
- 通过 Visual Studio 创建群集。 可以通过此选项使用首选的配置直接从 Visual Studio 创建安全的群集。 此类群集适用于测试方案。可以先创建群集，然后在 Visual Studio 中将内容直接发布到该群集。
- 发布到订阅中的现有群集。

本教程将按步骤从 Visual Studio 创建群集。 对于其他选项，可以复制并粘贴连接终结点，或者从订阅中进行选择。
> [!NOTE]
> 许多服务使用反向代理来互相通信。 通过 Visual Studio 创建的群集以及合作群集默认启用反向代理。  如果使用现有的群集，则必须[在群集中启用反向代理](service-fabric-reverseproxy.md#setup-and-configuration)。

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>将应用部署到 Service Fabric 群集
1. 在解决方案资源管理器中右键单击应用程序项目，选择“发布”。

2. 使用 Azure 帐户登录，以便访问订阅。 如果使用的是合作群集，则此步骤为可选步骤。

3. 选择**连接终结点**对应的下拉列表，然后选择“<Create New Cluster...>”选项。
    
    ![发布对话框](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)
    
4. 在“创建群集”对话框中，修改以下设置：

    1. 在“群集名称”字段中指定群集的名称，并指定要使用的订阅和位置。
    2. 可选：可以修改节点数。 默认有三个节点，这是测试 Service Fabric 方案的最低要求。
    3. 选择“证书”选项卡。在此选项卡中键入一个密码，用于确保群集证书的安全。 此证书有助于确保群集的安全。 也可修改用于保存证书的路径。 Visual Studio 还可以为你导入证书，因为这是将应用程序发布到群集的必需步骤。
    4. 选择“VM 详细信息”选项卡。指定一个密码，以便将其用于构成群集的虚拟机 (VM)。 可以使用用户名和密码远程连接到 VM。 此外还必须选择 VM 大小，并可根据需要更改 VM 映像。
    5. 可选：在“高级”选项卡上，可以修改要在负载均衡器上打开的端口的列表，该均衡器将与群集一起创建。 还可以添加现有的 Application Insights 密钥，用于路由应用程序日志文件。
    6. 修改完设置以后，选择“创建”按钮。 需要数分钟才能创建完毕；输出窗口会指示群集何时完全创建好。
    
    ![“创建群集”对话框](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

4. 要使用的群集就绪以后，右键单击应用程序项目，然后选择“发布”。

    完成发布后，应该可以通过浏览器向应用程序发送请求。

5. 打开首选的浏览器，键入群集地址（不含端口信息的连接终结点 - 例如 win1kw5649s.westus.cloudapp.azure.com）。

    现在，应会看到在本地运行该应用程序时所看到的相同结果。

    ![来自群集的 API 响应](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 通过 Visual Studio 创建群集
> * 使用 Visual Studio 将应用程序部署到远程群集

转到下一教程：
> [!div class="nextstepaction"]
> [使用 Visual Studio Team Services 设置持续集成](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
