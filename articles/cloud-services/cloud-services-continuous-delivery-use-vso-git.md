---
title: "在 Azure 中使用 Git 和 Visual Studio Team Services 进行持续交付 | Microsoft Docs"
description: "了解如何将 Visual Studio Team Services 团队项目配置为使用 Git 自动生成并部署到 Azure App Service 或云服务中的 Web 应用功能。"
services: cloud-services
documentationcenter: .net
author: mlearned
manager: douge
editor: 
ms.assetid: 4b3297ef-0de6-4d5f-925c-fcdacc3085ac
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/06/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 91e061820e8edc81bca395dc78d3e1e840b21438


---
# <a name="continuous-delivery-to-azure-using-visual-studio-team-services-and-git"></a>使用 Visual Studio Team Services 和 Git 向 Azure 持续交付
可以使用 Visual Studio Team Services 团队项目为你的源代码托管 Git 存储库，并在将提交推送到存储库时自动生成并部署到 Azure Web 应用或云服务。

您将需要安装 Visual Studio 2013 和 Azure SDK。 如果你尚未安装 Visual Studio 2013，请在 [www.visualstudio.com](http://www.visualstudio.com) 上选择“**免费试用**”链接以下载该软件。 从[此处](http://go.microsoft.com/fwlink/?LinkId=239540)安装 Azure SDK。

> [!NOTE]
> 需要使用 Visual Studio Team Services 帐户来完成本教程：可以[免费建立一个 Visual Studio Team Services 帐户](http://go.microsoft.com/fwlink/p/?LinkId=512979)。
> 
> 

若要使用 Visual Studio Team Services 将云服务设置为自动生成并部署到 Azure，请执行下列步骤：

## <a name="1-create-a-git-repository"></a>1：创建 Git 存储库
1. 如果还没有 Visual Studio Team Services 帐户，可以在[此处](http://go.microsoft.com/fwlink/?LinkId=397665)获取一个。 创建团队项目时，请选择 Git 作为源代码管理系统。 按照说明将 Visual Studio 连接到你的团队项目。
2. 在**团队资源管理器**中，选择“**克隆此存储库**”链接。
   
    ![][3]
3. 指定本地副本的位置，然后选择“**克隆**”按钮。

## <a name="2-create-a-project-and-commit-it-to-the-repository"></a>2：创建一个项目并将其提交到存储库
1. 在**团队资源管理器**的“**解决方案**”部分，选择“**新建**”链接在本地存储库中创建一个新项目。
   
    ![][4]
2. 你可以通过执行本演练中的步骤来部署 Web 应用或云服务（Azure 应用程序）。 创建新的 Azure 云服务项目或新的 ASP.NET MVC 项目。 请确保该项目面向 .NET Framework 4 或更高版本。 如果要创建云服务项目，请添加一个 ASP.NET MVC Web 角色和一个辅助角色。
   如果要创建 Web 应用，请选择“**ASP.NET Web 应用**”项目模板，然后选择 **MVC**。 有关详细信息，请参阅[在 Azure 应用服务中创建 ASP.NET Web 应用](../app-service-web/web-sites-dotnet-get-started.md)。
3. 打开解决方案的快捷菜单，然后选择“**提交”**。
   
    ![][7]
4. 如果这是你首次在 Visual Studio Team Services 中使用 Git，则需要提供一些信息，以在 Git 中对自己进行标识。 在**团队资源管理器**的“**挂起更改**”区域中，输入你的用户名和电子邮件地址。 输入提交注释，然后选择“**提交**”按钮。
   
    ![][8]
5. 记下在签入时用于包含或排除特定更改的选项。 如果已排除所需的更改，请选择“**全部包含**”。
6. 您现在已提交存储库的本地副本中所做的更改。 接下来，通过选择“**同步**”链接与服务器同步这些更改。

## <a name="3-connect-the-project-to-azure"></a>3：将项目连接到 Azure
1. 现在，已在 Visual Studio Team Services 的 Git 存储库中包含某些源代码，可以将你的 git 存储库连接到 Azure。  在 [Azure 经典门户](http://go.microsoft.com/fwlink/?LinkID=213885)中选择你的云服务或 Web 应用，或通过选择左下角的 + 图标，再选择“**云服务**”或“**Web 应用**”，然后选择“**快速创建**”来创建新的云服务或 Web 应用。
   
    ![][9]
2. 对于云服务，选择“**使用 Visual Studio Team Services 设置发布**”链接。 对于 Web 应用，请选择“**从源控件设置部署**”链接。
   
    ![][10]
3. 在向导中，在文本框中键入 Visual Studio Team Services 帐户的名称，然后选择“**立即授权**”链接。 系统可能会要求你登录。
   
    ![][11]
4. 在“**连接请求**”弹出对话框中，选择“**接受**”以授予 Azure 在 Visual Studio Team Services 中配置团队项目的权限。
   
    ![][12]
5. 授权成功后，将显示一个包含 Visual Studio Team Services 团队项目的下拉列表。  选择在之前的步骤中创建的团队项目的名称，然后选择向导的复选标记按钮。
   
    ![][13]
   
    下次你将提交推送到存储库时，Visual Studio Team Services 将生成并将你的项目部署到 Azure。

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4：触发重新生成和重新部署项目
1. 在 Visual Studio 中，打开文件并进行更改。 例如，更改 MVC Web 角色中 Views\\Shared 文件夹下的文件 `_Layout.cshtml`。
   
    ![][17]
2. 编辑该站点的页脚文本并保存该文件。
   
    ![][18]
3. 在**解决方案资源管理器**中，打开解决方案节点、项目节点或你已更改文件的快捷菜单，然后选择“**提交**”。
4. 键入一条注释，然后选择“**提交**”。
   
    ![][20]
5. 选择“**同步**”链接。
   
    ![][38]
6. 选择“**推送**”链接，将你的提交推送到 Visual Studio Team Services 中的存储库。 （你也可以使用“**同步**”按钮将你的提交复制到存储库。 区别在于“**同步**”还从存储库中提取最新的更改。）
   
    ![][39]
7. 选择“**主页**”按钮可返回**团队资源管理器**主页。
   
    ![][21]
8. 选择“**生成**”可查看正在进行的生成。
   
    ![][22]
   
    **团队资源管理器**将显示已为你的签入触发生成。
   
    ![][23]
9. 要查看进行生成时的详细日志，请双击正在进行的生成的名称。
10. 虽然生成正在进行中，但可查看使用向导链接到 Azure 时创建的生成定义。  打开生成定义的快捷菜单，然后选择“**编辑生成定义**”。
    
    ![][25]
11. 在“**触发器**”选项卡中，你将看到生成定义已设置为默认情况下在每次签入时生成。 （对于云服务，Visual Studio Team Services 生成并自动将主分支部署到过渡环境。 您仍必须执行一个手动步骤，以部署到实时网站。 对于没有过渡环境的 Web 应用，将直接在实时站点中部署主分支。
    
    ![][26]
12. 在“**进程**”选项卡中，你会看到部署环境已设置为云服务或 Web 应用的名称。
    
     ![][27]
13. 指定属性的值（如果希望这些值与默认值不同）。 Azure 发布的属性在“**部署**”部分中，并且你可能还需要设置 MSBuild 参数。 例如，在云服务项目中，若要指定“云”以外的服务配置，请将 MSbuild 参数设置为 `/p:TargetProfile=[YourProfile]`，其中 *[YourProfile]* 将服务配置文件匹配如 ServiceConfiguration.*YourProfile*.cscfg 这样的名称。
    
     下表显示了 **Deployment** 节中提供的属性：
    
    | 属性 | 默认值 |
    | --- | --- |
    | 允许不受信任的证书 |如果为 false，则 SSL 证书必须由根颁发机构签名。 |
    | 允许升级 |允许部署更新现有的部署，而不允许创建新部署。 保留 IP 地址。 |
    | 不要删除 |如果为 true，则不会覆盖现有的无关部署（允许升级）。 |
    | 部署设置的路径 |Web 应用的 .pubxml 文件的路径，相对于存储库的根文件夹。 对于云服务将忽略该属性。 |
    | Sharepoint 部署环境 |与服务名称相同。 |
    | Azure 部署环境 |Web 应用或云服务的名称 |
14. 此时，您的生成应已成功完成。
    
     ![][28]
15. 如果双击生成名称，则 Visual Studio 将显示“**生成摘要**”，其中包括来自关联的单元测试项目的任何测试结果。
    
     ![][29]
16. 在 [Azure 经典门户](http://go.microsoft.com/fwlink/?LinkID=213885)中，可以在选定过渡环境后在“**部署**”选项卡上查看关联的部署。
    
     ![][30]
17. 浏览到你的站点的 URL。 对于 Web 应用，只需选择门户中的“**浏览**”按钮。 对于云服务，选择“**仪表板**”页面的“**速览**”部分中的 URL，其中显示过渡环境。
    
    默认情况下，来自云服务的持续集成的部署将发布到过渡环境。 可以通过将“**备用云服务环境**”属性设置为“**生产**”来改变此情况。 这里显示了站点 URL 在云服务仪表板页上的位置。
    
    ![][31]
    
    新的浏览器选项卡将打开以显示正在运行的网站。
    
    ![][32]
18. 如果对项目进行其他更改，则将触发更多生成，并且将累积多个部署。 标记为“活动”的最新项目。
    
    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5：重新部署以前的生成
此步骤是可选的。 在 Azure 经典门户中，选择以前的部署并选择“**重新部署**”可使站点退回以前的签入。 请注意，这将在 TFS 中触发新的生成，并在部署历史记录中创建新的条目。

![][34]

## <a name="6-change-the-production-deployment"></a>6：更改生产部署
准备就绪后，可以在 Azure 经典门户中选择“**交换**”将过渡环境提升为生产环境。 新部署的过渡环境将提升为生产环境，而之前的生产环境（如果有）将变成过渡环境。 虽然生产环境和过渡环境的活动部署可能会不同，但最新生成的部署历史记录都是相同的，不管是哪一种环境。

![][35]

## <a name="7-deploy-from-a-working-branch"></a>7：从工作分支部署。
使用 Git 时，您通常在工作分支中进行更改，当您的开发达到完成状态时，将集成到主分支。 在一个项目的开发阶段，您需要生成并将工作分支部署到 Azure。

1. 在**团队资源管理器**中，选择“**主页**”按钮，然后选择“**分支**”按钮。
   
    ![][40]
2. 选择“**新建分支**”链接。
   
    ![][41]
3. 输入分支的名称，如“working”，然后选择“**创建分支**”。 这将创建一个新的本地分支。
   
    ![][42]
4. 发布该分支。 选择“**取消发布的分支**”中的分支名称，然后选择“**发布**”。
   
    ![][44]
5. 默认情况下，只有更改主分支才会触发一次持续生成。 若要为工作分支设置持续生成，请在**团队资源管理器**中选择“**生成**”页，然后选择“**编辑生成定义**”。
6. 打开“**源设置**”选项卡。 在“**监视持续集成和生成的分支**”下面，选择“**单击此处添加新行**”。
   
    ![][47]
7. 指定您创建的分支，例如 refs/heads/working。
   
    ![][48]
8. 在代码中进行更改，打开已更改的文件的快捷菜单，然后选择“**提交**”。
   
    ![][43]
9. 选择“**未同步提交**”链接，然后选择“**同步**”按钮或“**推送**”链接，将更改复制到 Visual Studio Team Services 中的工作分支的副本。
   
   ![][45]
10. 导航到“**生成**”视图并查找刚刚为工作分支触发的生成。

## <a name="next-steps"></a>后续步骤
若要了解有关将 Git 与 Visual Studio Team Services 配合使用的更多技巧，请参阅[使用 Visual Studio 在 Git 中开发和共享你的代码](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx)；有关使用不受 Visual Studio Team Services 管理的 Git 存储库发布到 Azure 的信息，请参阅[持续部署到 Azure 应用服务](../app-service-web/app-service-continuous-deployment.md)。 有关 Visual Studio Team Services 的详细信息，请参阅 [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861)。

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG



<!--HONumber=Feb17_HO2-->


