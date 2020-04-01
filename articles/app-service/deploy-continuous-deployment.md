---
title: 配置持续部署
description: 了解如何从 GitHub、Bit Bucket、Azure 存储库或其他存储库将 CI/CD 启用 Azure 应用服务。 选择适合您需求的生成管道。
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 847de2c2c8916558d542473d9b7c80fd5552dbf7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437229"
---
# <a name="continuous-deployment-to-azure-app-service"></a>持续部署到 Azure 应用服务

[Azure 应用服务](overview.md)通过提取最新更新，支持从 GitHub、Bit Bucket 和[Azure 存储库](https://azure.microsoft.com/services/devops/repos/)存储库进行连续部署。 本文介绍如何使用 Azure 门户通过 Kudu 生成服务或[Azure 管道](https://azure.microsoft.com/services/devops/pipelines/)持续部署应用。 

有关源代码管理服务的详细信息，请参阅[创建存储库 （GitHub）、][创建存储库（Bit Bucket）]或[创建新的 Git 存储库（Azure 存储库）。]

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>授权 Azure 应用服务 

要使用 Azure 存储库，请确保 Azure DevOps 组织已链接到 Azure 订阅。 有关详细信息，请参阅设置[Azure DevOps 服务帐户，以便它可以部署到 Web 应用](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)。

对于 Bitbucket 或 GitHub，授权 Azure 应用服务连接到存储库。 您只需使用源代码管理服务授权一次。 

1. 在[Azure 门户](https://portal.azure.com)中，搜索**应用服务**并选择。

   ![搜索应用服务。](media/app-service-continuous-deployment/search-for-app-services.png)

1. 选择要部署的应用服务。

   ![选择你的应用。](media/app-service-continuous-deployment/select-your-app.png)
   
1. 在应用页面上，选择左侧菜单中的 **"部署中心**"。
   
1. 在 **"部署中心**"页上，选择**GitHub**或**Bitbucket，** 然后选择 **"授权**"。 
   
   ![选择源代码管理服务，然后选择"授权"。](media/app-service-continuous-deployment/github-choose-source.png)
   
1. 如有必要，登录到服务，然后按照授权提示进行操作。 

## <a name="enable-continuous-deployment"></a>启用持续部署 

授权源代码管理服务后，请配置应用，以便通过内置[Kudu 应用服务](#option-1-kudu-app-service)生成服务器或通过[Azure 管道](#option-2-azure-pipelines)进行持续部署。 

### <a name="option-1-kudu-app-service"></a>选项 1：库杜应用服务

您可以使用内置库杜应用服务生成服务器从 GitHub、Bitbucket 或 Azure 存储库持续部署。 

1. 在[Azure 门户](https://portal.azure.com)中，搜索**应用服务**，然后选择要部署的应用服务。 
   
1. 在应用页面上，选择左侧菜单中的 **"部署中心**"。
   
1. 在 **"部署中心**"页上选择您的授权源代码管理提供程序，然后选择"**继续**"。 对于 GitHub 或 Bitbucket，您还可以选择 **"更改帐户"** 以更改授权帐户。 
   
   > [!NOTE]
   > 要使用 Azure 存储库，请确保 Azure DevOps 服务组织已链接到 Azure 订阅。 有关详细信息，请参阅设置[Azure DevOps 服务帐户，以便它可以部署到 Web 应用](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)。
   
1. 对于 GitHub 或 Azure 存储库，在**生成提供程序**页上，选择**应用服务生成服务**，然后选择"**继续**"。 Bitbucket 始终使用应用服务生成服务。
   
   ![选择应用服务生成服务，然后选择"继续"。](media/app-service-continuous-deployment/choose-kudu.png)
   
1. 在 **"配置"** 页上：
   
   - 对于 GitHub，下拉并选择要连续部署**的组织**、**存储库**和**分支**。
     
     > [!NOTE]
     > 如果看不到任何存储库，则可能需要在 GitHub 中授权 Azure 应用服务。 浏览到您的 GitHub 存储库，然后转到**设置** > **应用程序** > **授权 OAuth 应用程序**。 选择**Azure 应用服务**，然后选择 **"授予**"。 对于组织存储库，您必须是组织的所有者才能授予权限。
     
   - 对于 Bitbucket，选择要连续部署的 Bitbucket**团队**、**存储库**和**分支**。
     
   - 对于 Azure 存储库，选择要连续部署的**Azure DevOps 组织**、**项目**、**存储库**和**分支**。
     
     > [!NOTE]
     > 如果未列出 Azure DevOps 组织，请确保它链接到 Azure 订阅。 有关详细信息，请参阅设置[Azure DevOps 服务帐户，以便它可以部署到 Web 应用](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)。
     
1. 选择“继续”****。
   
   ![填写存储库信息，然后选择"继续"。](media/app-service-continuous-deployment/configure-kudu.png)
   
1. 配置生成提供程序后，请查看 **"摘要"** 页上的设置，然后选择 **"完成**"。
   
1. 所选存储库和分支中的新提交现在连续部署到应用服务应用中。 您可以在 **"部署中心**"页上跟踪提交和部署。
   
   ![跟踪部署中心的提交和部署](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>选项 2：Azure 管道 

如果帐户具有必要的权限，则可以设置 Azure 管道以从 GitHub 或 Azure 存储库持续部署。 有关通过 Azure 管道进行部署的详细信息，请参阅[将 Web 应用部署到 Azure 应用服务](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)。

#### <a name="prerequisites"></a>先决条件

对于使用 Azure 管道创建连续传递的 Azure 应用服务，Azure DevOps 组织应具有以下权限： 

- Azure 帐户必须具有写入 Azure 活动目录和创建服务的权限。 
  
- Azure 帐户必须在 Azure 订阅中具有**所有者**角色。

- 您必须是要使用的 Azure DevOps 项目中的管理员。

#### <a name="github--azure-pipelines"></a>GitHub + Azure 管道

1. 在[Azure 门户](https://portal.azure.com)中，搜索**应用服务**，然后选择要部署的应用服务。 
   
1. 在应用页面上，选择左侧菜单中的 **"部署中心**"。

1. 在**部署中心**页面上选择**GitHub**作为源代码管理提供程序，然后选择"**继续**"。 对于**GitHub，** 您可以选择 **"更改帐户"** 来更改授权帐户。

    ![源代码管理 (source control)](media/app-service-continuous-deployment/deployment-center-src-control.png)
   
1. 在**生成提供程序**页上，选择**Azure 管道（预览），** 然后选择"**继续**"。

    ![生成提供程序](media/app-service-continuous-deployment/select-build-provider.png)
   
1. 在 **"配置**"页上，在 **"代码"** 部分中，选择要连续部署**的组织**、**存储库**和**分支**，然后选择"**继续**"。
     
     > [!NOTE]
     > 如果看不到任何存储库，则可能需要在 GitHub 中授权 Azure 应用服务。 浏览到您的 GitHub 存储库，然后转到**设置** > **应用程序** > **授权 OAuth 应用程序**。 选择**Azure 应用服务**，然后选择 **"授予**"。 对于组织存储库，您必须是组织的所有者才能授予权限。
       
    在 **"生成"** 部分中，指定 Azure 管道应用于运行生成任务的语言框架 Azure DevOps 组织、项目语言框架，然后选择"**继续**"。

   ![生成提供程序](media/app-service-continuous-deployment/build-configure.png)

1. 配置生成提供程序后，请查看 **"摘要"** 页上的设置，然后选择 **"完成**"。

   ![生成提供程序](media/app-service-continuous-deployment/summary.png)
   
1. 所选存储库和分支中的新提交现在连续部署到应用服务中。 您可以在 **"部署中心**"页上跟踪提交和部署。
   
   ![跟踪部署中心的提交和部署](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Azure 存储库 + Azure 管道

1. 在[Azure 门户](https://portal.azure.com)中，搜索**应用服务**，然后选择要部署的应用服务。 
   
1. 在应用页面上，选择左侧菜单中的 **"部署中心**"。

1. 选择**Azure 存储库**作为**部署中心**页上的源代码管理提供程序，然后选择"**继续**"。

    ![源代码管理 (source control)](media/app-service-continuous-deployment/deployment-center-src-control.png)

1. 在**生成提供程序**页上，选择**Azure 管道（预览），** 然后选择"**继续**"。

    ![源代码管理 (source control)](media/app-service-continuous-deployment/azure-pipelines.png)

1. 在 **"配置**"页上，在 **"代码"** 部分中，选择要连续部署**的组织**、**存储库**和**分支**，然后选择"**继续**"。

   > [!NOTE]
   > 如果未列出现有的 Azure DevOps 组织，则可能需要将其链接到 Azure 订阅。 有关详细信息，请参阅定义[CD 发行管道](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)。

   在 **"生成"** 部分中，指定 Azure 管道应用于运行生成任务的语言框架 Azure DevOps 组织、项目语言框架，然后选择"**继续**"。

   ![生成提供程序](media/app-service-continuous-deployment/build-configure.png)

1. 配置生成提供程序后，请查看 **"摘要"** 页上的设置，然后选择 **"完成**"。  
     
   ![生成提供程序](media/app-service-continuous-deployment/summary-azure-pipelines.png)

1. 所选存储库和分支中的新提交现在连续部署到应用服务中。 您可以在 **"部署中心**"页上跟踪提交和部署。

## <a name="disable-continuous-deployment"></a>禁用持续部署

要禁用连续部署，请在应用**的部署中心**页面顶部选择 **"断开连接**"。

![禁用持续部署](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>使用不支持的存储库

对于 Windows 应用，您可以手动配置来自门户没有直接支持的云 Git 或 Mercurial 存储库的连续部署，例如[GitLab](https://gitlab.com/)。 通过选择 **"部署中心**"页中的"外部"框来执行此操作。 有关详细信息，请参阅[使用手动步骤设置连续部署](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)。

## <a name="additional-resources"></a>其他资源

* [调查持续部署的常见问题](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [使用 Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Git 文档](https://git-scm.com/documentation)
* [项目 Kudu](https://github.com/projectkudu/kudu/wiki)

[创建存储库 (GitHub)]: https://help.github.com/articles/create-a-repo
[创建存储库 (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[创建新的 Git 存储库（Azure 存储库）]: /azure/devops/repos/git/creatingrepo
