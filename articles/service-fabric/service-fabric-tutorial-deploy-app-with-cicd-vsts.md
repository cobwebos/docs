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
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 890acae2aebf7684e567b9b49377ca7b6da95245
ms.openlocfilehash: d0f67b1a63c36e878ed5f7c9aa1c45267e5b156c
ms.contentlocale: zh-cn
ms.lasthandoff: 09/20/2017

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
> * [设置监视和诊断应用程序](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>先决条件
在开始学习本教程之前：
- 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [安装 Visual Studio 2017](https://www.visualstudio.com/)，并安装 **Azure 开发**以及 **ASP.NET 和 Web 开发**工作负荷。
- [安装 Service Fabric SDK](service-fabric-get-started.md)
- 创建一个 Service Fabric 应用程序，例如[根据此教程](service-fabric-tutorial-create-dotnet-app.md)创建。 
- 在 Azure 上创建一个 Windows Service Fabric 群集，例如[根据此教程](service-fabric-tutorial-create-cluster-azure-ps.md)创建
- 创建一个 [Team Services 帐户](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)。

## <a name="download-the-voting-sample-application"></a>下载投票示例应用程序
如果未生成[本教程系列的第一部分](service-fabric-tutorial-create-dotnet-app.md)中的投票示例应用程序，还可以下载它。 在命令窗口中，运行以下命令，将示例应用程序存储库克隆到本地计算机。

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

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
Team Services 生成定义所描述的工作流由一系列按顺序执行的生成步骤组成。 创建一个生成定义，以生成要部署到 Service Fabric 群集的 Service Fabric 应用程序程序包和其他项目。 详细了解 [Team Services 生成定义](https://www.visualstudio.com/docs/build/define/create)。 

Team Services 发布定义描述了将应用程序程序包部署到群集的工作流。 一起使用时，生成定义和发布定义将执行从开始到结束的整个工作流，即一开始只有源文件，而结束时群集中会有一个运行的应用程序。 详细了解 Team Services [发布定义](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)。

### <a name="create-a-build-definition"></a>创建生成定义
打开 Web 浏览器并导航到新的团队项目：https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting。 

依次选择“生成和发布”选项卡、“生成”、“+ 新建定义”。  在“选择模板”中，选择“Azure Service Fabric 应用程序”模板，然后单击“应用”。 

![选择“生成模板”][select-build-template] 

由于投票应用程序包含 .NET Core 项目，请添加还原依赖项的任务。 在“任务”视图中，选择左下角的“+ 添加任务”。 在“命令行”搜索找到命令行任务，然后单击“添加”。 

![添加任务][add-task] 

在新的任务中，在“显示名称”中输入“运行 dotnet.exe”，在“工具”中输入“dotnet.exe”，在“参数”中输入“restore”。 

![新建任务][new-task] 

在“触发器”视图的“持续集成”下，单击“启用此触发器”开关。 

选择“保存并排队”，并输入“托管 VS2017”作为“代理队列”。 选择“排队”，手动启动生成。  生成还会触发推送或签入。

若要检查生成进度，请切换到“生成”选项卡。在验证生成成功执行后，定义用于将应用程序部署到群集的发布定义。 

### <a name="create-a-release-definition"></a>创建发布定义  

依次选择“生成和发布”选项卡、“发布”、“+ 新建定义”。  在“创建发布定义”中，从列表中选择“Azure Service Fabric 部署”模板，单击“下一步”。  选择“生成”源，勾选“持续部署”框，单击“创建”。 

在“环境”视图中，单击“群集连接”右侧的“添加”。  指定“mysftestcluster”的连接名称、“tcp://mysftestcluster.westus.cloudapp.azure.com:19000”的群集终结点和群集的 Azure Active Directory 或证书凭据。 对于 Azure Active Directory 凭据，可在“**用户名**”和“**密码**”字段中定义需要用来连接到群集的凭据。 对于基于证书的身份验证，可在“客户端证书”字段中定义客户端证书文件的 Base64 编码。  若要了解如何获取该值，请参阅有关该字段的弹出帮助。  如果证书受密码保护，可在“**密码**”字段中定义密码。  单击“保存”，保存发布定义。

![添加群集连接][add-cluster-connection] 

单击“在代理上运行”，然后为“部署队列”选择“托管 VS2017”。 单击“保存”，保存发布定义。

![在代理上运行][run-on-agent]

选择“+ 发布” -> “创建发布” -> “创建”，手动创建发布。  验证部署是否已成功且应用程序是否正在群集中运行。  打开 web 浏览器并导航到 [http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/)。  记下应用程序版本，在本例中为“1.0.0.20170616.3”。 

## <a name="commit-and-push-changes-trigger-a-release"></a>提交并推送更改，触发发布
通过将一些代码更改签入到 Team Services 中来验证持续集成管道是否正常工作。    

在编写代码时，Visual Studio 会自动跟踪代码更改。 通过从右下角的状态栏中选择“挂起的更改”图标（![挂起的][pending]）来将更改提交到本地 Git 存储库。

在“团队资源管理器”中的“更改”视图中，添加一条消息来说明你的更新，然后提交更改。

![全部提交][changes]

在“团队资源管理器”中选择“未发布的更改”状态栏图标（![未发布的更改][unpublished-changes]）或“同步”视图。 选择“推送”以更新 Team Services/TFS 中的代码。

![推送更改][push]

将更改推送到 Team Services 会自动触发生成。  当生成定义成功完成时，会自动创建一个发布，并将开始升级群集上的应用程序。

若要检查生成进度，请在 Visual Studio 中切换到“团队资源管理器”中的“生成”选项卡。  在验证生成成功执行后，定义用于将应用程序部署到群集的发布定义。

验证部署是否已成功且应用程序是否正在群集中运行。  打开 web 浏览器并导航到 [http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.westus.cloudapp.azure.com:19080/Explorer/)。  记下应用程序版本，在本例中为“1.0.0.20170815.3”。

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>更新应用程序
在应用程序中进行代码更改。  按照前面的步骤保存并提交更改。

在应用程序升级开始后，可以在 Service Fabric Explorer 中观察升级进度：

![Service Fabric Explorer][sfx2]

应用程序升级可能要花费几分钟时间才能完成。 当升级完成后，应用程序将运行下一版本。  在本例中为“1.0.0.20170815.4”。

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 向项目中添加源代码管理
> * 创建生成定义
> * 创建发布定义
> * 自动部署和升级应用程序

转到下一教程：
> [!div class="nextstepaction"]
> [设置监视和诊断应用程序](service-fabric-tutorial-monitoring-aspnet.md) 


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
[run-on-agent]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/RunOnAgent.png
