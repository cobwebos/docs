---
title: 从本地 Git 部署到 Azure 应用服务
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
ms.date: 06/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: 0f6a0e2fe3aa632137392efe806aaab265eedf10
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435536"
---
# <a name="local-git-deployment-to-azure-app-service"></a>从本地 Git 部署到 Azure 应用服务

本操作方法指南说明如何将代码从本地计算机上的 Git 存储库部署到 [Azure 应用服务](app-service-web-overview.md)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

按照本操作方法指南中的步骤操作：

* [安装 Git](http://www.git-scm.com/downloads)。
* 使用想要部署的代码维护本地 Git 存储库。

要遵循示例存储库操作，请在本地终端窗口运行以下命令：

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git-with-kudu-builds"></a>通过 Kudu 生成从本地 Git 部署

使用 Kudu 生成服务器为应用启用本地 Git 部署的最简单方法是使用 Cloud Shell。

### <a name="create-a-deployment-user"></a>创建部署用户

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>使用 Kudu 启用本地 Git

若要使用 Kudu 生成服务器为应用启用本地 Git 部署，请在 Cloud Shell 中运行 [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git)。

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

要创建启用 Git 的应用，请使用 `--deployment-local-git` 参数在 Cloud Shell 中运行 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)。

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

`az webapp create` 命令的输出应如下所示：

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>部署项目

回到本地终端窗口，将 Azure 远程功能添加到本地 Git 存储库。 使用从[启用应用的 Git](#enable-git-for-you-app)中获取的 Git 远程 URL 替换 \<url>。

```bash
git remote add azure <url>
```

使用以下命令推送到 Azure 远程功能以部署应用。 提示输入密码时，请确保输入在[配置部署用户](#configure-a-deployment-user)中创建的密码，而不是用于登录到 Azure 门户的密码。

```bash
git push azure master
```

在输出中可能会看到特定于运行时的自动化，如 MSBuild for ASP.NET、`npm install` for Node.js 和 `pip install` for Python。 

浏览到应用以验证内容已部署。

## <a name="deploy-from-local-git-with-vsts-builds"></a>通过 VSTS 生成从本地 Git 部署

> [!NOTE]
> 要让应用服务在 VSTS 帐户中创建必要的生成和发布定义，Azure 帐户必须在 Azure 订阅中具有“所有者”角色。
>

若要使用 Kudu 生成服务器为应用启用本地 Git 部署，请在 [Azure 门户](https://portal.azure.com)中导航至应用。

在应用页的左侧导航栏中，单击“部署中心” > “本地 Git” > “继续”。 

![](media/app-service-deploy-local-git/portal-enable.png)

单击“VSTS 持续交付” > “继续”。

![](media/app-service-deploy-local-git/vsts-build-server.png)

在“配置”页上，配置新的 VSTS 帐户，或指定现有帐户。 完成后，单击“继续”。

> [!NOTE]
> 如果想要使用未列出的现有 VSTS 帐户，则需要[将 VSTS 帐户链接到 Azure 订阅](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)。

在“测试”页面中，选择是否要启用负载测试，然后单击“继续”。

根据应用服务计划的[定价层](/pricing/details/app-service/plans/)，可能还会看到“部署到过渡环境”页。 选择是否要启用部署槽位，然后单击“继续”。

在“摘要”页中，确认选项，然后单击“完成”。

准备 VSTS 帐户需要几分钟的时间。 准备就绪后，在部署中心复制 Git 存储库 URL。

![](media/app-service-deploy-local-git/vsts-repo-ready.png)

回到本地终端窗口，将 Azure 远程功能添加到本地 Git 存储库。 将 _\<url>_ 替换为从上一步获得的 URL。

```bash
git remote add vsts <url>
```

使用以下命令推送到 Azure 远程功能以部署应用。 当 Git 凭据管理器提示时，请使用 visualstudio.com 用户登录。 有关其他身份验证方法，请参阅 [VSTS 身份验证概述](/vsts/git/auth-overview?view=vsts)。

```bash
git push vsts master
```

部署完成后，可以在 `https://<vsts_account>.visualstudio.com/<project_name>/_build` 中找到构建进度，并在 `https://<vsts_account>.visualstudio.com/<project_name>/_release` 中找到部署进度。

浏览到应用以验证内容已部署。

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshooting-kudu-deployment"></a>排查 Kudu 部署问题

以下是使用 Git 发布到 Azure 中的应用服务应用时遇到的常见错误或问题：

---
**症状**：`Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**原因**：如果应用无法启动和运行，则会发生该错误。

**解决方法**：在 Azure 门户中启动应用。 如果 Web 应用停止时，Git 部署将不可用。

---
**症状**：`Couldn't resolve host 'hostname'`

**原因**：如果创建“azure”远程网站时输入的地址信息不正确，则会发生该错误。

**解决方法**：使用 `git remote -v` 命令列出所有远程网站以及关联的 URL。 确认“azure”远程网站的 URL 正确。 如果需要，请删除此远程网站并使用正确的 URL 重新创建它。

---
**症状**：`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**原因**：如果在 `git push` 期间未指定分支，或者未在 `.gitconfig` 中设置 `push.default` 值，则会出现此错误。

**解决方法**：再次运行 `git push`，指定主分支。 例如：

```bash
git push azure master
```

---
**症状**：`src refspec [branchname] does not match any.`

**原因**：如果尝试推送到“azure”远程网站上主分支之外的分支，则会发生该错误。

**解决方法**：再次运行 `git push`，指定主分支。 例如：

```bash
git push azure master
```

---
**症状**：`RPC failed; result=22, HTTP code = 5xx.`

**原因**：如果尝试通过 HTTPS 推送大型 Git 存储库，则可能出现此错误。

**解决方法**：在本地计算机上更改 Git 配置，以增大 postBuffer

```bash
git config --global http.postBuffer 524288000
```

---
**症状**：`Error - Changes committed to remote repository but your web app not updated.`

**原因**：如果部署 Node.js 应用时使用的 package.json 文件指定了其他所需模块，则会发生该错误。

**解决方法**：应在发生此错误之前记录包含“npm ERR!” 的其他消息，这些消息可提供有关失败的其他上下文。 以下是该错误的已知原因和相应的“npm ERR!” 消息:

* **package.json 文件格式不正确**：npm ERR! 无法读取依赖项。
* **没有 Windows 的二进制分发的本机模块**：

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      或
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>其他资源

* [项目 Kudu 文档](https://github.com/projectkudu/kudu/wiki)
* [连续部署到 Azure 应用服务](app-service-continuous-deployment.md)
* [示例：从本地 Git 存储库创建 Web 应用并部署代码 (Azure CLI)](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [示例：从本地 Git 存储库创建 Web 应用并部署代码 (PowerShell)](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
