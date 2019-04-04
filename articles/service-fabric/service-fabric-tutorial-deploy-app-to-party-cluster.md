---
title: 将 Service Fabric 应用程序部署到 Azure 中的群集 | Microsoft Docs
description: 了解如何将应用程序从 Visual Studio 部署到群集。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/14/2019
ms.author: aljo,mikhegn
ms.custom: mvc
ms.openlocfilehash: 451cfde133955b987b97bc2447724d2e00010892
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667372"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>教程：将 Service Fabric 应用程序部署到 Azure 中的群集

本教程是一个系列中的第二部分。 它介绍如何将 Azure Service Fabric 应用程序部署到 Azure 中的新群集。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 创建群集。
> * 使用 Visual Studio 将应用程序部署到远程群集。

此教程系列介绍了如何：
> [!div class="checklist"]
> * [构建 .NET Service Fabric 应用程序](service-fabric-tutorial-create-dotnet-app.md)。
> * 将应用程序部署到远程群集。
> * [向 ASP.NET Core 前端服务添加 HTTPS 终结点](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)。
> * [使用 Azure Pipelines 配置 CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)。
> * [设置应用程序的监视和诊断](service-fabric-tutorial-monitoring-aspnet.md)。

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [安装 Visual Studio 2017](https://www.visualstudio.com/)，并安装 **Azure 开发**以及 **ASP.NET 和 Web 开发**工作负荷。
* [安装 Service Fabric SDK](service-fabric-get-started.md)。

## <a name="download-the-voting-sample-application"></a>下载投票示例应用程序

如果未生成[本教程系列的第一部分](service-fabric-tutorial-create-dotnet-app.md)中的投票示例应用程序，可以下载它。 在命令窗口中，运行以下代码，将示例应用程序存储库克隆到本地计算机。

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

在 Visual Studio 中打开应用程序，以管理员身份运行，然后生成应用程序。

## <a name="create-a-cluster"></a>创建群集

应用程序就绪以后，即可创建 Service Fabric 群集，然后将应用程序部署到群集。 [Service Fabric 群集](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere)是一组通过网络连接在一起的虚拟机或物理计算机，微服务会在其中部署和管理。

在本教程中，请在 Visual Studio IDE 中创建一个新的三节点型测试群集，然后将应用程序发布到该群集。 请参阅[有关创建和管理群集的教程](service-fabric-tutorial-create-vnet-and-windows-cluster.md)，了解如何创建生产群集。 也可通过 [Azure 门户](https://portal.azure.com)、[PowerShel](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)、[Azure CLI](./scripts/cli-create-cluster.md) 脚本或 [Azure 资源管理器模板](service-fabric-tutorial-create-vnet-and-windows-cluster.md)将应用程序部署到此前已创建的现有群集。

> [!NOTE]
> Voting 应用程序和许多其他的应用程序使用 Service Fabric 反向代理在服务之间通信。 通过 Visual Studio 创建的群集默认启用反向代理。 如果部署到现有的群集，则必须[在群集中启用反向代理](service-fabric-reverseproxy-setup.md)，否则 Voting 应用程序无法正常运行。


### <a name="find-the-votingweb-service-endpoint"></a>找到 VotingWeb 服务终结点

如果已按照[此教程系列的第一部分](service-fabric-tutorial-create-dotnet-app.md)中的步骤操作，则 Voting 应用程序的前端 Web 服务会在特定端口 (8080) 上进行侦听。 当应用程序部署到 Azure 中的群集时，该群集和应用程序都在 Azure 负载均衡器之后运行。 必须使用规则在 Azure 负载均衡器中打开应用程序端口。 此规则通过负载均衡器将入站流量发送到 Web 服务。 端口可以在 **VotingWeb/PackageRoot/ServiceManifest.xml** 文件的 **Endpoint** 元素中找到。 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

记下服务终结点，后续步骤中需要使用它。  如果部署到现有群集，请打开此端口，方法是使用 [PowerShell 脚本](./scripts/service-fabric-powershell-open-port-in-load-balancer.md)在 Azure 负载均衡器中创建负载均衡规则和探测，或者在 [Azure 门户](https://portal.azure.com)中使用此群集的负载均衡器。

### <a name="create-a-test-cluster-in-azure"></a>在 Azure 中创建测试群集
在“解决方案资源管理器”中，右键单击“Voting”并选择“发布”。

在“连接终结点”中，选择“创建新群集”。  如果部署到现有的群集，请从列表中选择群集终结点。  此时会打开“创建 Service Fabric 群集”对话框。

在“群集”选项卡中，输入**群集名称**（例如“mytestcluster”），选择订阅，选择群集的区域（例如“美国中南部”），输入群集节点的数目（对于测试群集，建议使用三节点），然后输入资源组（例如“mytestclustergroup”）。 单击“下一步”。

![创建群集](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

在“证书”选项卡中，输入群集证书的密码和输出路径。 自签名证书创建为 PFX 文件并保存到指定的输出路径。  使用证书是为了确保节点到节点和客户端到节点的安全。  自签名证书不应用于生产群集。  此证书由 Visual Studio 用于对群集进行身份验证，以及用于部署应用程序。 选择“导入证书”，以便将 PFX 安装在计算机的 CurrentUser\My certificate 存储中。  单击“下一步”。

![创建群集](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

在“VM 详细信息”选项卡中，输入群集管理员帐户的“用户名”和“密码”。  选择群集节点的“虚拟机映像”，以及每个群集节点的“虚拟机大小”。  单击“高级”选项卡。

![创建群集](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

在“端口”中，输入上一步的 VotingWeb 服务终结点（例如 8080）。  创建群集以后，这些应用程序端口会在 Azure 负载均衡器中打开，这样就可以将流量转发到群集。  单击“创建”即可创建群集，这需要几分钟的时间。

![创建群集](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>将应用程序发布到群集

新群集就绪以后，即可直接通过 Visual Studio 部署 Voting 应用程序。

在“解决方案资源管理器”中，右键单击“Voting”并选择“发布”。 此时会显示“发布”对话框。

在“连接终结点”中，选择在上一步创建的群集的终结点。  例如，“mytestcluster.southcentral.cloudapp.azure.com:19000”。 如果选择“高级连接参数”，则会自动填充证书信息。  
![发布 Service Fabric 应用程序](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

选择“发布”。

应用程序部署完以后，请打开浏览器并输入群集地址，后跟 **:8080**。 或者输入另一端口（如果已配置一个）。 例如 `http://mytestcluster.southcentral.cloudapp.azure.com:8080`。 会看到应用程序在 Azure 群集中运行。 在投票网页中，尝试添加和删除投票选项，并针对这些选项中的一个或多个进行投票。

![Service Fabric 投票示例](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>后续步骤
本教程的此部分介绍了如何：

> [!div class="checklist"]
> * 创建群集。
> * 使用 Visual Studio 将应用程序部署到远程群集。

转到下一教程：
> [!div class="nextstepaction"]
> [启用 HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
