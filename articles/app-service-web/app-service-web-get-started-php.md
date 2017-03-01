---
title: "在 5 分钟内将第一个 PHP Web 应用部署到 Azure（CLI 2.0 预览版）| Microsoft 文档"
description: "了解如何通过部署一个示例 PHP 应用，轻松地在应用服务中运行 Web 应用。 快速开始进行实际开发并立即查看结果。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: f739bf6101936ff6bb25738e4888476e3b33a38f


---
# <a name="deploy-your-first-php-web-app-to-azure-in-five-minutes-cli-20-preview"></a>在 5 分钟内将第一个 PHP Web 应用部署到 Azure（CLI 2.0 预览版）

> [!div class="op_single_selector"]
> * [第一个 HTML 站点](app-service-web-get-started-html.md)
> * [第一个 .NET 应用](app-service-web-get-started-dotnet.md)
> * [第一个 PHP 应用](app-service-web-get-started-php.md)
> * [第一个 Node.js 应用](app-service-web-get-started-nodejs.md)
> * [第一个 Python 应用](app-service-web-get-started-python.md)
> * [第一个 Java 应用](app-service-web-get-started-java.md)
> 
> 

本教程帮助用户将第一个 PHP Web 应用部署到 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。
应用服务可用于创建 Web 应用、[移动应用后端](/documentation/learning-paths/appservice-mobileapps/)和 [API 应用](../app-service-api/app-service-api-apps-why-best-platform.md)。

你可以： 

* 在 Azure App Service 中创建 Web 应用。
* 部署 PHP 示例代码。
* 查看生产中实时运行的代码。
* 以 [推送 Git 提交](https://git-scm.com/docs/git-push)的相同方式来更新 Web 应用。

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本

可以使用以下 CLI 版本之一完成任务：

- [Azure CLI 1.0](app-service-web-get-started-php-cli-nodejs.md) - 适用于经典部署模型和资源管理部署模型的 CLI
- [Azure CLI 2.0（预览版）](app-service-web-get-started-php.md)- 适用于资源管理部署模型的下一代 CLI

## <a name="prerequisites"></a>先决条件
* [Git](http://www.git-scm.com/downloads)。
* [Azure CLI 2.0 预览版](/cli/azure/install-az-cli2)。
* 一个 Microsoft Azure 帐户。 如果没有帐户，可以[注册免费试用帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或者[激活 Visual Studio 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

> [!NOTE]
> 无需 Azure 帐户即可 [试用应用服务](https://azure.microsoft.com/try/app-service/) 。 创建入门级应用并使用长达一小时 — 无需信用卡，也无需做出承诺。
> 
> 

## <a name="deploy-a-php-web-app"></a>部署 PHP Web 应用
1. 打开新的 Windows 命令提示符、PowerShell 窗口、Linux shell 或 OS X 终端。 运行 `git --version` 和 `azure --version`，验证计算机上是否已安装 Git 和 Azure CLI。
   
    ![在 Azure 中测试第一个 Web 应用的 CLI 工具安装](./media/app-service-web-get-started-languages/1-test-tools-2.0.png)
   
    如果尚未安装这些工具，请参阅[先决条件](#Prerequisites)中的下载链接。
2. 如下所示登录 Azure ：
   
        az login
   
    按照帮助消息的提示继续此登录过程。
   
    ![登录到 Azure 以创建第一个 Web 应用](./media/app-service-web-get-started-languages/3-azure-login-2.0.png)

3. 设置应用服务的部署用户。 稍后将使用这些凭据部署代码。
   
        az appservice web deployment user set --user-name <username> --password <password>

3. 创建新[资源组](../azure-resource-manager/resource-group-overview.md)。 在这第一篇应用服务教程中，实际上并不需要知道什么是资源组。

        az group create --location "<location>" --name my-first-app-group

    若要查看可为 `<location>` 使用的可能值，请使用 `az appservice list-locations` CLI 命令。

3. 创建新的“免费”[应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。 在这第一篇应用服务教程中，只需知道此计划中的 Web 应用不会产生费用。

        az appservice plan create --name my-free-appservice-plan --resource-group my-first-app-group --sku FREE

4. 在 `<app_name>` 中创建使用唯一名称的新 Web 应用。

        az appservice web create --name <app_name> --resource-group my-first-app-group --plan my-free-appservice-plan

4. 接下来，获取所要部署的示例 PHP 代码。 切换到工作目录 (`CD`) 并克隆示例应用，如下所示：
   
        cd <working_directory>
        git clone https://github.com/Azure-Samples/app-service-web-php-get-started.git

5. 更改为示例应用的存储库。 例如：
   
        cd app-service-web-php-get-started
5. 使用以下命令为 App 应用服务 Web 应用配置本地 Git 部署：

        az appservice web source-control config-local-git --name <app_name> --resource-group my-first-app-group

    将返回类似于下面的 JSON 输出，这表示已配置远程 Git 存储库：

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. 在 JSON 中添加该 URL 作为本地存储库（简称 `azure`）的 Git 远程地址。

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. 像使用 Git 推送任何代码一样，将示例代码部署到 Azure 应用。 出现提示时，使用之前配置的密码。
   
        git push azure master
   
    ![在 Azure 中将代码推送到第一个 Web 应用](./media/app-service-web-get-started-languages/php-git-push.png)
   
    `git push` 不仅将代码放在 Azure 中，也在部署引擎中触发部署任务。 另外，还可以  [启用编辑器扩展](web-sites-php-mysql-deploy-use-git.md#composer) ，以在 PHP 应用中自动处理 composer.json 文件。

恭喜！应用已部署到 Azure App Service。

## <a name="see-your-app-running-live"></a>查看应用实时运行
若要查看 Azure 中实时运行的应用，请从存储库中的任何目录运行以下命令：

    azure site browse

## <a name="make-updates-to-your-app"></a>更新应用
现在可以使用 Git 随时从项目（存储库）根目录进行推送，以更新实时站点。 采用首次部署代码时的相同方法执行此操作。 例如，每次想要推送已在本地测试的新更改时，只需从项目（存储库）根目录运行以下命令：

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>后续步骤
[创建、配置 Laravel Web 应用，并将其部署到 Azure](app-service-web-php-get-started.md)。 通过按照本教程中的说明进行操作，你将学会在 Azure 中运行任何 PHP Web 应用所需的以下基本技能：

* 通过 PowerShell/Bash 在 Azure 中创建并配置应用。
* 设置 PHP 版本。
* 使用不在根应用程序目录中的开始文件。
* 实现 Commposer 自动化。
* 访问环境特定的变量。
* 排查常见错误。

或者，使用你的第一个 Web 应用执行更多操作。 例如：

* 尝试使用 [其他方法将代码部署到 Azure](web-sites-deploy.md)。 例如，若要从某个 GitHub 存储库中进行部署，只需选择 **GitHub**，而不是“部署选项”中的“本地 Git 存储库”。
* 使 Azure 应用上升到更高的层次。 对用户进行身份验证。 按需缩放。 设置一些性能警报。 所有这些操作只需按几下鼠标即可完成。 请参阅 [在第一个 Web 应用中添加功能](app-service-web-get-started-2.md)。




<!--HONumber=Jan17_HO3-->


