---
title: 在 5 分钟内将第一个 Web 应用部署到 Azure | Microsoft Docs
description: 了解如何通过部署示例应用，轻松地在应用服务中运行 Web 应用。快速开始进行实际开发并立即查看结果。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: wpickett
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/16/2016
ms.author: cephalin

---
# 在 5 分钟内将第一个 Web 应用部署到 Azure
本教程帮助用户将一个简单的 HTML+CSS Web 应用部署到 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。应用服务可用于创建 Web 应用、[移动应用后端](/documentation/learning-paths/appservice-mobileapps/)和 [API 应用](../app-service-api/app-service-api-apps-why-best-platform.md)。

你将能够：

* 在 Azure App Service 中创建 Web 应用。
* 向它部署 HTML 和 CSS。
* 查看生产中实时运行的页面。
* 以[推送 Git 提交](https://git-scm.com/docs/git-push)的相同方式更新内容。

## 先决条件
* [安装 Git](http://www.git-scm.com/downloads)。通过以下方式验证安装是否已成功：从新的 Windows 命令提示符、PowerShell 窗口、Linux shell 或 OS X 终端运行 `git --version`。
* 获取 Microsoft Azure 帐户。如果没有帐户，可以[注册免费试用帐户](/pricing/free-trial/?WT.mc_id=A261C142F)，或者[激活 Visual Studio 订户权益](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

> [!NOTE]
> 无需 Azure 帐户即可[试用应用服务](http://go.microsoft.com/fwlink/?LinkId=523751)。创建入门级应用并使用长达一小时 -- 无需信用卡，也无需做出承诺。
> 
> 

<a name="create">

## 创建 Web 应用
1. 使用你的 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。
2. 从左侧菜单，依次单击“新建”>“Web + 移动”>“Web 应用”。
   
    ![](./media/app-service-web-get-started-languages/create-web-app-portal.png)
3. 在应用创建边栏选项卡中，将以下设置用于新应用：
   
   * **应用名称**：键入一个唯一名称。
   * **资源组**：选择“新建”并为资源组指定名称。
   * **应用服务计划/位置**：单击它进行配置，然后单击“新建”，设置应用服务计划的名称、位置和定价层。可随意使用**免费**定价层。
     
     完成后，应用创建边栏选项卡应如下所示：
     
     ![](./media/app-service-web-get-started-languages/create-web-app-settings.png)
4. 单击底部的“创建”。可以单击顶部的“通知”图标查看进度。
   
    ![](./media/app-service-web-get-started-languages/create-web-app-started.png)
5. 完成部署后，你应该会看到此通知消息。单击该消息即可打开部署的边栏选项卡。
   
    ![](./media/app-service-web-get-started-languages/create-web-app-finished.png)
6. 在“部署成功”边栏选项卡中，单击“资源”链接即可打开 Web 应用新的边栏选项卡。
   
    ![](./media/app-service-web-get-started-languages/create-web-app-resource.png)

## 将内容部署到你的 Web 应用
现在，让我们使用 Git 将一些内容部署到 Azure。

1. 在 Web 应用边栏选项卡中，向下滚动到“部署选项”或搜索它，然后单击它。
   
    ![](./media/app-service-web-get-started-languages/deploy-web-app-deployment-options.png)
2. 依次单击“选择源”>“本地 Git 存储库”>“确定”。
3. 返回 Web 应用边栏选项卡，单击“部署凭据”。
4. 设置部署凭据，然后单击“保存”。
5. 返回 Web 应用边栏选项卡，向下滚动到“属性”或搜索它，然后单击它。单击“Git URL”旁边的“复制”按钮。
   
    ![](./media/app-service-web-get-started-languages/deploy-web-app-properties.png)
   
    现在，可以使用 Git 部署你的内容。
6. 在命令行终端中，更改为工作目录 (`CD`) 并克隆示例应用，如下所示：
   
        git clone https://github.com/Azure-Samples/app-service-web-html-get-started.git
   
    ![在 Azure 中克隆第一个 Web 应用的应用示例代码](./media/app-service-web-get-started-languages/html-git-clone.png)
7. 更改为示例应用的存储库。例如，
   
        cd app-service-web-html-get-started
8. 使用之前几个步骤从门户中复制的 Azure 应用的 Git URL，为该应用远程配置 Git。
   
        git remote add azure <giturlfromportal>
9. 将示例代码部署到 Azure 应用，如同使用 Git 推送任何代码一样：
   
        git push azure master
   
    ![在 Azure 中将代码推送到第一个 Web 应用](./media/app-service-web-get-started-languages/html-git-push.png)

就这么简单！ 你的代码现在 Azure 中实时运行。在浏览器中，导航到 http://*&lt;appname>*.azurewebsites.net，了解它是否起作用。

## 更新应用
现在可以使用 Git 随时从项目（存储库）根目录进行推送，以更新实时站点。采用首次部署内容时的相同方法执行此操作。例如，每次想要推送已在本地测试的新更改时，只需从项目（存储库）根目录运行以下命令：

    git add .
    git commit -m "<your_message>"
    git push azure master

## 后续步骤
根据你的语言框架找到所需的开发和部署步骤：

> [!div class="op_single_selector"]
> * [.NET](web-sites-dotnet-get-started.md)
> * [PHP](app-service-web-php-get-started.md)
> * [Node.js](app-service-web-nodejs-get-started.md)
> * [Python](web-sites-python-ptvs-django-mysql.md)
> * [Java](web-sites-java-get-started.md)
> 
> 

或者，使用你的第一个 Web 应用执行更多操作。例如：

* 尝试[将代码部署到 Azure 的其他方法](../app-service-web/web-sites-deploy.md)。例如，若要从你的 GitHub 存储库之一进行部署，只需选择“GitHub”，而不是选择“部署选项”中的“本地 Git 存储库”。
* 使 Azure 应用上升到更高的层次。对用户进行身份验证。按需缩放。设置一些性能警报。所有这些操作只需按几下鼠标即可完成。请参阅[在第一个 Web 应用中添加功能](app-service-web-get-started-2.md)。

<!---HONumber=AcomDC_0921_2016-->