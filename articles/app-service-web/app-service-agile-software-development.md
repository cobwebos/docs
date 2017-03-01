---
title: "使用 Azure App Service 进行敏捷软件开发"
description: "学习如何使用支持敏捷软件开发的方式，通过 Azure App Service 创建高缩放性的复杂应用程序。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: c0fdb676-36a6-4738-925f-65b4835d187f
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/01/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: c214e37fb4211e8a2686a2beb18cf84ee77d5783
ms.lasthandoff: 02/16/2017


---
# <a name="agile-software-development-with-azure-app-service"></a>使用 Azure App Service 进行敏捷软件开发
本教程将介绍如何以支持[敏捷软件开发](https://en.wikipedia.org/wiki/Agile_software_development)的方式，通过 [Azure App Service](/services/app-service/) 创建高缩放性的复杂应用程序。 假设用户已知道了如何[通过可预测方式在 Azure 中部署复杂应用程序](app-service-deploy-complex-application-predictably.md)。

技术流程限制通常会妨碍成功的实施灵便方法。 如果在 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 中合理地将部署的协调与管理结合起来，则具有[连续发布](app-service-continuous-deployment.md)、[过渡环境](web-sites-staged-publishing.md)（槽）和[监视](web-sites-monitor.md)等功能的 Azure App Service 是非常适合采用敏捷软件开发的开发人员的解决方案。

下表是敏捷开发的相关要求以及 Azure 服务如何启用它们的简短列表。

| 要求 | Azure 如何实现 |
| --- | --- |
| - 每次提交时生成<br>- 自动快速生成 |配置连续部署后，Azure App Service 可以根据开发分支充当实时运行的构建版本。 每次将代码推送到分支时，Azure 中会自动构建和实时运行代码。 |
| - 将构建版本设为自我测试 |负载测试、Web 测试等可以使用 Azure 资源管理器模板进行部署。 |
| - 在生产环境的复本中运行测试 |Azure 资源管理器模板可以用来创建 Azure 生产环境的复本（包括应用设置、连接字符串模板、缩放等）以通过快速且可预测的方式测试。 |
| - 轻松查看最新构建版本的结果 |从存储库连续部署到 Azure，意味着你可以在提交更改后，立即在实时应用程序中测试新代码。 |
| - 每天提交到主要分支<br>- 自动进行部署 |生产应用程序与存储库主要分支的连续集成可自动将主要分支的每次提交/合并部署到生产环境。 |

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-do"></a>执行的操作
用户将逐步执行典型的“开发-测试-过渡-生产”工作流，以将新更改发布到 [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) 示例应用程序（包含两个 [Web 应用](/services/app-service/web/)：一个是前端 (FE)，另一个是 Web API 后端 (BE)）和 [SQL 数据库](/services/sql-database/)。 将按如下所示使用部署体系结构：

![](./media/app-service-agile-software-development/what-1-architecture.png)

将图片放入文字：

* 部署体系结构分成三个不同的环境（或 Azure 中的[资源组](../azure-resource-manager/resource-group-overview.md)），每个环境都有自身的[应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)、[缩放](web-sites-scale.md)设置和 SQL 数据库。 
* 你可以单独管理每个环境。 它们甚至可以存在于不同的订阅中。
* 过渡和生产环境实现为相同 App Service 应用的两个槽。 主分支是设置进行具有过渡槽的连续集成。
* 在过渡槽（包含生产数据）上验证主分支的提交时，已验证的过渡应用将交换到生产槽，[且不会造成停机](web-sites-staged-publishing.md)。

生产和过渡环境由 [*&lt;repository_root>*/ARMTemplates/ProdandStage.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/ProdAndStage.json) 中的模板定义。

开发和测试环境由 [*&lt;repository_root>*/ARMTemplates/Dev.json](https://github.com/azure-appservice-samples/ToDoApp/blob/master/ARMTemplates/Dev.json) 中的模板定义。

你还要使用典型分支策略，其中，代码从开发分支移到测试分支，然后移到主分支（所谓的质量提升）。

![](./media/app-service-agile-software-development/what-2-branches.png) 

## <a name="what-you-will-need"></a>所需的项目
* 一个 Azure 帐户
* 一个 [GitHub](https://github.com/) 帐户
* Git Shell（与 [GitHub for Windows](https://windows.github.com/) 一起安装）- 使用户能够在同一会话中运行 Git 和 PowerShell 命令 
* 最新的 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/0.9.4-June2015/azure-powershell.0.9.4.msi) 位
* 基本了解以下知识：
  * [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 模板部署（另请参阅[通过可预测的方式在 Azure 中部署复杂应用程序](app-service-deploy-complex-application-predictably.md)）
  * [Git](http://git-scm.com/documentation)
  * [PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [!NOTE]
> 完成本教程需要有一个 Azure 帐户：
> 
> * 可以 [免费建立一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)：获取可用来试用付费版 Azure 服务的信用额度，甚至在用完信用额度后，仍可以保留帐户和使用免费的 Azure 服务（如 Web 应用）。
> * 可以[激活 Visual Studio 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)：Visual Studio 订阅每月为用户提供可用来试用付费版 Azure 服务的信用额度。
> 
> 如果您想要在注册 Azure 帐户之前开始使用 Azure App Service，请转到 [试用 App Service](https://azure.microsoft.com/try/app-service/)，您可以在 App Service 中立即创建一个生存期较短的入门 Web 应用。 你不需要使用信用卡，也不需要做出承诺。
> 
> 

## <a name="set-up-your-production-environment"></a>设置生产环境
> [!NOTE]
> 本教程中使用的脚本自动从 GitHub 存储库配置连续发布。 这需要你的 GitHub 凭据已存储在 Azure 中，否则，尝试配置 Web Apps 的源代码管理设置时，脚本化部署将会失败。 
> 
> 若要在 Azure 中存储 GitHub 凭据，请在 [Azure 门户](https://portal.azure.com/)中创建 Web 应用，并[配置 GitHub 部署](app-service-continuous-deployment.md)。 您只需执行此操作一次。 
> 
> 

在典型的 DevOps 方案中，应用程序在 Azure 中实时运行，并且你可以通过连续发布对它进行更改。 此方案将会提供你开发、测试和使用的模板来部署生产环境。 本部分将介绍如何设置生产环境。

1. 创建 [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) 存储库的专属分叉。 有关创建分叉的信息，请参阅 [Fork a Repo](https://help.github.com/articles/fork-a-repo/)（分叉存储库）。 创建分叉后，可以在浏览器中查看它。
   
   ![](./media/app-service-agile-software-development/production-1-private-repo.png)
2. 打开 Git Shell 会话。 如果还没有 Git Shell，请立即安装 [GitHub for Windows](https://windows.github.com/)。
3. 通过执行以下命令创建分叉的本地复本：
   
     git clone https://github.com/<your_fork>/ToDoApp.git 
4. 创建本地克隆后，请导航到 *&lt;repository_root>*\ARMTemplates 并运行 deploy.ps1 脚本，如下所示：
   
     .\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git
5. 出现提示时，键入所需的用户名和密码来访问数据库。
   
   你应会看到各种 Azure 资源的设置进度。 部署完成后，脚本将在浏览器中启动应用程序，并发出友好的提示音。
   
   ![](./media/app-service-agile-software-development/production-2-app-in-browser.png)
   
   > [!TIP]
   > 查看 *&lt;repository_root>*\ARMTemplates\Deploy.ps1，了解其如何生成具有唯一 ID 的资源。 可以使用相同的方法来创建相同部署的复本，而不必担心资源名称冲突。
   > 
   > 
6. 返回 Git Shell 会话，运行：
   
     .\swap –Name ToDoApp<unique_string>master
   
   ![](./media/app-service-agile-software-development/production-4-swap.png)
7. 脚本完成后，请返回浏览到前端的地址 (http://ToDoApp*&lt;unique_string>*master.azurewebsites.net/)，以查看在生产环境中运行的应用程序。
8. 登录到 [Azure 门户](https://portal.azure.com/)并查看创建的内容。
   
   应该可以在相同的资源组中看到两个 Web 应用，其中一个的名称具有 `Api` 后缀。 当你查看资源组视图时，还会看到 SQL 数据库和服务器、App Service 计划以及 Web 应用的过渡槽。 浏览不同的资源，并将其与 *&lt;repository_root>*\ARMTemplates\ProdAndStage.json 进行比较，以查看其在模板中的配置方式。
   
   ![](./media/app-service-agile-software-development/production-3-resource-group-view.png)

你现在已经设置了生产环境。 接下来，将要开始更新应用程序。

## <a name="create-dev-and-test-branches"></a>创建开发和测试分支
现在，你已在 Azure 的生产环境中创建了一个复杂应用程序，接下来，你要使用灵便方法来更新应用程序。 在本部分中，你将要创建需要进行必要更新的开发和测试分支。

1. 首先创建测试环境。 在 Git Shell 会话中，运行以下命令来创建名为 **NewUpdate** 的新分支的环境。 
   
     git checkout -b NewUpdate   git push origin NewUpdate   .\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch NewUpdate
2. 出现提示时，键入所需的用户名和密码来访问数据库。 
   
   部署完成后，脚本将在浏览器中启动应用程序，并发出友好的提示音。 这时，你已创建了带有自身测试环境的新分支。 请花点时间来了解有关此测试环境的一些要点：
   
   * 可以在任何 Azure 订阅中创建测试环境。 这意味着，你可以分开管理生产环境和测试环境。
   * 测试环境在 Azure 中实时运行。
   * 测试环境类似于生产环境，差别在于过渡槽和缩放设置。 因为这是 ProdandStage.json 与 Dev.json 之间的唯一差别，所以你可以得知这项信息。
   * 可以在测试环境自身具有不同定价层（例如**免费**）的应用服务计划中对其进行管理。
   * 删除这个测试环境就像删除资源组一样简单。 [稍后](#delete)将学习如何执行这项操作。
3. 运行以下命令，以继续创建开发分支：
   
     git checkout -b Dev   git push origin Dev   .\deploy.ps1 -TemplateFile .\Dev.json -RepoUrl https://github.com/<your_fork>/ToDoApp.git -Branch Dev
4. 出现提示时，键入所需的用户名和密码来访问数据库。 
   
   请花点时间来了解有关此开发环境的一些要点： 
   
   * 开发环境的配置与测试环境相同，因为它是使用相同模板部署的。
   * 可以在开发人员自己的 Azure 订阅中创建每个开发环境，但要对测试环境进行单独管理。
   * 开发环境在 Azure 中实时运行。
   * 删除开发环境就像删除资源组一样简单。 [稍后](#delete)将学习如何执行这项操作。

> [!NOTE]
> 有多位开发人员处理新的更新时，只要执行以下操作，每一位都可以轻松创建分支和专用开发环境：
> 
> 1. 在 GitHub 中创建其存储库的专属分叉（请参阅[分叉存储库](https://help.github.com/articles/fork-a-repo/)）。
> 2. 克隆其本地计算机上的分叉
> 3. 运行相同的命令，以创建其自身的开发分支和环境。
> 
> 

完成后，GitHub 分叉应有三个分支：

![](./media/app-service-agile-software-development/test-1-github-view.png)

三个不同的资源组中应该有六个 Web 应用（两个一组，共三组）：

![](./media/app-service-agile-software-development/test-2-all-webapps.png)

> [!NOTE]
> 请注意，ProdandStage.json 将生产环境指定为使用**标准**定价层，这适合生产应用程序的缩放性。
> 
> 

## <a name="build-and-test-every-commit"></a>构建和测试每项提交
模板文件 ProdAndStage.json 和 Dev.json 已指定源代码管理参数，默认情况下，将设置 Web 应用程序的连续发布。 因此，GitHub 分支的每项提交都会触发从该分支自动部署到 Azure。 现在，我们看看你的设置的运行情况。

1. 确保你处于本地存储库的 Dev 分支中。 为此，请在 Git Shell 中运行以下命令：
   
     git checkout Dev
2. 通过将代码更改为使用 [Bootstrap](http://getbootstrap.com/components/) 列表，对应用的 UI 层进行简单更改。 打开 *&lt;repository_root>*\src\MultiChannelToDo.Web\index.cshtml，进行下面突出显示的更改：
   
   ![](./media/app-service-agile-software-development/commit-1-changes.png)
   
   > [!NOTE]
   > 如果无法看到上述图像： 
   > 
   > * 在第 18 行，将 `check-list` 更改为 `list-group`。
   > * 在第 19 行，将 `class="check-list-item"` 更改为 `class="list-group-item"`。
   > 
   > 
3. 保存更改。 返回到 Git Shell 并运行以下命令：
   
     cd <repository_root> git add .
     git commit -m "changed to bootstrap style" git push origin Dev
   
   这些 git 命令与另一个源代码管理系统（例如 TFS）中的“签入你的代码”类似。 运行 `git push` 时，新的提交将触发自动将代码推送到 Azure，然后重建应用程序，以反映开发环境中的更改。
4. 若要验证是否已将此代码推送到开发环境，请转到开发环境的 Web 应用边栏选项卡，查看“部署”部分。 你应该可以在这里看到最新提交消息。
   
   ![](./media/app-service-agile-software-development/commit-2-deployed.png)
5. 在这里，单击“浏览”以查看 Azure 中实时应用程序中的新更改。
   
   ![](./media/app-service-agile-software-development/commit-3-webapp-in-browser.png)
   
   这对应用程序而言是相当小的更改。 不过，多次对复杂 Web 应用程序进行更改通常会产生不利和非预期的负面影响。 如果能够轻松测试实时构建版本中的每个提交，便可以在客户看到这些问题之前找出问题。

到目前为止，用户应已认识到，作为 **NewUpdate** 项目的开发人员，可轻松自行创建开发环境，然后构建每项提交并测试每个构建版本。

## <a name="merge-code-into-test-environment"></a>将代码合并到测试环境
准备好将代码从 Dev 分支推送到 NewUpdate 分支后，请执行标准的 git 过程：

1. 在 GitHub 中，将 NewUpdate 的任何新提交（例如其他开发人员所创建的提交）合并到 Dev 分支。 GitHub 上的任何新提交都会在开发环境中触发代码推送和构建。 然后可以确保开发分支中的代码仍能与 NewUpdate 分支中的最新位一起运行。
2. 在 GitHub 中，将所有新提交从 Dev 分支合并到 NewUpdate 分支。 此操作将在测试环境中触发代码推送和构建。 

请再次注意，因为连续部署已设置这些 git 分支，所以你不需要采取任何其他措施（例如运行集成构建）。 你只需要使用 git 执行标准源代码管理措施，Azure 就会为你执行所有构建过程。

现在，请将代码推送到 **NewUpdate** 分支。 在 Git Shell 中运行以下命令：

    git checkout NewUpdate
    git pull origin NewUpdate
    git merge Dev
    git push origin NewUpdate

就这么简单！ 

转到测试环境的 Web 应用边栏选项卡，以查看新提交是否已推送到测试环境（合并到 NewUpdate 分支）。 然后，单击“浏览”查看样式更改是否正在 Azure 中实时运行。

## <a name="deploy-update-to-production"></a>将更新部署到生产环境
将代码推送到过渡和生产环境的过程类似于前面将代码所推送到测试环境。 真的就这么简单。 

在 Git Shell 中运行以下命令：

    git checkout master
    git pull origin master
    git merge NewUpdate
    git push origin master

请记住，根据在 ProdandStage.json 中设置过渡和生产环境的方式，新代码将推送到“过渡”槽，并在该处运行。 因此，如果你导航到过渡槽的 URL，则会看到新代码正在该处运行。 为此，请在 Git Shell 中运行 `Show-AzureWebsite` cmdlet。

    Show-AzureWebsite -Name ToDoApp<unique_string>master -Slot Staging

现在，在过渡槽中验证更新之后，唯一要做的就是将它交换到生产环境。 在 Git Shell 中，只要运行以下命令：

    cd <repository_root>\ARMTemplates
    .\swap.ps1 -Name ToDoApp<unique_string>master

祝贺你！ 你已成功地将新的更新发布到生产 Web 应用程序。 不仅如此，完成的方式也只是轻松地创建开发和测试环境，以及构建和测试每项提交。 这些是敏捷软件开发的重要构建块。

<a name="delete"></a>

## <a name="delete-dev-and-test-enviroments"></a>删除开发和测试环境
由于你特意将开发和测试环境构建为独立的资源组，因此可以很容易删除它们。 若要删除你在本教程中创建的环境（GitHub 分支和 Azure 项目），只需在 Git Shell 中运行以下命令：

    git branch -d Dev
    git push origin :Dev
    git branch -d NewUpdate
    git push origin :NewUpdate
    Remove-AzureRmResourceGroup -Name ToDoApp<unique_string>dev-group -Force -Verbose
    Remove-AzureRmResourceGroup -Name ToDoApp<unique_string>newupdate-group -Force -Verbose

## <a name="summary"></a>摘要
对于许多想要采用 Azure 作为其应用程序平台的公司而言，敏捷软件开发是必不可少的。 在本教程中，你已学习如何轻松创建和删除生产环境的确切副本或近似副本，即使对于复杂应用程序也是一样。 你还学习了如何利用此功能创建开发过程，以便在 Azure 中构建和测试每项提交。 本教程旨在演示如何最恰当地将 Azure App Service 和 Azure 资源管理器配合使用，以创建提供灵便方法的 DevOps 解决方案。 接下来，可以通过执行高级 DevOps 技术（例如[在生产环境中测试](app-service-web-test-in-production-get-start.md)），基于此方案生成项目。 有关常用的生产中测试方案，请参阅 [Azure App Service 中的外部测试部署（Beta 测试）](app-service-web-test-in-production-controlled-test-flight.md)。

## <a name="more-resources"></a>更多资源
* [通过可预测的方式在 Azure 中部署复杂应用程序](app-service-deploy-complex-application-predictably.md)
* [Agile Development in Practice: Tips and Tricks for Modernized Development Cycle](http://channel9.msdn.com/Events/Ignite/2015/BRK3707)（敏捷开发实践：有关现代化开发周期的提示和技巧）
* [Advanced deployment strategies for Azure Web Apps using Resource Manager templates](http://channel9.msdn.com/Events/Build/2015/2-620)（使用资源管理器模板为 Azure Web 应用制定高级部署策略）
* [创作 Azure Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md)
* [JSONLint - JSON 验证程序](http://jsonlint.com/)
* [ARMClient - 设置从 GitHub 到站点的发布](https://github.com/projectKudu/ARMClient/wiki/Setup-GitHub-publishing-to-Site)
* [Git 分支 - 基本分支和合并](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
* [David Ebbo 的博客](http://blog.davidebbo.com/)
* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Azure 跨平台命令行工具](../xplat-cli-install.md)
* [在 Azure AD 中创建或编辑用户](https://msdn.microsoft.com/library/azure/hh967632.aspx#BKMK_1)
* [项目 Kudu Wiki](https://github.com/projectkudu/kudu/wiki)


