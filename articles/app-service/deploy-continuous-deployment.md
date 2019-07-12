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
ms.date: 06/18/2019
ms.author: cephalin;dariagrigoriu
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 3a207f5513c24f2c33571df929f7490ec7f2eba5
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836825"
---
# <a name="continuous-deployment-to-azure-app-service"></a>持续部署到 Azure 应用服务

[Azure 应用服务](overview.md)启用持续部署从 GitHub、 BitBucket 和[Azure 存储库](https://azure.microsoft.com/services/devops/repos/)提取最新的更新存储库。 本文介绍如何使用 Azure 门户可连续部署通过 Kudu 生成服务对应用程序或[Azure 管道](https://azure.microsoft.com/services/devops/pipelines/)。 

源代码管理服务的详细信息，请参阅[创建存储库 (GitHub)]，[创建存储库 (BitBucket)]，或[创建新的 Git 存储库 （Azure 存储库）]。

若要手动配置门户并不直接支持，如的云存储库持续部署[GitLab](https://gitlab.com/)，请参阅[设置连续部署使用手动步骤](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)。

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>授权 Azure 应用服务 

若要使用 Azure 存储库，请确保你的 Azure DevOps 组织链接到你的 Azure 订阅。 有关详细信息，请参阅[设置 Azure DevOps 服务帐户，以便它可以部署到 web 应用](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)。

对于 Bitbucket 或 GitHub，授权 Azure 应用服务以连接到你的存储库。 只需一次授权与源代码管理服务。 

1. 选择**应用服务**中[Azure 门户](https://portal.azure.com)左导航窗格中，并选择想要部署的 web 应用。 
   
1. 在应用程序页上，选择**部署中心**在左侧菜单中。
   
1. 上**部署中心**页上，选择**GitHub**或**Bitbucket**，然后选择**Authorize**。 
   
   ![选择源代码管理服务，然后选择授权。](media/app-service-continuous-deployment/github-choose-source.png)
   
1. 登录到服务，如有必要，并按照授权提示进行操作。 

## <a name="enable-continuous-deployment"></a>启用持续部署 

授权源代码管理服务后，将应用配置为通过内置持续部署[Kudu 应用服务生成服务器](#option-1-use-the-app-service-build-service)，或通过[Azure 管道](#option-2-use-azure-pipelines)。 

### <a name="option-1-use-the-app-service-build-service"></a>选项 1：使用应用服务生成服务

可以使用的内置的 Kudu 应用服务中的 GitHub、 Bitbucket 或 Azure 存储库生成服务器可连续部署。 

1. 选择**应用服务**中[Azure 门户](https://portal.azure.com)左导航窗格中，并选择想要部署的 web 应用。 
   
1. 在应用程序页上，选择**部署中心**在左侧菜单中。
   
1. 选择你已授权的源代码管理提供程序上**部署中心**页，然后选择**继续**。 对于 GitHub 或 Bitbucket，还可以选择**更改帐户**更改授权的帐户。 
   
   > [!NOTE]
   > 若要使用 Azure 存储库，请确保你的 Azure DevOps 服务组织链接到你的 Azure 订阅。 有关详细信息，请参阅[设置 Azure DevOps 服务帐户，以便它可以部署到 web 应用](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)。
   
1. GitHub 或 Azure 存储库上**生成提供程序**页上，选择**应用服务构建服务**，然后选择**继续**。 Bitbucket 始终使用应用服务生成服务。
   
   ![选择应用服务生成服务，然后选择继续。](media/app-service-continuous-deployment/choose-kudu.png)
   
1. 上**配置**页：
   
   - Github，下拉列表中，选择**组织**，**存储库**，并**分支**你想要连续部署。
     
     > [!NOTE]
     > 如果看不到任何存储库，您可能需要授权在 GitHub 中的 Azure 应用服务。 浏览到你的 GitHub 存储库并转到**设置** > **应用程序** > **授权 OAuth 应用**。 选择**Azure 应用服务**，然后选择**授予**。
     
   - 对于 Bitbucket，请选择 Bitbucket**团队**，**存储库**，并**分支**你想要连续部署。
     
   - 针对 Azure 存储库中，选择**Azure 开发运营组织**，**项目**，**存储库**，以及**分支**你想要连续部署。
     
     > [!NOTE]
     > 如果未列出你的 Azure DevOps 组织，请确保链接到你的 Azure 订阅。 有关详细信息，请参阅[设置 Azure DevOps 服务帐户，以便它可以部署到 web 应用](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)...
     
1. 选择**继续**。
   
   ![填写存储库的信息，然后选择继续。](media/app-service-continuous-deployment/configure-kudu.png)
   
1. 配置生成提供程序后，查看设置上**摘要**页上，并选择**完成**。
   
   中的所选存储库和分支的新提交现在应用服务应用到连续部署。 可以在跟踪的提交和部署**部署中心**页。
   
   ![跟踪提交和部署中心中的部署](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>选项 2：使用 Azure Pipelines 

如果你的帐户具有所需的权限，您可以设置 Azure 管道可从 GitHub 或 Azure 存储库存储库连续部署。 有关通过 Azure 管道部署的详细信息，请参阅[的 web 应用部署到 Azure 应用服务](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)。

若要创建 Azure DevOps 组织中的持续交付 Azure 管道的 Azure 应用服务： 

- 你的 Azure 帐户必须有权写入到 Azure Active Directory 并创建一个服务。 
  
- 你的 Azure 帐户必须具有**所有者**在 Azure 订阅中的角色。

- 必须是你想要使用 Azure DevOps 项目中的管理员。

若要配置 Azure 管道 （预览版）：

1. 选择**应用服务**中[Azure 门户](https://portal.azure.com)左导航窗格中，并选择想要部署的 web 应用。 
   
1. 在应用程序页上，选择**部署中心**在左侧菜单中。
   
1. 上**生成提供程序**页上，选择**Azure 管道 （预览版）** ，然后选择**继续**。 
   
1. 上**配置**页上，在**代码**部分：
   
   - Github，下拉列表中，选择**组织**，**存储库**，并**分支**你想要连续部署。
     
     > [!NOTE]
     > 如果看不到任何存储库，您可能需要授权在 GitHub 中的 Azure 应用服务。 浏览到你的 GitHub 存储库并转到**设置** > **应用程序** > **授权 OAuth 应用**。 选择**Azure 应用服务**，然后选择**授予**。
     
   - 针对 Azure 存储库中，选择**Azure 开发运营组织**，**项目**，**存储库**，以及**分支**你想要连续部署或配置新的 Azure DevOps 组织。
     
     > [!NOTE]
     > 如果未列出你现有的 Azure DevOps 组织，可能需要将其链接到你的 Azure 订阅。 有关详细信息，请参阅[定义 CD 发布管道](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)。
     
1. 选择**继续**。
   
1. 针对 Azure 存储库中**构建**部分中，指定 Azure 管道应使用以运行生成任务，然后选择的语言框架**继续**。
   
1. 上**测试**页上，选择是否要启用负载测试，然后选择**继续**。
   
1. 具体取决于你的应用服务计划[定价层](https://azure.microsoft.com/pricing/details/app-service/plans/)，可能会看到**部署到过渡环境**页。 选择是否[启用部署槽](deploy-staging-slots.md)，然后选择**继续**。
   
   > [!NOTE]
   > Azure 管道不允许持续交付到生产槽。 此限制可以防止意外部署到生产环境。 设置持续交付到过渡槽，验证所做的更改，并准备就绪后，然后交换槽。
   
1. 配置生成提供程序后，查看设置上**摘要**页上，并选择**完成**。
   
   中的所选存储库和分支的新提交现在应用服务应用到连续部署。 可以在跟踪的提交和部署**部署中心**页。
   
   ![跟踪提交和部署中心中的部署](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>禁用持续部署

若要禁用连续部署，请选择**断开连接**应用程序的顶部**部署中心**页。

![禁用持续部署](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>其他资源

* [调查持续部署的常见问题](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [使用 Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Git 文档](https://git-scm.com/documentation)
* [项目 Kudu](https://github.com/projectkudu/kudu/wiki)

[创建存储库 (GitHub)]: https://help.github.com/articles/create-a-repo
[创建存储库 (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[创建新的 Git 存储库 （Azure 存储库）]: /azure/devops/repos/git/creatingrepo
