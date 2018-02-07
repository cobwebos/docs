---
title: "从本地 Git 部署到 Azure 应用服务"
description: "了解如何实现从本地 Git 部署到 Azure 应用服务。"
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: cfowler
editor: mollybos
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 948c54a2e9be2260d0a7d2cce31b67ffbbd23d03
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="local-git-deployment-to-azure-app-service"></a>从本地 Git 部署到 Azure 应用服务

本教程说明如何将应用从本地计算机上的 Git 存储库部署到 [Azure Web 应用](app-service-web-overview.md)。 应用服务通过 [Azure 门户]中的“本地 Git”部署选项支持此方法。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* Git。 可在[此处](http://www.git-scm.com/downloads)下载二进制安装文件。
* 对 Git 有一个基本的了解。
* 一个 Microsoft Azure 帐户。 如果没有帐户，可以[注册免费试用帐户](https://azure.microsoft.com/pricing/free-trial)，或者[激活 Visual Studio 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)。

> [!NOTE]
> 若要在注册 Azure 帐户之前开始使用 Azure 应用服务，请转到[试用应用服务](https://azure.microsoft.com/try/app-service/)，可以在应用服务中立即创建一个生存期较短的入门应用。 不需要使用信用卡，也不需要做出承诺。
>

## <a name="Step1"></a>步骤 1：创建本地存储库

执行下列任务可创建新的 Git 存储库。

1. 启动命令行工具，例如 **Git Bash** (Windows) 或 **Bash** (Unix Shell)。 在 OS X 系统上，可以通过 **Terminal** 应用程序访问命令行。
1. 导航到要部署的内容所在的目录。
1. 使用以下命令可初始化新的 Git 存储库：

  ```bash
  git init
  ```

## <a name="Step2"></a>步骤 2：提交内容

应用服务支持用各种编程语言创建的应用程序。

1. 如果存储库尚不包含内容，请添加静态 .html 文件（如下所示）或跳过此步骤：
   * 使用文本编辑器，在 Git 存储库的根中创建一个名为 **index.html** 的新文件。
   * 添加以下文本作为 index.html 文件的内容并保存该文件：*Hello Git!*
1. 在命令行中，验证当前位置是否在 Git 存储库的根目录下。 然后使用以下命令将文件添加到存储库中：

        git add -A 
1. 接下来，使用以下命令将更改提交到存储库：

        git commit -m "Hello Azure App Service"

## <a name="Step3"></a>步骤 3：启用应用服务应用存储库

执行以下步骤为应用服务应用启用 Git 存储库。

1. 登录到 [Azure 门户]。
1. 在应用服务应用的视图中，单击“设置”>“部署源”。 依次单击“选择源”、“本地 Git 存储库”、“确定”。

    ![本地 Git 存储库](./media/app-service-deploy-local-git/local_git_selection.png)

1. 如果这是第一次在 Azure 中设置存储库，则需要为其创建登录凭据。 使用凭据登录到 Azure 存储库并从本地 Git 存储库推送更改。 在 Web 应用的视图中，单击“设置”>“部署凭据”，并配置部署用户名和密码。 完成后，单击“保存”。

    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a name="Step4"></a>步骤 4：部署项目

使用以下步骤通过本地 Git 将应用发布到应用服务。

1. 在 Azure 门户的 Web 应用视图中，对“Git URL”单击“设置”>“属性”。

    ![](./media/app-service-deploy-local-git/git_url.png)

    **Git URL** 是从本地存储库到部署的远程引用。 在后续步骤中将使用此 URL。
1. 使用命令行验证当前位置是否在本地 Git 存储库的根目录下。
1. 使用 `git remote` 添加步骤 1 的 **Git URL** 中所列的远程引用。 命令类似于以下内容：

    ```bash
    git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git
    ```

   > [!NOTE]
   > **remote** 命令可将命名引用添加到远程存储库。 在本示例中，它为 Web 应用的存储库创建名为“azure”的引用。
   >

1. 使用创建的新 **azure** 远程将内容推送到应用服务。

    ```bash
    git push azure master
    ```

    在 Azure 门户中重置部署凭据时，系统会提示输入以前创建的密码。 输入该密码（请注意，在键入密码时，Gitbash 不会将星号回显到控制台）。 
1. 返回到 Azure 门户中的应用。 最近推送的日志条目应显示在“部署”视图中。

    ![](./media/app-service-deploy-local-git/deployment_history.png)

1. 单击 Web 应用页顶部的“浏览”按钮来验证是否已部署内容。

## <a name="Step5"></a>故障排除

以下是使用 Git 发布到 Azure 中的应用服务应用时遇到的常见错误或问题：

---
**症状**：`Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**原因**：如果应用无法正常工作，则会发生该错误。

**解决方法**：在 Azure 门户中启动应用。 如果 Web 应用停止时，Git 部署将不可用。

---
**症状**：`Couldn't resolve host 'hostname'`

**原因**：如果创建“azure”远程网站时输入的地址信息不正确，则会发生该错误。

**解决方法**：使用 `git remote -v` 命令列出所有远程网站以及关联的 URL。 确认“azure”远程网站的 URL 正确。 如果需要，请删除此远程网站并使用正确的 URL 重新创建它。

---
**症状**：`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**原因**：如果在 `git push` 期间未指定分支，或者未在 `.gitconfig` 中设置 `push.default` 值，则会出现此错误。

**解决方法**：请再次执行推送操作，并指定 master 分支。 例如：

```bash
git push azure master
```

---
**症状**：`src refspec [branchname] does not match any.`

**原因**：如果尝试推送到“azure”远程网站上 master 分支之外的分支，则会发生该错误。

**解决方法**：请再次执行推送操作，并指定 master 分支。 例如：

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

**原因**：如果部署的是 Node.js 应用，其中包含用于指定其他必需模块的 package.json 文件，则会发生该错误。

**解决方法**：应在发生此错误之前记录包含“npm ERR!” 的其他消息，这些消息可提供有关失败的其他上下文。 以下是该错误的已知原因和相应的“npm ERR!” 消息:

* **package.json 文件格式不正确**：npm ERR! 无法读取依赖项。
* **不具有 Windows 的二进制分发的本机模块**：

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      或
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>其他资源

* [Git 文档](http://git-scm.com/documentation)
* [项目 Kudu 文档](https://github.com/projectkudu/kudu/wiki)
* [连续部署到 Azure 应用服务](app-service-continuous-deployment.md)
* [如何使用适用于 Azure 的 PowerShell](/powershell/azure/overview)
* [如何使用 Azure 命令行接口](../cli-install-nodejs.md)

[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[Azure 门户]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Azure Command-Line Interface]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
