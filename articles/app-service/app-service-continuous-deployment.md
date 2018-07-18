---
title: 持续部署到 Azure 应用服务 | Microsoft Docs
description: 了解如何实现持续部署到 Azure 应用服务。
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: cephalin;dariagrigoriu
ms.openlocfilehash: d83d1ad74d04356f73f18a744c2d1509b5efc280
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233838"
---
# <a name="continuous-deployment-to-azure-app-service"></a>持续部署到 Azure 应用服务
本文展示了如何为 [Azure 应用服务](app-service-web-overview.md)配置持续部署。 应用服务可以使用 BitBucket、GitHub 和 [Visual Studio Team Services (VSTS)](https://www.visualstudio.com/team-services/) 实现持续部署，方法是从上述服务之一中的现有存储库拉取最新的更新。

若要了解如何通过 Azure 门户中未列出的云存储库（如 [GitLab](https://gitlab.com/)）手动配置持续部署，请参阅[使用手动步骤设置持续部署](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)。

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

将准备好的存储库发布到受支持的服务之一。 要深入了解如何将项目发布到这些服务，请参阅[创建存储库 (GitHub)]、[创建存储库 (BitBucket)] 和 [VSTS 入门]。

## <a name="deploy-continuously-from-github"></a>从 GitHub 持续部署

若要使用 GitHub 实现持续部署，请在 [Azure 门户](https://portal.azure.com)中导航至你的应用服务应用页。

在左侧菜单中，单击“部署中心” > “GitHub” > “授权”。 按照授权提示进行操作。 

![](media/app-service-continuous-deployment/github-choose-source.png)

只需要通过 GitHub 授权一次。 如果已获得授权，只需单击“继续”即可。 可以通过单击“更改帐户”来更改已获得授权的 GitHub 帐户。

![](media/app-service-continuous-deployment/github-continue.png)

在“生成提供程序”页面中，选择生成提供程序并单击 >“继续”。

### <a name="option-1-use-app-service-kudu-build-server"></a>选项 1：使用应用服务 Kudu 生成服务器

在“配置”页面中，选择要从中进行持续部署的组织、存储库和分支。 完成后，单击“继续”。

### <a name="option-2-use-vsts-continuous-delivery"></a>选项 2：使用 VSTS 持续交付

> [!NOTE]
> 要让应用服务在 VSTS 帐户中创建必要的生成和发布定义，Azure 帐户必须在 Azure 订阅中具有“所有者”角色。
>

在“配置”页面中，在“代码”部分中，选择要从中进行持续部署的组织、存储库和分支。 完成后，单击“继续”。

在“配置”页面中，在“生成”部分中，配置新的 VSTS 帐户，或指定一个现有帐户。 完成后，单击“继续”。

> [!NOTE]
> 如果想要使用未列出的现有 VSTS 帐户，则需要[将 VSTS 帐户链接到 Azure 订阅](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)。

在“测试”页面中，选择是否要启用负载测试，然后单击“继续”。

根据应用服务计划的[定价层](/pricing/details/app-service/plans/)，可能还会看到“部署到过渡环境”页。 选择是否[要启用部署槽位](web-sites-staged-publishing.md)，然后单击“继续”。

### <a name="finish-configuration"></a>完成配置

在“摘要”页中，确认选项，然后单击“完成”。

在配置完成后，选定存储库中的新提交将持续部署到应用服务应用。

![](media/app-service-continuous-deployment/github-finished.png)

## <a name="deploy-continuously-from-bitbucket"></a>从 BitBucket 持续部署

若要使用 BitBucket 实现持续部署，请在 [Azure 门户](https://portal.azure.com)中导航至你的应用服务应用页。

在左侧菜单中，单击“部署中心” > “BitBucket” > “授权”。 按照授权提示进行操作。 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

只需要通过 BitBucket 授权一次。 如果已获得授权，只需单击“继续”即可。 可以通过单击“更改帐户”来更改已获得授权的 BitBucket 帐户。

![](media/app-service-continuous-deployment/bitbucket-continue.png)

在“配置”页面中，选择要从中进行持续部署的存储库和分支。 完成后，单击“继续”。

在“摘要”页中，确认选项，然后单击“完成”。

在配置完成后，选定存储库中的新提交将持续部署到应用服务应用。

## <a name="deploy-continuously-from-vsts"></a>从 VSTS 持续部署

若要使用 VSTS 实现持续部署，请在 [Azure 门户](https://portal.azure.com)中导航至你的应用服务应用页。

在左侧菜单中，单击“部署中心” > “VSTS” > “继续”。 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

在“生成提供程序”页面中，选择生成提供程序并单击 >“继续”。

### <a name="option-1-use-app-service-kudu-build-server"></a>选项 1：使用应用服务 Kudu 生成服务器

在“配置”页面中，选择要从中进行持续部署的 VSTS 帐户、项目、存储库和分支。 完成后，单击“继续”。

### <a name="option-2-use-vsts-continuous-delivery"></a>选项 2：使用 VSTS 持续交付

> [!NOTE]
> 要让应用服务在 VSTS 帐户中创建必要的生成和发布定义，Azure 帐户必须在 Azure 订阅中具有“所有者”角色。
>

在“配置”页面中，在“代码”部分中，选择要从中进行持续部署的 VSTS 帐户、项目、存储库和分支。 完成后，单击“继续”。

> [!NOTE]
> 如果想要使用未列出的现有 VSTS 帐户，则需要[将 VSTS 帐户链接到 Azure 订阅](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)。

在“配置”页中，在“生成”部分中，指定 VSTS 在为选定的存储库运行生成任务时应当使用的语言框架。 完成后，单击“继续”。

在“测试”页面中，选择是否要启用负载测试，然后单击“继续”。

根据应用服务计划的[定价层](/pricing/details/app-service/plans/)，可能还会看到“部署到过渡环境”页。 选择是否[要启用部署槽位](web-sites-staged-publishing.md)，然后单击“继续”。 

### <a name="finish-configuration"></a>完成配置

在“摘要”页中，确认选项，然后单击“完成”。

在配置完成后，选定存储库中的新提交将持续部署到应用服务应用。

## <a name="disable-continuous-deployment"></a>禁用持续部署

若要禁用持续部署，请在 [Azure 门户](https://portal.azure.com)中导航至你的应用服务应用页。

在左侧菜单中，单击“部署中心” > “GitHub”、“VSTS”或“BitBucket” > “断开连接”。

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>其他资源

* [How to investigate common issues with continuous deployment](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)（如何调查持续部署的常见问题）
* [如何使用适用于 Azure 的 PowerShell]
* [Git 文档]
* [项目 Kudu](https://github.com/projectkudu/kudu/wiki)
* [Use Azure to automatically generate a CI/CD pipeline to deploy an ASP.NET 4 app](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)（使用 Azure 自动生成部署 ASP.NET 4 应用的 CI/CD 管道）

[Azure portal]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/en-us/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[如何使用适用于 Azure 的 PowerShell]: /powershell/azureps-cmdlets-docs
[Git 文档]: http://git-scm.com/documentation

[创建存储库 (GitHub)]: https://help.github.com/articles/create-a-repo
[创建存储库 (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[VSTS 入门]: https://www.visualstudio.com/docs/vsts-tfs-overview
