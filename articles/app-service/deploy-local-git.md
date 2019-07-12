---
title: 从本地 Git 存储库进行部署 - Azure 应用服务
description: 了解如何实现从本地 Git 部署到 Azure 应用服务。
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: dariagrigoriu;cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: fa961e43408fed014be2266c14c7972d39435b97
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836908"
---
# <a name="local-git-deployment-to-azure-app-service"></a>本地 Git 部署到 Azure 应用服务

本操作方法指南演示如何将应用部署到[Azure 应用服务](overview.md)从本地计算机上的 Git 存储库。

## <a name="prerequisites"></a>先决条件

按照本操作方法指南中的步骤操作：

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [安装 Git](https://www.git-scm.com/downloads)。
  
- 具有你想要部署的代码的本地 Git 存储库。 若要下载示例存储库，请在本地终端窗口中运行以下命令：
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>使用 Kudu 生成服务器部署

若要启用的应用程序与应用的 Kudu 服务生成服务器的本地 Git 部署的最简单方法是使用 Azure Cloud Shell。 

### <a name="configure-a-deployment-user"></a>配置部署用户

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>获取部署 URL

若要获取的 URL，若要启用现有应用程序的本地 Git 部署，请运行[ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) Cloud Shell 中。 替换\<应用名称 > 和\<组名称 > 与您的应用程序和其 Azure 资源组的名称。

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

或者，若要创建新的启用 Git 的应用程序，运行[ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)在 Cloud Shell 中使用`--deployment-local-git`参数。 替换\<应用名称 >，\<组名称 >，和\<计划名称 > 与新的 Git 应用程序、 其 Azure 资源组和其 Azure 应用服务计划的名称。

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

这两个命令返回的 URL，如： `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`。 使用此 URL 在下一步中的将应用部署。

而不是使用此帐户级别 URL，您还可以启用本地 Git 使用的应用级凭据。 Azure 应用服务会自动生成这些凭据，以便每个应用程序。 

通过在 Cloud Shell 中运行以下命令获取应用程序凭据。 替换\<应用名称 > 和\<组名称 > 使用你的应用的名称和 Azure 资源组名称。

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

使用返回下一步中的将应用部署的 URL。

### <a name="deploy-the-web-app"></a>部署 Web 应用

1. 打开本地终端窗口中的为本地 Git 存储库，并将 Azure 远程功能添加。 在以下命令，将为\<url > 与部署特定于用户的 URL 或从上一步骤中获得的特定于应用的 URL。
   
   ```bash
   git remote add azure <url>
   ```
   
1. 推送到 Azure 远程功能与`git push azure master`。 
   
1. 在中**Git 凭据管理器**窗口中，输入你[部署用户密码](#configure-a-deployment-user)，不在 Azure 登录的密码。
   
1. 查看输出。 你可能会看到特定于运行时的自动化，如 MSBuild for ASP.NET，`npm install`适用于 Node.js，和`pip install`适用于 Python。 
   
1. 浏览到您的应用程序以验证内容已部署在 Azure 门户中。

## <a name="deploy-with-azure-pipelines-builds"></a>使用 Azure 管道生成部署

如果你的帐户具有所需的权限，您可以设置 Azure 管道 （预览版） 来为应用启用本地 Git 部署。 

- 你的 Azure 帐户必须有权写入到 Azure Active Directory 并创建一个服务。 
  
- 你的 Azure 帐户必须具有**所有者**在 Azure 订阅中的角色。

- 必须是你想要使用 Azure DevOps 项目中的管理员。

若要启用的应用程序与 Azure 管道 （预览版） 的本地 Git 部署：

1. 在 Azure 应用服务应用程序页导航[Azure 门户](https://portal.azure.com)，然后选择**部署中心**在左侧菜单中。
   
1. 上**部署中心**页上，选择**本地 Git**，然后选择**继续**。 
   
   ![选择本地 Git，然后选择继续](media/app-service-deploy-local-git/portal-enable.png)
   
1. 上**生成提供程序**页上，选择**Azure 管道 （预览版）** ，然后选择**继续**。 
   
   ![选择 Azure 管道 （预览版），然后选择继续。](media/app-service-deploy-local-git/pipeline-builds.png)

1. 上**配置**页上，配置新的 Azure DevOps 组织，或指定现有组织，并选择**继续**。
   
   > [!NOTE]
   > 如果未列出你现有的 Azure DevOps 组织，可能需要将其链接到你的 Azure 订阅。 有关详细信息，请参阅[定义 CD 发布管道](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)。
   
1. 具体取决于你的应用服务计划[定价层](https://azure.microsoft.com/pricing/details/app-service/plans/)，可能会看到**部署到过渡环境**页。 选择是否[启用部署槽](deploy-staging-slots.md)，然后选择**继续**。
   
1. 上**摘要**页上，查看设置，并选择**完成**。
   
1. Azure 管道准备就绪后，复制从 Git 存储库 URL**部署中心**页后，可以在下一步中使用。 
   
   ![复制 Git 存储库 URL](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. 在本地终端窗口中，将 Azure 远程添加到本地 Git 存储库。 在命令中，将为\<url > 从上一步骤中获得的 Git 存储库的 url。
   
   ```bash
   git remote add azure <url>
   ```
   
1. 推送到 Azure 远程功能与`git push azure master`。 
   
1. 上**Git 凭据管理器**页上，使用你的 visualstudio.com 用户名登录。 有关其他身份验证方法，请参阅[Azure DevOps 服务身份验证概述](/vsts/git/auth-overview?view=vsts)。
   
1. 完成部署后，查看在生成进度`https://<azure_devops_account>.visualstudio.com/<project_name>/_build`，并在部署进度`https://<azure_devops_account>.visualstudio.com/<project_name>/_release`。
   
1. 浏览到您的应用程序以验证内容已部署在 Azure 门户中。

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>排查部署问题

使用 Git 发布到 Azure 中的应用服务应用时，可能会看到以下常见的错误消息：

|消息|原因|解决方法
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|应用程序未启动并运行。|在 Azure 门户中启动应用。 停止 web 应用时，Git 部署不可用。|
|`Couldn't resolve host 'hostname'`|Azure 远程地址信息不正确。|使用 `git remote -v` 命令列出所有远程网站以及关联的 URL。 确认“azure”远程网站的 URL 正确。 如果需要，请删除此远程网站并使用正确的 URL 重新创建它。|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|未指定分支期间`git push`，或尚未设置`push.default`中的值`.gitconfig`。|运行`git push`同样，指定 master 分支： `git push azure master`。|
|`src refspec [branchname] does not match any.`|你尝试推送到分支 master 之外的分支 azure 远程网站上。|运行`git push`同样，指定 master 分支： `git push azure master`。|
|`RPC failed; result=22, HTTP code = 5xx.`|如果尝试通过 HTTPS 推送大型 Git 存储库，则可能出现此错误。|更改以使在本地计算机上的 git 配置`postBuffer`更大。 例如：`git config --global http.postBuffer 524288000`。|
|`Error - Changes committed to remote repository but your web app not updated.`|部署 Node.js 应用时使用_package.json_指定其他必需的模块的文件。|查看`npm ERR!`有关失败的更多上下文此错误之前的错误消息。 以下是此错误，以及相应的已知的原因`npm ERR!`消息：<br /><br />**格式不正确的 package.json 文件**: `npm ERR! Couldn't read dependencies.`<br /><br />**本机模块不具有的二进制分发的 Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />或 <br />`npm ERR! [modulename@version] 预安装： \make || gmake\`|

## <a name="additional-resources"></a>其他资源

- [项目 Kudu 文档](https://github.com/projectkudu/kudu/wiki)
- [连续部署到 Azure 应用服务](deploy-continuous-deployment.md)
- [示例：创建 web 应用并部署代码从本地 Git 存储库 (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [示例：创建 web 应用并部署代码从本地 Git 存储库 (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
