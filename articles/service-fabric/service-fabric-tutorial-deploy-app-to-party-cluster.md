---
title: 将 Service Fabric 应用程序部署到 Azure 中的群集 | Microsoft Docs
description: 了解如何将应用程序从 Visual Studio 部署到群集。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: 81cd4d247ba6153fd205ead36f29a52b420bb427
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502822"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>教程：将 Service Fabric 应用程序部署到 Azure 中的群集

本教程是一个系列的第二部分，介绍如何将 Azure Service Fabric 应用程序部署到 Azure 中的新群集。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 创建一个合作群集。
> * 使用 Visual Studio 将应用程序部署到远程群集。

此教程系列介绍了如何：
> [!div class="checklist"]
> * [构建 .NET Service Fabric 应用程序](service-fabric-tutorial-create-dotnet-app.md)
> * 将应用程序部署到远程群集
> * [向 ASP.NET Core 前端服务添加 HTTPS 终结点](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [使用 Visual Studio Team Services 配置 CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [设置监视和诊断应用程序](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [安装 Visual Studio 2017](https://www.visualstudio.com/)，并安装 **Azure 开发**以及 **ASP.NET 和 Web 开发**工作负荷。
* [安装 Service Fabric SDK](service-fabric-get-started.md)。

## <a name="download-the-voting-sample-application"></a>下载投票示例应用程序

如果未生成[本教程系列的第一部分](service-fabric-tutorial-create-dotnet-app.md)中的投票示例应用程序，可以下载它。 在命令窗口中，运行以下命令，将示例应用程序存储库克隆到本地计算机。

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="publish-to-a-service-fabric-cluster"></a>发布到 Service Fabric 群集

至此，应用程序已准备就绪，可以直接通过 Visual Studio 将它部署到群集了。 [Service Fabric 群集](https://docs.microsoft.com/en-gb/azure/service-fabric/service-fabric-deploy-anywhere)是一组通过网络连接在一起的虚拟机或物理计算机，微服务会在其中部署和管理。

对于本教程，使用 Visual Studio 将投票应用程序部署到 Service Fabric 群集时有两个选项可用：

* 发布到一个试用（合作）群集。
* 发布到订阅中的现有群集。  可以通过 [Azure 门户](https://portal.azure.com)、[PowerShel](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)、[Azure CLI](./scripts/cli-create-cluster.md) 脚本或 [Azure 资源管理器模板](service-fabric-tutorial-create-vnet-and-windows-cluster.md)创建 Service Fabric 群集。

> [!NOTE]
> 许多服务使用反向代理来互相通信。 通过 Visual Studio 创建的群集以及合作群集默认启用反向代理。  如果使用现有的群集，则必须[在群集中启用反向代理](service-fabric-reverseproxy-setup.md#)。


### <a name="find-the-votingweb-service-endpoint-for-your-azure-subscription"></a>查找你的 Azure 订阅的 VotingWeb 服务终结点

如果打算将投票应用程序发布到你自己的 Azure 订阅，请找到前端 Web 服务的终结点。 如果使用合作群集，则投票示例使用的端口 8080 会自动打开，不需要在合作群集的负载均衡器中配置它。

前端 Web 服务在特定端口上进行侦听。  当应用程序部署到 Azure 中的群集时，该群集和应用程序都在 Azure 负载均衡器之后运行。  必须使用规则在此群集的 Azure 负载均衡器中打开应用程序端口，以便入站流量能够通过并抵达 Web 服务。  端口（例如 8080）可以在 *VotingWeb/PackageRoot/ServiceManifest.xml* 文件的 **Endpoint** 元素中找到：

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

对于你的 Azure 订阅，通过 [PowerShell 脚本](./scripts/service-fabric-powershell-open-port-in-load-balancer.md)使用 Azure 中的某个负载均衡规则或者在 [Azure 门户](https://portal.azure.com)中通过此群集的负载均衡器打开此端口。

### <a name="join-a-party-cluster"></a>加入 Party 群集

> [!NOTE]
> 如果打算将应用程序发布到 Azure 订阅中你自己的群集，请跳转到下一部分中的“使用 Visual Studio 部署应用程序”。

合作群集是在 Azure 上托管的、由 Service Fabric 团队运行的免费限时 Service Fabric 群集，任何人都可以在其中部署应用程序及了解平台的情况。 该群集使用单个自签名证书来确保节点到节点和客户端到节点的安全。

登录并[加入 Windows 群集](http://aka.ms/tryservicefabric)。 通过单击 **PFX** 链接，将 PFX 证书下载到计算机。 单击“如何连接到安全合作群集?”链接并复制证书密码。 后续步骤中需要使用证书、证书密码和“连接终结点”值。

![PFX 和连接终结点](./media/service-fabric-quickstart-dotnet/party-cluster-cert.png)

> [!Note]
> 每小时可用的合作群集数目有限。 如果在尝试注册合作群集时出错，可以等待一段时间再重试，或者遵循[部署 .NET 应用](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application)教程中的步骤，在 Azure 订阅中创建一个 Service Fabric 群集并在其中部署应用程序。 如果没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
>

在 Windows 计算机上，将 PFX 安装到 *CurrentUser\My* 证书存储中。

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


   PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
```

请记住一个后续步骤的指纹。

> [!Note]
> 默认情况下，Web 前端服务被配置为侦听端口 8080 上是否有传入流量。 端口 8080 在 Party 群集中打开。  如果需要更改应用程序端口，将其更改为在 Party 群集中打开的端口之一。
>

### <a name="publish-the-application-using-visual-studio"></a>使用 Visual Studio 发布应用程序

至此，应用程序已准备就绪，可以直接通过 Visual Studio 将它部署到群集了。

1. 在解决方案资源管理器中，右键单击“投票”，再选择“发布”。 此时，“发布”对话框显示。

2. 将合作群集页面中或 Azure 订阅中的“连接终结点”复制到“连接终结点”字段。 例如，`zwin7fh14scd.westus.cloudapp.azure.com:19000`。 单击“高级连接参数”，并确保 *FindValue* 和 *ServerCertThumbprint* 值与在前面的步骤中为合作群集安装的证书的指纹匹配，或者与和你的 Azure 订阅匹配的证书匹配。

    ![发布对话框](./media/service-fabric-quickstart-dotnet/publish-app.png)

    群集中的每个应用程序都必须具有唯一名称。  Party 群集是一个公共、共享的环境，但是可能与现有应用程序存在冲突。  如果存在名称冲突，请重命名 Visual Studio 项目并重新部署。

3. 单击“发布” 。

4. 打开浏览器，键入群集地址（后跟“:8080”或所配置的其他端口），以转到群集中的投票应用程序，例如 `http://zwin7fh14scd.westus.cloudapp.azure.com:8080`。 此时，应该能够看到应用程序在 Azure 群集中运行。 在投票网页中，尝试添加和删除投票选项，并针对这些选项中的一个或多个进行投票。

    ![应用程序前端](./media/service-fabric-quickstart-dotnet/application-screenshot-new-azure.png)


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建一个合作群集。
> * 使用 Visual Studio 将应用程序部署到远程群集。

转到下一教程：
> [!div class="nextstepaction"]
> [启用 HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
