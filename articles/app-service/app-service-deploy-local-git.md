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
ms.date: 03/05/2018
ms.author: dariagrigoriu;cephalin
ms.openlocfilehash: 4cbe26055bdbf906223a327ab8cf94bebe9e7998
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
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

## <a name="prepare-your-repository"></a>准备存储库

确保存储库根路径具有项目中的正确文件。

| 运行时 | 根目录文件 |
|-|-|
| ASP.NET（仅限 Windows） | *.sln、*.csproj 或 default.aspx |
| ASP.NET Core | *.sln 或 *.csproj |
| PHP | index.php |
| Ruby（仅限 Linux） | Gemfile |
| Node.js | server.js、app.js 或具有启动脚本的 package.json |
| Python（仅限 Windows） | \*.py、requirements.txt 或 runtime.txt |
| HTML | default.htm、default.html、default.asp、index.htm、index.html 或 iisstart.htm |
| Web 作业 | App\_Data/jobs/continuous（适用于连续的 WebJobs）或 App\_Data/jobs/triggered（适用于触发的 WebJobs）下的 \<job_name>/run.\<extension>。 有关详细信息，请参阅 [Kudu WebJobs 文档](https://github.com/projectkudu/kudu/wiki/WebJobs) |
| 函数 | 请参阅 [Azure Functions 的连续部署](../azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements)。 |

要自定义部署，可以在存储库根路径中添加 .deployment 文件。 有关详细信息，请参阅[自定义部署](https://github.com/projectkudu/kudu/wiki/Customizing-deployments)和[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)。

> [!NOTE]
> 请确保 `git commit` 想要部署的所有更改。
>
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user.md)]

## <a name="enable-git-for-your-app"></a>启用应用的 Git

要启用现有应用服务应用的 Git 部署，请在 Cloud Shell 中运行 [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_local_git)。

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

要创建启用 Git 的应用，请使用 `--deployment-local-git` 参数在 Cloud Shell 中运行 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create)。

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

## <a name="deploy-your-project"></a>部署项目

回到本地终端窗口，将 Azure 远程功能添加到本地 Git 存储库。 使用从[启用应用的 Git](#enable-git-for-you-app)中获取的 Git 远程 URL 替换 \<url>。

```bash
git remote add azure <url>
```

使用以下命令推送到 Azure 远程功能以部署应用。 提示输入密码时，请确保输入在[配置部署用户](#configure-a-deployment-user)中创建的密码，而不是用于登录到 Azure 门户的密码。

```bash
git push azure master
```

在输出中可能会看到特定于运行时的自动化，如 MSBuild for ASP.NET、`npm install` for Node.js 和 `pip install` for Python。 

部署完成后，Azure 门户中的应用现应在“部署选项”页具有 `git push` 的记录。

![](./media/app-service-deploy-local-git/deployment_history.png)

浏览到应用以验证内容已部署。

## <a name="troubleshooting"></a>故障排除

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
* **不具有 Windows 的二进制分发的本机模块**：

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      或
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>其他资源

* [项目 Kudu 文档](https://github.com/projectkudu/kudu/wiki)
* [连续部署到 Azure 应用服务](app-service-continuous-deployment.md)
