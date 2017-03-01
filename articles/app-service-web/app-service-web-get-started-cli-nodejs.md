---
title: "在&5; 分钟内将第一个 Web 应用部署到 Azure | Microsoft Docs"
description: "了解如何通过部署一个示例应用，轻松地在应用服务中运行 Web 应用。 快速开始进行实际开发并立即查看结果。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 65c9bdd9-8763-4c56-8e15-f790992e951e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/04/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 2c750583212d7ed13e3bb5f4d0770d52aa9610b2


---
# <a name="deploy-your-first-web-app-to-azure-in-five-minutes"></a>在五分钟内将第一个 Web 应用部署到 Azure
本教程帮助用户将第一个 Web 应用部署到 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。
应用服务可用于创建 Web 应用、[移动应用后端](/documentation/learning-paths/appservice-mobileapps/)和 [API 应用](../app-service-api/app-service-api-apps-why-best-platform.md)。

你可以： 

* 在 Azure App Service 中创建 Web 应用。
* 部署示例代码（在 ASP.NET、PHP、Node.js、Java 或 Python 之间选择）。
* 查看生产中实时运行的代码。
* 以 [推送 Git 提交](https://git-scm.com/docs/git-push)的相同方式来更新 Web 应用。

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="cli-versions-to-complete-the-task"></a>用于完成任务的 CLI 版本

可以使用以下 CLI 版本之一完成任务：

- [Azure CLI 1.0](app-service-web-get-started-cli-nodejs.md) - 适用于经典部署模型和资源管理部署模型的 CLI
- [Azure CLI 2.0（预览版）](app-service-web-get-started.md)- 适用于资源管理部署模型的下一代 CLI

## <a name="prerequisites"></a>先决条件
* [Git](http://www.git-scm.com/downloads)。
* [Azure CLI](../xplat-cli-install.md)。
* 一个 Microsoft Azure 帐户。 如果没有帐户，可以[注册免费试用帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或者[激活 Visual Studio 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

> [!NOTE]
> 无需 Azure 帐户即可 [试用应用服务](https://azure.microsoft.com/try/app-service/) 。 创建入门级应用并使用长达一小时 — 无需信用卡，也无需做出承诺。
> 
> 

## <a name="deploy-a-web-app"></a>部署 Web 应用
让我们将 Web 应用部署到 Azure App Service。

1. 打开新的 Windows 命令提示符、PowerShell 窗口、Linux shell 或 OS X 终端。 运行 `git --version` 和 `azure --version`，验证计算机上是否已安装 Git 和 Azure CLI。
   
    ![在 Azure 中测试第一个 Web 应用的 CLI 工具安装](./media/app-service-web-get-started/1-test-tools.png)
   
    如果尚未安装这些工具，请参阅[先决条件](#Prerequisites)中的下载链接。
2. 如下所示登录 Azure ：
   
        azure login
   
    按照帮助消息的提示继续此登录过程。
   
    ![登录到 Azure 以创建第一个 Web 应用](./media/app-service-web-get-started/3-azure-login.png)
3. 将 Azure CLI 更改为 ASM 模式，然后设置应用服务的部署用户。 稍后使用凭据部署代码。
   
        azure config mode asm
        azure site deployment user set --username <username> --pass <password>

4. 切换到工作目录 (`CD`) 并克隆示例应用，如下所示：
   
        git clone <github_sample_url>
   
    ![在 Azure 中克隆第一个 Web 应用的应用示例代码](./media/app-service-web-get-started/2-clone-sample.png)
   
    对于 *&lt;github_sample_url>*，请根据所需的框架，使用下述某个 URL：
   
   * HTML+CSS+JS： [https://github.com/Azure-Samples/app-service-web-html-get-started.git](https://github.com/Azure-Samples/app-service-web-html-get-started.git)
   * ASP.NET： [https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git](https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git)
   * PHP (CodeIgniter)： [https://github.com/Azure-Samples/app-service-web-php-get-started.git](https://github.com/Azure-Samples/app-service-web-php-get-started.git)
   * Node.js (Express)： [https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git](https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git)
   * Java： [https://github.com/Azure-Samples/app-service-web-java-get-started.git](https://github.com/Azure-Samples/app-service-web-java-get-started.git)
   * Python (Django)： [https://github.com/Azure-Samples/app-service-web-python-get-started.git](https://github.com/Azure-Samples/app-service-web-python-get-started.git)

5. 更改为示例应用的存储库。 例如：
   
        cd app-service-web-html-get-started

6. 在 Azure 中创建具有唯一应用名称及之前配置的部署用户的应用服务应用资源。 出现提示时，指定所需的区域数目。
   
        azure site create <app_name> --git --gitusername <username>
   
    ![在 Azure 中创建第一个 Web 应用的 Azure 资源](./media/app-service-web-get-started/4-create-site.png)
   
    现在已在 Azure 中创建应用。 而且当前的目录也已进行 Git 初始化并作为 Git 远程连接到新的 App Service 应用。
    可浏览到应用 URL (http://&lt;app_name>.azurewebsites.net) 查看美观的默认 HTML 页面，但现在真的要做的是在此处获取代码。

7. 像使用 Git 推送任何代码一样，将示例代码部署到 Azure 应用。 出现提示时，使用之前配置的密码。
   
        git push azure master
   
    ![在 Azure 中将代码推送到第一个 Web 应用](./media/app-service-web-get-started/5-push-code.png)
   
    如果使用了某种语言框架，会看到不同的输出。 `git push` 不仅将代码放在 Azure 中，也在部署引擎中触发部署任务。 如果项目（存储库）根目录中有任何 package.json (Node.js) 或 requirements.txt (Python) 文件，或 ASP.NET 项目中有 packages.config 文件，则部署脚本将为你还原所需程序包。 另外，还可以 [启用编辑器扩展](web-sites-php-mysql-deploy-use-git.md#composer) ，以在 PHP 应用中自动处理 composer.json 文件。

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
根据你的语言框架找到所需的开发和部署步骤：

> [!div class="op_single_selector"]
> * [.NET](web-sites-dotnet-get-started.md)
> * [PHP](app-service-web-php-get-started-cli-nodejs.md)
> * [Node.js](app-service-web-nodejs-get-started-cli-nodejs.md)
> * [Python](web-sites-python-ptvs-django-mysql.md)
> * [Java](web-sites-java-get-started.md)
> 
> 

或者，使用你的第一个 Web 应用执行更多操作。 例如：

* 尝试使用 [其他方法将代码部署到 Azure](web-sites-deploy.md)。 例如，若要从某个 GitHub 存储库中进行部署，只需选择 **GitHub**，而不是“部署选项”中的“本地 Git 存储库”。
* 使 Azure 应用上升到更高的层次。 对用户进行身份验证。 按需缩放。 设置一些性能警报。 所有这些操作只需按几下鼠标即可完成。 请参阅 [在第一个 Web 应用中添加功能](app-service-web-get-started-2.md)。




<!--HONumber=Feb17_HO3-->


