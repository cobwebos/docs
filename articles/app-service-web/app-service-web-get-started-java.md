---
title: "在 5 分钟内将第一个 Java Web 应用部署到 Azure | Microsoft Docs"
description: "了解如何通过部署一个示例应用，轻松地在应用服务中运行 Web 应用。 快速学会如何进行实际开发并立即查看结果。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/13/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 4fc33ba185122496661f7bc49d14f7522d6ee522
ms.openlocfilehash: 90c1099f7970fc490ca01f2d815679f967c46187


---
# <a name="deploy-your-first-java-web-app-to-azure-in-five-minutes"></a>在 5 分钟内将第一个 Java Web 应用部署到 Azure
本教程帮助用户将一个简单的 Java Web 应用部署到 [Azure App Service](../app-service/app-service-value-prop-what-is.md)。
应用服务可用于创建 Web 应用、[移动应用后端](/documentation/learning-paths/appservice-mobileapps/)和 [API 应用](../app-service-api/app-service-api-apps-why-best-platform.md)。

你可以： 

* 在 Azure App Service 中创建 Web 应用。
* 部署示例 Java 应用。
* 查看生产中实时运行的代码。

## <a name="prerequisites"></a>先决条件
* 获取 FTP/FTPS 客户端，例如 [FileZilla](https://filezilla-project.org/)。
* 获取 Microsoft Azure 帐户。 如果没有帐户，可以[注册免费试用帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或者[激活 Visual Studio 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

> [!NOTE]
> 无需 Azure 帐户即可 [试用应用服务](http://go.microsoft.com/fwlink/?LinkId=523751) 。 创建入门级应用并使用长达一小时 — 无需信用卡，也无需做出承诺。
> 
> 

<a name="create"></a>

## <a name="create-a-web-app"></a>创建 Web 应用
1. 使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com) 。
2. 在左侧菜单中，单击“新建” > “Web + 移动” > >“Web 应用”。
   
    ![](./media/app-service-web-get-started-languages/create-web-app-portal.png)
3. 在应用创建边栏选项卡中，将以下设置用于新的应用：
   
   * **应用名称**：键入唯一名称。
   * **资源组**：选择“新建”，为资源组指定名称。
   * **应用服务计划/位置**：单击进行配置，然后单击“新建”，设置应用服务计划的名称、位置和定价层。 可随意使用 **免费** 定价层。
     
     完成后，应用创建边栏选项卡的外观应如下所示：
     
     ![](./media/app-service-web-get-started-languages/create-web-app-settings.png)
4. 单击底部的“创建”  。 可以单击顶部的“通知”  图标查看进度。
   
    ![](./media/app-service-web-get-started-languages/create-web-app-started.png)
5. 完成部署后，你应该会看到此通知消息。 单击该消息即可打开部署的边栏选项卡。
   
    ![](./media/app-service-web-get-started-languages/create-web-app-finished.png)
6. 在“成功的部署”边栏选项卡中，单击“资源”链接，打开新 Web 应用的边栏选项卡。
   
    ![](./media/app-service-web-get-started-languages/create-web-app-resource.png)

## <a name="deploy-a-java-app-to-your-web-app"></a>将 Java 应用部署到 Web 应用
现在，我们使用 FTPS 将 Java 应用部署到 Azure 中。

1. 在 Web 应用边栏选项卡中，向下滚动到“ **应用程序设置** ”或搜索它，然后单击。 
   
    ![](./media/app-service-web-get-started-languages/set-java-application-settings.png)
2. 在“Java 版本”中，选择“Java 8”，单击“保存”。
   
    ![](./media/app-service-web-get-started-languages/set-java-application-settings.png)
   
    收到“已成功更新 Web 应用设置”通知时，请导航至 http://*&lt;appname>*.azurewebsites.net，查看操作中的默认 JSP servlet。
3. 返回 Web 应用边栏选项卡，向下滚动到“ **部署凭据** ”或搜索它，然后单击它。
4. 设置部署凭据，然后单击“ **保存**”。
5. 返回 Web 应用选项卡，单击“ **概述**”。 单击“FTP/部署用户名”和“FTPS 主机名”旁的“复制”按钮，复制这些值。
   
    ![](./media/app-service-web-get-started-languages/get-ftp-url.png)
   
    现在，可以使用 FTPS 部署 Java 应用。
6. 在 FTP/FTPS 客户端，使用你在上一步中复制的值登录到 Azure web 应用的 FTP 服务器。 使用以前创建的部署密码。
   
    下面的屏幕快照显示使用 FileZilla 登录。
   
    ![](./media/app-service-web-get-started-languages/filezilla-login.png)
   
    可能会看到来自 Azure 无法识别 SSL 证书的安全性警告。 继续操作。
7. 单击 [此链接](https://github.com/Azure-Samples/app-service-web-java-get-started/raw/master/webapps/ROOT.war) 将 WAR 文件下载到本地计算机。
8. 在 FTP/FTPS 客户端，在远程站点导航至 **/site/wwwroot/webapps** ，将下载到本地计算机上的 WAR 文件拖至远程目录。
   
    ![](./media/app-service-web-get-started-languages/transfer-war-file.png)
   
    单击“ **确定** ”将覆盖 Azure 中的文件。
   
   > [!NOTE]
   > 根据 Tomcat 的默认行为，/site/wwwroot/webapps 中的文件名 **ROOT.war** 提供根 Web 应用 (http://*&lt;appname>*.azurewebsites.net)，文件名 ***&lt;anyname>*.war** 提供命名的 Web 应用 (http://*&lt;appname>*.azurewebsites.net/*&lt;anyname>*)。
   > 
   > 

就这么简单！ 你的 Java 应用现在就在 Azure 中实时运行了。 在浏览器中，导航到 http://*&lt;appname>*.azurewebsites.net，了解它是否起作用。 

## <a name="make-updates-to-your-app"></a>更新应用
无论何时需要进行更新，只需将新的 WAR 文件上载到 FTP/FTPS 客户端的同一远程目录。

## <a name="next-steps"></a>后续步骤
[基于 Azure 应用商店中的模板创建 Java Web 应用](web-sites-java-get-started.md#marketplace)。 可以获取自己完全可以自定义的 Tomcat 容器，并获取熟悉的管理器 UI。 

直接在 [IntelliJ](app-service-web-debug-java-web-app-in-intellij.md) 或 [Eclipse](app-service-web-debug-java-web-app-in-eclipse.md) 中调试 Azure Web 应用。

或者，使用你的第一个 Web 应用执行更多操作。 例如：

* 尝试使用 [其他方法将代码部署到 Azure](web-sites-deploy.md)。 
* 使 Azure 应用上升到更高的层次。 对用户进行身份验证。 按需缩放。 设置一些性能警报。 所有这些操作只需按几下鼠标即可完成。 请参阅 [在第一个 Web 应用中添加功能](app-service-web-get-started-2.md)。




<!--HONumber=Dec16_HO1-->


