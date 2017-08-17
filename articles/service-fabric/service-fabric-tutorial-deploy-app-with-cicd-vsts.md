---
title: "部署具有持续集成功能的 Azure Service Fabric 应用程序 (Team Services) | Microsoft Docs"
description: "了解如何使用 Visual Studio Team Services 为 Service Fabric 应用程序设置持续集成和部署。  将应用程序部署到 Azure 中的 Service Fabric 群集。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: dda5ea77b35fa8491128135ea7709016781f2aea
ms.contentlocale: zh-cn
ms.lasthandoff: 08/10/2017

---

# <a name="deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>将具有 CI/CD 的应用程序部署到 Service Fabric 群集
本教程是一个系列的第三部分，介绍了如何使用 Visual Studio Team Services 为 Azure Service Fabric 应用程序设置持续集成和部署。  需要一个现有的 Service Fabric 应用程序，将使用在[生成 .NET 应用程序](service-fabric-tutorial-create-dotnet-app.md)中创建的应用程序作为示例。

在该系列的第三部分中，你会学习如何：

> [!div class="checklist"]
> * 向项目中添加源代码管理
> * 在 Team Services 中创建生成定义
> * 在 Team Services 中创建发布定义
> * 自动部署和升级应用程序

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * [构建 .NET Service Fabric 应用程序](service-fabric-tutorial-create-dotnet-app.md)
> * [将应用程序部署到远程群集](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * 使用 Visual Studio Team Services 配置 CI/CD

## <a name="prerequisites"></a>先决条件
在开始学习本教程之前：
- 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [安装 Visual Studio 2017](https://www.visualstudio.com/)，并安装 **Azure 开发**以及 **ASP.NET 和 Web 开发**工作负荷。
- [安装 Service Fabric SDK](service-fabric-get-started.md)
- 创建一个 Service Fabric 应用程序，例如[根据此教程](service-fabric-tutorial-create-dotnet-app.md)创建。 
- 在 Azure 上创建一个 Windows Service Fabric 群集，例如[根据此教程](service-fabric-tutorial-create-cluster-azure-ps.md)创建
- 创建一个 [Team Services 帐户](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)。

## <a name="prepare-a-publish-profile"></a>准备一个发布配置文件
你已[创建了一个应用程序](service-fabric-tutorial-create-dotnet-app.md)并已[将该应用程序部署到了 Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)，现在可以设置持续集成了。  首先，在应用程序中准备一个发布配置文件，供要在 Team Services 中执行的部署进程使用。  应当将发布配置文件配置为以你之前创建的群集为目标。  启动 Visual Studio 并打开一个现有的 Service Fabric 应用程序项目。  在“解决方案资源管理器”中，右键单击该应用程序并选择“发布...”。

在应用程序项目中选择一个要用于持续集成工作流的目标配置文件，例如 Cloud。  指定群集连接终结点。  选中“升级应用程序”复选框，以便应用程序针对 Team Services 中的每个部署进行升级。  单击“保存”超链接将设置保存到发布配置文件，然后单击“取消”关闭对话框。  

![推送配置文件][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>将 Visual Studio 解决方案共享到一个新的 Team Services Git 存储库
将应用程序源文件共享到 Team Services 中的一个团队项目，以便可以生成内部版本。  

通过在 Visual Studio 的右下角的状态栏中选择“添加到源代码管理” -> “Git”为项目创建一个新的本地 Git 存储库。 

在“团队资源管理器”中的“推送”视图中，在“推送到 Visual Studio Team Services”下选择“发布 Git 存储库”按钮。

![推送 Git 存储库][push-git-repo]

验证你的电子邮件地址并在“Team Services 域”下拉列表中选择你的帐户。 输入你的存储库名称并选择“发布存储库”。

![推送 Git 存储库][publish-code]

发布存储库会在你的帐户中创建一个与本地存储库同名的新团队项目。 若要在现有团队项目中创建存储库，请单击“存储库名称”旁边的“高级”并选择一个团队项目。 可以通过选择“在 web 上查看”来在 web 上查看代码。

## <a name="configure-continuous-delivery-with-vsts"></a>配置“使用 VSTS 实现持续交付”
使用 Visual Studio 中的内置向导配置“使用 VSTS 实现持续交付”。

1. 在“解决方案资源管理器”中右键单击“MyApplication”应用程序项目，然后选择“添加” -> “使用 VSTS 实现持续交付”。 此时将弹出一个对话框，用于输入所需配置。

    ![使用 VSTS 实现持续交付][continuous-delivery-with-VSTS]

    “帐户”、“项目”和“Git 存储库”值将自动输入。

2. 选择“Hosted VS2017”作为代理队列。

3. 在“群集连接”下拉列表中选择“创建连接”，这将打开一个网站来配置 VSTS 中的服务终结点。 

4. 将焦点置于浏览器窗口上并选择“新建服务终结点” -> “Service Fabric”。

    ![新建服务终结点][new-service-endpoint]

    此时将弹出一个对话框，用于添加新的 Service Fabric 连接。
    
5. 选择“基于证书”并完成对话框：

    ![“新建服务终结点”对话框][new-service-endpoint-dialog]

    1. 输入**连接名称**。
    2. 在“群集终结点”字段中输入群集的管理终结点（例如，“tcp://mycluster.eastus.cloudapp.azure.com:19000”）。 选择“tcp://”作为协议。  默认管理终结点端口为 19000。
    3. 输入**服务器证书指纹**。  可以通过打开你的群集的 Service Fabric Explorer (https://mycluster.eastus.cloudapp.azure.com:19080) 来获取指纹。
        - 在树视图中选择“群集”节点，然后在右侧的窗格中选择“清单”选项卡。
        - 在 XML 文件中查找 **<ServerCertificate>** 元素并复制 **X509FindValue** 属性的内容。
    4. 在“客户端证书”字段中，以 Base64 编码的字符串形式输入客户端证书，需要获取生成代理上安装的证书：
        - 确保具有以 PFX 文件格式提供的证书。
        - 运行以下 PowerShell 命令来将 PFX 文件作为 Base64 编码的字符串输出到剪贴板：`[System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(C:\path\to\certificate.pfx)) | clip`
        - 将剪贴板中的值粘贴 (ctrl+v) 到对话框中的相应字段。
    5. 在“密码”字段中键入**证书密码**，然后单击“确定”。

6. 在 Visual Studio 中，在“使用 VSTS 实现持续交付”对话框的“群集连接”字段中进行选择。**<Refresh>** 刚才创建的群集终结点现在应当会显示在下拉列表中。

7. 选择默认的生成和发布定义名称，或者在对话框中更改所建议的名称。 完成后，单击“确定”。

过一会儿，Visual Studio 中将弹出一个对话框，询问是否要在浏览器中打开生成和发布定义。 可以选择这样做来检查它们的配置情况，但这不是完成本教程所必需的。

- Team Services 生成定义所描述的工作流由一系列按顺序执行的生成步骤组成。 创建一个生成定义，以生成要部署到 Service Fabric 群集的 Service Fabric 应用程序程序包和其他项目。 详细了解 [Team Services 生成定义](https://www.visualstudio.com/docs/build/define/create)。
- Team Services 发布定义描述了将应用程序程序包部署到群集的工作流。 一起使用时，生成定义和发布定义将执行从开始到结束的整个工作流，即一开始只有源文件，而结束时群集中会有一个运行的应用程序。 详细了解 Team Services [发布定义](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)。

## <a name="commit-and-push-changes-trigger-a-release"></a>提交并推送更改，触发发布
通过将一些代码更改签入到 Team Services 中来验证持续集成管道是否正常工作。    

在编写代码时，Visual Studio 会自动跟踪代码更改。 通过从右下角的状态栏中选择“挂起的更改”图标（![挂起的][pending]）来将更改提交到本地 Git 存储库。

在“团队资源管理器”中的“更改”视图中，添加一条消息来说明你的更新，然后提交更改。

![全部提交][changes]

在“团队资源管理器”中选择“未发布的更改”状态栏图标（![未发布的更改][unpublished-changes]）或“同步”视图。 选择“推送”以更新 Team Services/TFS 中的代码。

![推送更改][push]

将更改推送到 Team Services 会自动触发生成。  当生成定义成功完成时，会自动创建一个发布，并将开始升级群集上的应用程序。

若要检查生成进度，请在 Visual Studio 中切换到“团队资源管理器”中的“生成”选项卡。  在验证生成成功执行后，定义用于将应用程序部署到群集的发布定义。

验证部署是否已成功且应用程序是否正在群集中运行。  打开 web 浏览器并导航到 [http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/)。  记下应用程序版本，在本例中为“1.0.0.20170616.3”。

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>更新应用程序
在应用程序中进行代码更改。  按照前面的步骤保存并提交更改。

在应用程序升级开始后，可以在 Service Fabric Explorer 中观察升级进度：

![Service Fabric Explorer][sfx2]

应用程序升级可能要花费几分钟时间才能完成。 当升级完成后，应用程序将运行下一版本。  在本例中为“1.0.0.20170616.4”。

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 向项目中添加源代码管理
> * 创建生成定义
> * 创建发布定义
> * 自动部署和升级应用程序

现在，你已部署了应用程序并配置了持续集成，请尝试以下操作：
- [升级应用](service-fabric-application-upgrade.md)
- [测试应用](service-fabric-testability-overview.md) 
- [监视和诊断](service-fabric-diagnostics-overview.md)


<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[add-task]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddTask.png
[new-task]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewTask.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
