---
title: 配置持续部署
description: 了解如何启用从 GitHub、BitBucket、Azure Repos 或其他存储库 Azure App Service 的 CI/CD。 选择满足你的需求的生成管道。
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 08/23/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: d58eb333c930d2ffac4eb57340ea776338325181
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266034"
---
# <a name="continuous-deployment-to-azure-app-service"></a>持续部署到 Azure 应用服务

[Azure App Service](overview.md)可以通过拉取最新的更新，从 GitHub、BitBucket 和[Azure Repos](https://azure.microsoft.com/services/devops/repos/)存储库进行连续部署。 本文介绍如何使用 Azure 门户通过 Kudu 生成服务或[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)持续部署应用。 

有关源代码管理服务的详细信息，请参阅[创建存储库 (GitHub)]、[创建存储库 (BitBucket)]或[创建新的 Git 存储库（Azure Repos）]。

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>授权 Azure App Service 

若要使用 Azure Repos，请确保 Azure DevOps 组织已链接到 Azure 订阅。 有关详细信息，请参阅[设置 Azure DevOps Services 帐户，以便它可以部署到 web 应用](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)。

对于 Bitbucket 或 GitHub，授权 Azure App Service 连接到存储库。 只需使用源代码管理服务进行一次授权。 

1. 在[Azure 门户](https://portal.azure.com)中，搜索并选择 "**应用服务**"。 

   ![搜索应用服务。](media/app-service-continuous-deployment/search-for-app-services.png)

1. 选择要部署的 web 应用。

   ![选择应用。](media/app-service-continuous-deployment/select-your-app.png)
   
1. 在应用页的左侧菜单中，选择 "**部署中心**"。
   
1. 在 "**部署中心**" 页上，选择 " **GitHub** " 或 " **Bitbucket**"，然后选择 "**授权**"。 
   
   ![选择 "源代码管理" 服务，然后选择 "授权"。](media/app-service-continuous-deployment/github-choose-source.png)
   
1. 如有必要，请登录到服务，并按照授权提示进行操作。 

## <a name="enable-continuous-deployment"></a>启用持续部署 

授权源代码管理服务后，通过内置的[Kudu 应用服务生成服务器](#option-1-use-the-app-service-build-service)或[Azure Pipelines](#option-2-use-azure-pipelines)，将应用配置为连续部署。 

### <a name="option-1-use-the-app-service-build-service"></a>选项1：使用应用服务生成服务

你可以使用内置的 Kudu 应用服务生成服务器从 GitHub、Bitbucket 或 Azure Repos 持续进行部署。 

1. 在[Azure 门户](https://portal.azure.com)中，搜索并选择 "**应用服务**"，然后选择要部署的 web 应用。 
   
1. 在应用页的左侧菜单中，选择 "**部署中心**"。
   
1. 在 "**部署中心**" 页上选择授权的源代码管理提供程序，并选择 "**继续**"。 对于 GitHub 或 Bitbucket，还可以选择 "**更改帐户**" 以更改授权帐户。 
   
   > [!NOTE]
   > 若要使用 Azure Repos，请确保 Azure DevOps Services 组织已链接到 Azure 订阅。 有关详细信息，请参阅[设置 Azure DevOps Services 帐户，以便它可以部署到 web 应用](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)。
   
1. 对于 GitHub 或 Azure Repos，请在 "**生成提供程序**" 页上选择 "**应用服务生成服务**"，然后选择 "**继续**"。 Bitbucket 始终使用应用服务生成服务。
   
   ![选择 "应用服务生成服务"，并选择 "继续"。](media/app-service-continuous-deployment/choose-kudu.png)
   
1. 在 "**配置**" 页上：
   
   - 对于 GitHub，请下拉菜单，然后选择要连续部署的**组织**、**存储库**和**分支**。
     
     > [!NOTE]
     > 如果看不到任何存储库，可能需要在 GitHub 中授权 Azure App Service。 浏览到 GitHub 存储库，转到 "**设置**" > **应用程序**" > **授权 OAuth 应用**"。 选择 " **Azure App Service**"，然后选择 "**授予**"。 对于组织存储库，你必须是组织的所有者才能授予权限。
     
   - 对于 Bitbucket，选择要连续部署的 Bitbucket**团队**、**存储库**和**分支**。
     
   - 对于 "Azure Repos"，请选择要连续部署的**Azure DevOps 组织**、**项目**、**存储库**和**分支**。
     
     > [!NOTE]
     > 如果 Azure DevOps 组织未列出，请确保它已链接到 Azure 订阅。 有关详细信息，请参阅[设置 Azure DevOps Services 帐户，以便它可以部署到 web 应用](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)。
     
1. 选择“继续”。
   
   ![填写 "存储库信息"，然后选择 "继续"。](media/app-service-continuous-deployment/configure-kudu.png)
   
1. 配置生成提供程序之后，查看 "**摘要**" 页上的设置，然后选择 "**完成**"。
   
   所选存储库和分支中的新提交现在会连续部署到应用服务应用。 可以在 "**部署中心**" 页上跟踪提交和部署。
   
   ![在部署中心跟踪提交和部署](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>选项2：使用 Azure Pipelines 

如果帐户具有所需的权限，则可以设置 Azure Pipelines 从 GitHub 或 Azure Repos 存储库持续部署。 有关通过 Azure Pipelines 部署的详细信息，请参阅[将 web 应用部署到 Azure 应用服务](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)。

对于在 Azure DevOps 组织中创建持续交付 Azure Pipelines 的 Azure App Service： 

- 你的 Azure 帐户必须具有写入 Azure Active Directory 和创建服务的权限。 
  
- 你的 Azure 帐户必须拥有 Azure 订阅中的 "**所有者**" 角色。

- 你必须是要使用的 Azure DevOps 项目中的管理员。

配置 Azure Pipelines （预览版）：

1. 在[Azure 门户](https://portal.azure.com)中，搜索并选择 "**应用服务**"，然后选择要部署的 web 应用。 
   
1. 在应用页的左侧菜单中，选择 "**部署中心**"。
   
1. 在 "**生成提供程序**" 页上，选择 " **Azure Pipelines （预览）** "，然后选择 "**继续**"。 
   
1. 在 "**配置**" 页上的 "**代码**" 部分中：
   
   - 对于 GitHub，请下拉菜单，然后选择要连续部署的**组织**、**存储库**和**分支**。
     
     > [!NOTE]
     > 如果看不到任何存储库，可能需要在 GitHub 中授权 Azure App Service。 浏览到 GitHub 存储库，转到 "**设置**" > **应用程序**" > **授权 OAuth 应用**"。 选择 " **Azure App Service**"，然后选择 "**授予**"。 对于组织存储库，你必须是组织的所有者才能授予权限。
     
   - 对于 "Azure Repos"，请选择要连续部署的**Azure DevOps 组织**、**项目**、**存储库**和**分支**，或配置新的 azure DevOps 组织。
     
     > [!NOTE]
     > 如果未列出现有的 Azure DevOps 组织，可能需要将其链接到 Azure 订阅。 有关详细信息，请参阅[定义 CD 发布管道](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)。
     
1. 选择“继续”。
   
1. 对于 "Azure Repos"，请在 "**生成**" 部分中指定 Azure Pipelines 应用于运行生成任务的语言框架，然后选择 "**继续**"。
   
1. 在 "**测试**" 页上，选择是否要启用负载测试，然后选择 "**继续**"。
   
1. 根据你的应用服务计划[定价层](https://azure.microsoft.com/pricing/details/app-service/plans/)，你可能会看到 "**部署到过渡**" 页。 选择是否[启用部署槽位](deploy-staging-slots.md)，然后选择 "**继续**"。
   
   > [!NOTE]
   > Azure Pipelines 不允许持续交付到生产槽。 此限制可防止意外部署到生产环境。 设置到过渡槽的连续交付，验证是否存在更改，然后在准备就绪时交换槽。
   
1. 配置生成提供程序之后，查看 "**摘要**" 页上的设置，然后选择 "**完成**"。
   
   所选存储库和分支中的新提交现在会连续部署到应用服务应用。 可以在 "**部署中心**" 页上跟踪提交和部署。
   
   ![在部署中心跟踪提交和部署](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>禁用持续部署

若要禁用连续部署，请在应用的**部署中心**页面顶部选择 "**断开连接**"。

![禁用持续部署](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>使用不支持的存储库

对于 Windows 应用，你可以从门户不直接支持的云 Git 或 Mercurial 存储库手动配置连续部署，如[GitLab](https://gitlab.com/)。 可以通过在 "**部署中心**" 页中选择 "外部" 框来执行此操作。 有关详细信息，请参阅[使用手动步骤设置连续部署](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)。

## <a name="additional-resources"></a>其他资源

* [调查连续部署的常见问题](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [使用 Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Git 文档](https://git-scm.com/documentation)
* [项目 Kudu](https://github.com/projectkudu/kudu/wiki)

[创建存储库 (GitHub)]: https://help.github.com/articles/create-a-repo
[创建存储库 (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[创建新的 Git 存储库（Azure Repos）]: /azure/devops/repos/git/creatingrepo
