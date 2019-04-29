---
title: 持续部署 - Azure 应用服务 | Microsoft Docs
description: 了解如何实现连续部署到 Azure 应用服务。
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
ms.date: 12/03/2018
ms.author: cephalin;dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: fcb2c270b36d5efbe7b799787cf2a123b51bea5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765566"
---
# <a name="continuous-deployment-to-azure-app-service"></a>持续部署到 Azure 应用服务
本文展示了如何为 [Azure 应用服务](overview.md)配置持续部署。 应用服务可以使用 BitBucket、GitHub 和 [Azure DevOps Services](https://www.visualstudio.com/team-services/) 实现持续部署，方法是从上述服务之一中的现有存储库拉取最新的更新。

若要了解如何通过 Azure 门户中未列出的云存储库（如 [GitLab](https://gitlab.com/)）手动配置持续部署，请参阅[使用手动步骤设置持续部署](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)。

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

将准备好的存储库发布到受支持的服务之一。 若要深入了解如何将项目发布到这些服务，请参阅[创建存储库 (GitHub)]、[创建存储库 (BitBucket)] 和 [Azure DevOps Services 入门]。

## <a name="deploy-continuously-from-github"></a>从 GitHub 持续部署

若要使用 GitHub 实现持续部署，请在 [Azure 门户](https://portal.azure.com)中导航至你的应用服务应用页。

在左侧菜单中，单击“部署中心” > “GitHub” > “授权”。 按照授权提示进行操作。 

![](media/app-service-continuous-deployment/github-choose-source.png)

只需要通过 GitHub 授权一次。 如果已获得授权，只需单击“继续”即可。 可以通过单击“更改帐户”来更改已获得授权的 GitHub 帐户。

![](media/app-service-continuous-deployment/github-continue.png)

在“生成提供程序”页面中，选择生成提供程序并单击 >“继续”。

### <a name="option-1-use-app-service-kudu-build-server"></a>选项 1：使用应用服务 Kudu 生成服务器

在“配置”页面中，选择要从中进行持续部署的组织、存储库和分支。 完成后，单击“继续”。

若要部署从 GitHub 组织中的存储库，浏览到 GitHub 并转到**设置** > **应用程序** > **授权 OAuth 应用**. 然后，单击"Azure 应用服务"。

![设置 > 应用程序 > 经授权 OAuth 应用 > Azure 应用服务](media/app-service-continuous-deployment/github-settings-navigation.png)

在下一步的页中，通过单击右侧的"授予"按钮授予对组织的存储库的应用服务访问。

![单击"授予"授予对组织的存储库的应用服务访问权限](media/app-service-continuous-deployment/grant-access.png)

你的组织现在应显示的"组织"列表中**配置**部署中心页。

### <a name="option-2-use-azure-pipelines-preview"></a>选项 2：使用 Azure Pipelines（预览版）

> [!NOTE]
> 若要让应用服务在 Azure DevOps Services 组织中创建所需的 Azure 管道，Azure 帐户必须在 Azure 订阅中具有“所有者”角色。
>

在“配置”页面中，在“代码”部分中，选择要从中进行持续部署的组织、存储库和分支。 完成后，单击“继续”。

在“配置”页的“生成”部分，配置新的 Azure DevOps Services 组织或指定一个现有组织。 完成后，单击“继续”。

> [!NOTE]
> 如果想要使用未列出的现有 Azure DevOps Services 组织，则需要[将 Azure DevOps Services 组织链接到 Azure 订阅](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)。

在“测试”页面中，选择是否要启用负载测试，然后单击“继续”。

根据应用服务计划的[定价层](https://azure.microsoft.com/pricing/details/app-service/plans/)，可能还会看到“部署到过渡环境”页。 选择是否[要启用部署槽位](deploy-staging-slots.md)，然后单击“继续”。

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

## <a name="deploy-continuously-from-azure-repos-devops-services"></a>从 Azure Repos (DevOps Services) 进行持续部署

若要通过 [Azure Repos](https://docs.microsoft.com/azure/devops/repos/index) 实现持续部署，请在 [Azure 门户](https://portal.azure.com)中导航至应用服务应用页。

在左侧菜单中，单击“部署中心” > “Azure Repos” > “继续”。 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

在“生成提供程序”页面中，选择生成提供程序并单击 >“继续”。

> [!NOTE]
> 如果想要使用未列出的现有 Azure DevOps Services 组织，则需要[将 Azure DevOps Services 组织链接到 Azure 订阅](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)。

### <a name="option-1-use-app-service-kudu-build-server"></a>选项 1：使用应用服务 Kudu 生成服务器

在“配置”页面中，选择要从中进行持续部署的 Azure DevOps Services 组织、项目、存储库和分支。 完成后，单击“继续”。

### <a name="option-2-use-azure-devops-services-continuous-delivery"></a>选项 2：使用 Azure DevOps Services 持续交付

> [!NOTE]
> 若要让应用服务在 Azure DevOps Services 组织中创建所需的 Azure 管道，Azure 帐户必须在 Azure 订阅中具有“所有者”角色。
>

在“配置”页的“代码”部分，选择要从中进行持续部署的 Azure DevOps Services 组织、项目、存储库和分支。 完成后，单击“继续”。

在“配置”页的“生成”部分，指定 Azure DevOps Services 在为选定的存储库运行生成任务时应当使用的语言框架。 完成后，单击“继续”。

在“测试”页面中，选择是否要启用负载测试，然后单击“继续”。

根据应用服务计划的[定价层](https://azure.microsoft.com/pricing/details/app-service/plans/)，可能还会看到“部署到过渡环境”页。 选择是否[要启用部署槽位](deploy-staging-slots.md)，然后单击“继续”。 

### <a name="finish-configuration"></a>完成配置

在“摘要”页中，确认选项，然后单击“完成”。

在配置完成后，选定存储库中的新提交将持续部署到应用服务应用。

## <a name="disable-continuous-deployment"></a>禁用持续部署

若要禁用持续部署，请在 [Azure 门户](https://portal.azure.com)中导航至你的应用服务应用页。

在左侧菜单中，单击“部署中心” > “GitHub”、“Azure DevOps Services”或“BitBucket” > “断开连接”。

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>其他资源

* [How to investigate common issues with continuous deployment](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)（如何调查连续部署的常见问题）
* [如何使用适用于 Azure 的 PowerShell]
* [Git 文档]
* [项目 Kudu](https://github.com/projectkudu/kudu/wiki)
* [Use Azure to automatically generate a CI/CD pipeline to deploy an ASP.NET 4 app](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)（使用 Azure 自动生成部署 ASP.NET 4 应用的 CI/CD 管道）

[Azure portal]: https://portal.azure.com
[Azure DevOps portal]: https://azure.microsoft.com/services/devops/
[Installing Git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
[如何使用适用于 Azure 的 PowerShell]: /powershell/azureps-cmdlets-docs
[Git 文档]: https://git-scm.com/documentation

[创建存储库 (GitHub)]: https://help.github.com/articles/create-a-repo
[创建存储库 (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Azure DevOps Services 入门]: https://docs.microsoft.com/azure/devops/user-guide/devops-alm-overview
