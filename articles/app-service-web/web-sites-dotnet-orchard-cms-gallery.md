<properties 
	pageTitle="从 Azure 应用商店创建 Orchard CMS Web 应用" 
	description="本教程教你如何在 Azure 中创建新 Web 应用，以及如何使用 Azure 门户启动和管理该 Web 应用。" 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.date="08/03/2015" 
	wacn.date=""/>

# 从 Azure 应用商店创建 Orchard CMS Web 应用

## 概述

应用商店提供了由 Microsoft、第三方公司和开源软件计划开发的各种流行 Web 应用程序。从应用商店中创建的 Web 应用程序不要求安装任何软件，只需通过浏览器连接到 [Azure 预览门户](http://go.microsoft.com/fwlink/?LinkId=529715)即可。有关应用商店中 Web 应用程序的详细信息，请参阅 [Azure 应用商店](/marketplace/)。

在本教程中，您将学习：

- 如何从应用商店创建新的 Web 应用

- 如何从管理门户启动和管理网站
 
你将构建一个使用默认模板的 Orchard CMS 网站。[Orchard](http://www.orchardproject.net/) 是一个基于 .NET 的免费、开源 CMS 应用程序，它允许你创建自定义的内容驱动型网站。Orchard CMS 包括一个扩展性框架，通过该框架你可以[下载附加模块和主题](http://gallery.orchardproject.net/)来自定义你的网站。下图显示你将要创建的 Orchard CMS 网站。

![Orchard 博客][13]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note)]

## 从应用商店创建 Orchard Web 应用

1. 登录到 [Azure 管理门户](http://manage.windowsazure.cn)。

2. 单击“新建”>“Web + 移动”>“Azure 应用商店”。
	
	![新建][1]

3. 单击“Web Apps”>“Orchard CMS”。在下一个边栏选项卡中，单击“创建”。
	
	![从应用商店创建][2]

4. 配置 Web 应用的 URL、App Service 计划、资源组和位置。完成后，单击“创建”。
	
	![配置应用][3]

	创建 Web 应用后，“通知”按钮将显示绿色的“成功”字样，Web 应用的边栏选项卡将会显示。

## 启动和管理 Orchard Web 应用

1. 在 Web 应用的边栏选项卡中，单击“浏览”打开 Web 应用的欢迎页。

	![“浏览”按钮][12]

2. 输入 Orchard 所需的配置信息，然后单击“完成设置”完成配置并打开 Web 应用主页。

	![登录到 Orchard][7]

	你将拥有一个新的 Orchard Web 应用，它看起来类似如下屏幕截图。

	![你的 Orchard Web 应用][13]

3. 按照 [Orchard 文档](http://docs.orchardproject.net/)中的详细介绍，了解更多关于 Orchard 的信息并配置新的 Web 应用。

## 后续步骤

* [创建使用身份验证和 SQL 数据库的 ASP.NET MVC 应用并将其部署到 Azure App Service](/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database)-- 了解如何通过 Visual Studio 在 Azure App Service 中创建新的 Web 应用。


[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png

<!---HONumber=71-->