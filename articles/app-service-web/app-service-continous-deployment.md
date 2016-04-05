<!-- not suitable for Mooncake -->

<properties
	pageTitle="连续部署到 Azure Web 应用"
	description="了解如何实现连续部署到 Azure Web 应用。"
	services="app-service"
	documentationCenter=""
	authors="dariac"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.date="03/15/2016"
	wacn.date=""/>
    
# 连续部署到 Azure

将 [Azure Web 应用]与 BitBucket、GitHub 和 Visual Studio Team Services (VSTS) 集成可以实现一种连续部署工作流，在其中，Azure 可以从发布到其中一种服务的项目中提取最近的更新。连续部署选项非常适合用于频繁集成多个分发内容的项目。

## <a name="overview"></a>启用连续部署

若要启用连续部署，请执行以下操作：

1. 将应用内容发布到用于连续部署的存储库。  
    有关将项目发布到这些服务的详细信息，请参阅[创建存储库 (GitHub)]、[创建存储库 (BitBucket)] 和 [VSTS 入门]。

2. 在 [Azure 管理门户]上的应用边栏选项卡中，单击“设置”>“部署源”。单击“选择源”，然后单击“GitHub”或其他源。

	![](./media/app-service-continous-deployment/cd_options.png)
	
3. 完成授权工作流。

4. 在“部署源”边栏选项卡中，选择要从中进行部署的项目和分支。完成后，单击“确定”。
  
	![](./media/app-service-continous-deployment/github_option.png)

	> [AZURE.NOTE] 在使用 GitHub 或 BitBucket 启用连续部署时，将显示公用项目和专用项目。

    Azure 将创建与所选存储库的关联，从指定的分支提取文件，并保留 Azure Web 应用存储库的副本。当你从 Azure 管理门户配置 VSTS 连续部署时，集成将使用 Azure [Kudu 部署引擎](https://github.com/projectkudu/kudu/wiki)，该引擎能够使用每个 `git push` 来自动完成生成和部署任务。你不需要在 VSTS 中单独设置连续部署。此过程完成后，应用边栏选项卡的“部署”部分将显示“活动的部署”消息，指出部署已成功完成。

5. 若要验证是否已成功部署应用，请在 Azure 管理门户的应用边栏选项卡顶部单击“URL”。

6. 若要验证是否能够在从所选存储库进行连续部署，请将更改推送到该存储库。推送到存储库完成后，你的应用应该很快更新以反映更改。可以在应用的“部署”边栏选项卡中验证是否已提取更新。

## <a name="VSsolution"></a>连续部署 Visual Studio 解决方案 

将 Visual Studio 解决方案推送到 Azure 就像推送简单的 index.html 文件一样容易。Azure 部署过程简化了所有细节，包括还原 NuGet 依赖项和构建应用程序二进制文件。可以按照仅在你的 Git 存储库中维护代码的源控制最佳实践操作，并让 Azure 部署处理其余工作。

将 Visual Studio 解决方案推送到 Azure 的步骤与[上一部分](#overview)中的步骤相同，前提是按以下方式配置你的解决方案和存储库：

-	使用 Visual Studio 源代码管理选项生成如下图所示的 `.gitignore` 文件，或者在内容存储库根目录中手动添加一个 `.gitignore` 文件，其内容类似于此 [.gitignore 示例](https://github.com/github/gitignore/blob/master/VisualStudio.gitignore)。 

    ![](./media/app-service-continous-deployment/VS_source_control.png)
 
-	将整个解决方案的目录树添加到你的存储库中，其中 .sln 文件位于存储库根中。

你按照说明设置存储库并将 Azure 中的应用配置为从某个联机 Git 存储库连续发布后，你就可以在 Visual Studio 中从本地开发 ASP.NET 应用程序，并且只需通过将所做的更改推送到联机的 Git 存储库即可连续部署代码。

## <a name="disableCD"></a>禁用连续部署

若要禁用连续部署，请执行以下操作：

1. 在 [Azure 管理门户]上的应用边栏选项卡中，单击“设置”>“部署源”。然后，在“部署”边栏选项卡中单击“断开连接”。

    ![](./media/app-service-continous-deployment/cd_disconnect.png)

2. 在显示确认消息时回答“是”后，若要从其他源设置发布，你可以返回到应用的边栏选项卡，然后单击“设置”>“部署源”。

## 其他资源

* [如何使用适用于 Azure 的 PowerShell]
* [如何使用针对 Mac 和 Linux 的 Azure 命令行工具]
* [Git 文档]
* [项目 Kudu](https://github.com/projectkudu/kudu/wiki)

>[AZURE.NOTE] 如果想要在注册 Azure 帐户之前开始使用 Azure，请转到[试用 Azure Web 应用](https://tryappservice.azure.com/)，你可以在 Azure 中立即创建一个生存期较短的入门 Web 应用。你不需要使用信用卡，也不需要做出承诺。

[Azure Web 应用]: /documentation/articles/app-service-changes-existing-services/
[Azure 管理门户]: https://manage.windowsazure.cn
[VSTS Portal]: https://www.visualstudio.com/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%AE%89%E8%A3%85-Git
[如何使用适用于 Azure 的 PowerShell]: /documentation/articles/powershell-install-configure
[如何使用针对 Mac 和 Linux 的 Azure 命令行工具]: /documentation/articles/xplat-cli-install
[Git 文档]: http://git-scm.com/documentation

[创建存储库 (GitHub)]: https://help.github.com/articles/create-a-repo
[创建存储库 (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[VSTS 入门]: https://www.visualstudio.com/get-started/overview-of-get-started-tasks-vs
[Continuous delivery to Azure using Visual Studio Team Services]: /documentation/articles/cloud-services-continuous-delivery-use-vso

<!---HONumber=Mooncake_0328_2016-->