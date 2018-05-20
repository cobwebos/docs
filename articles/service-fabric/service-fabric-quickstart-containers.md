---
title: 创建 Azure Service Fabric Windows 容器应用程序 | Microsoft Docs
description: 在本快速入门中，请在 Azure Service Fabric 上创建第一个 Windows 容器应用程序。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/30/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: d78dbc9a32e804e37eb76047edcc050482df5761
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-deploy-a-service-fabric-windows-container-application-on-azure"></a>快速入门：在 Azure 上部署 Service Fabric Windows 容器应用程序
Azure Service Fabric 是一款分布式系统平台，可用于部署和管理可缩放的可靠微服务和容器。 

在 Service Fabric 群集上运行 Windows 容器中的现有应用程序不需要对应用程序进行任何更改。 本快速入门介绍如何在 Service Fabric 应用程序中部署预建的 Docker 容器映像。 完成后，你会有一个正在运行的 Windows Server 2016 Nano Server 和 IIS 容器。 本快速入门介绍如何部署 Windows 容器。若要部署 Linux 容器，请阅读[此快速入门](service-fabric-quickstart-containers-linux.md)。

![iis-default][iis-default]

此快速入门介绍如何：

* 打包 Docker 映像容器
* 配置通信
* 生成并打包 Service Fabric 应用程序
* 将容器应用程序部署到 Azure

## <a name="prerequisites"></a>先决条件
* 一个 Azure 订阅（可以创建[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)）。
* 一台运行以下软件的开发计算机：
  * Visual Studio 2015 或 Visual Studio 2017。
  * [Service Fabric SDK 和工具](service-fabric-get-started.md)。

## <a name="package-a-docker-image-container-with-visual-studio"></a>使用 Visual Studio 打包 Docker 映像容器
Service Fabric SDK 和工具提供服务模板，用于将容器部署到 Service Fabric 群集。

以“管理员”身份启动 Visual Studio。  选择“文件” > “新建” > “项目”。

选择“Service Fabric 应用程序”，将其命名为“MyFirstContainer”，并单击“确定”。

从“托管的容器和应用程序”模板中选择“容器”。

在“映像名称”中输入“microsoft/iis:nanoserver”，即 [Windows Server Nano Server 和 IIS 基映像](https://hub.docker.com/r/microsoft/iis/)。 

配置容器的“端口到主机”端口映射，使端口 80 上针对服务的传入请求映射到容器上的端口 80。  将“容器端口”设置为“80”并将“主机端口”设置为“80”。  

将服务命名为“MyContainerService”，然后单击“确定”。

![新服务对话框][new-service]

## <a name="create-a-cluster"></a>创建群集
若要将应用程序部署到 Azure 中的群集，可以加入合作群集。 合作群集是在 Azure 上托管的、由 Service Fabric 团队运行的免费限时 Service Fabric 群集，任何人都可以在其中部署应用程序及了解平台的情况。  该群集使用单个自签名证书来确保节点到节点和客户端到节点的安全。 合作群集支持容器。 如果决定设置并使用自己的群集，该群集必须在支持容器的 SKU（例如，包含容器的 Windows Server 2016 Datacenter）上运行。

登录并[加入 Windows 群集](http://aka.ms/tryservicefabric)。 通过单击 **PFX** 链接，将 PFX 证书下载到计算机。 单击“如何连接到安全合作群集?”链接并复制证书密码。 后续步骤中需要使用证书、证书密码和“连接终结点”值。

![PFX 和连接终结点](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

> [!Note]
> 每小时可用的合作群集数目有限。 如果在尝试注册合作群集时出错，可以等待一段时间再重试，或者遵循[部署 .NET 应用](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application)教程中的步骤，在 Azure 订阅中创建一个 Service Fabric 群集并在其中部署应用程序。 通过 Visual Studio 创建的群集支持容器。 在群集中部署并验证应用程序后，可以直接跳到本快速入门中的 [Service Fabric 应用程序和服务清单的完整示例](#complete-example-service-fabric-application-and-service-manifests)。 
>

在 Windows 计算机上，将 PFX 安装到 *CurrentUser\My* 证书存储中。

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
``` 

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>使用 Visual Studio 将应用程序部署到 Azure
至此，应用程序已准备就绪，可以直接通过 Visual Studio 将它部署到群集了。

在解决方案资源管理器中右键单击“MyFirstContainer”，选择“发布”。 此时，“发布”对话框显示。

将 Party 群集页面中的“连接终结点”复制到“连接终结点”字段。 例如，`zwin7fh14scd.westus.cloudapp.azure.com:19000`。 

单击“发布” 。

群集中的每个应用程序都必须具有唯一名称。  Party 群集是一个公共、共享的环境，但是可能与现有应用程序存在冲突。  如果存在名称冲突，请重命名 Visual Studio 项目并重新部署。

打开浏览器并导航到“合作群集”页中指定的“连接终结点”。 可以选择性地在 URL 的前面添加方案标识符 `http://`，并在后面追加端口 `:80`。 例如，http://zwin7fh14scd.westus.cloudapp.azure.com:80。 此时会看到 IIS 默认网页：![IIS 默认网页][iis-default]

## <a name="next-steps"></a>后续步骤
在此快速入门中，读者学习了如何：

* 打包 Docker 映像容器
* 配置通信
* 生成并打包 Service Fabric 应用程序
* 将容器应用程序部署到 Azure

若要详细了解如何在 Service Fabric 中使用 Windows 容器，请继续学习适用于 Windows 容器应用的教程。

> [!div class="nextstepaction"]
> [创建 Windows 容器应用](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
