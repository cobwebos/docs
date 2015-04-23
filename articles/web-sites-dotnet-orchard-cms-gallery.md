<properties 
	pageTitle="在 Azure 中通过库创建 Orchard CMS 网站" 
	description="本教程教你如何在 Azure 中创建新网站，并让你了解如何使用管理门户启动和管理该网站。" 
	services="web-sites" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>
	
<tags ms.service="web-sites" ms.date="02/05/2015" wacn.date="04/11/2015"/>

# 在 Azure 中通过库创建 Orchard CMS 网站

## 概述

库提供了由 Microsoft、第三方公司和开源软件计划开发的各种流行 Web 应用程序。从库中创建的 Web 应用程序不要求安装任何软件，只需通过浏览器连接到 [Azure 管理门户](http://manage.windowsazure.cn)即可。有关库中 Web 应用程序的详细信息，请参阅 [Windows Web 应用库](http://www.microsoft.com/web/gallery/categories.aspx)。

在本教程中，您将学习：

- 如何从库中创建新网站

- 如何从管理门户启动和管理网站
 
你将构建一个使用默认模板的 Orchard CMS 网站。[Orchard](http://www.orchardproject.net/) 是一个基于 .NET 的免费、开源 CMS 应用程序，它允许你创建自定义的内容驱动型网站。Orchard CMS 包括一个扩展性框架，通过该框架你可以[下载附加模块和主题](http://gallery.orchardproject.net/)来自定义你的网站。下图显示你将要创建的 Orchard CMS 网站。

![Orchard blog][13]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note)]

<h2>从库中创建 Orchard 网站</h2>

1. 登录到 [Azure 管理门户 ](http://manage.windowsazure.cn)。

2. 单击该门户左下的"+ 新建"图标。
	
	![Create New][1]

3. 单击**"网站"**图标，然后单击**"从库中"**。
	
	![Create From Gallery][2]

4. 在列表中找到并单击"Orchard CMS"图标，然后单击箭头以继续。
	
	![Orchard from list][3]

5. 在**配置应用**页上，为所有字段输入或选择值：
	
- 输入选择的 URL 名称。	
- 选择最靠近你的用户的区域。（这将确保获得最佳性能。）

	![configure your app][4]

6. 单击框右下角的复选标记可启动新 Orchard CMS 网站的部署。

Azure 将发起构建和部署操作。在构建和部署网站的过程中，网站管理门户的底部会显示这些操作的状态。执行完所有操作后，将显示一条消息，指示已创建网站。

<h2>启动和管理 Orchard 网站</h2>

1. 单击"网站"页上的新网站的名称，然后单击门户底部的"浏览"以打开网站的欢迎页面。

	![launch dashboard][5]

	![browse button][12]

2. 输入 Orchard 所需的配置信息，然后单击"完成设置"完成配置并打开网站主页。

	![login to Orchard][7]

	你将拥有一个新的 Orchard 网站，它看起来类似如下屏幕快照。  

	![your Orchard site][13]

3. 按照 [Orchard 文档](http://docs.orchardproject.net/)中的详细介绍，了解更多关于 Orchard 的信息并配置你的新网站。

<h2>后续步骤</h2>
* [使用 Microsoft WebMatrix 开发和部署网站](/develop/net/tutorials/website-with-webmatrix/) -- 了解如何在 WebMatrix 中编辑 Azure 网站。 
* [将包含成员资格、OAuth 和 SQL Database 的安全 ASP.NET MVC 应用部署到 Azure 网站](/develop/net/tutorials/web-site-with-sql-database/)-- 了解如何从 Visual Studio 中创建新网站。

[1]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-01.png
[2]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-02.png
[3]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-03.png
[4]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-04.png
[5]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-05.png
[7]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-07.png
[12]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-12.png
[13]: ./media/web-sites-dotnet-orchard-cms-gallery/orchardgallery-08.png




<!--HONumber=51-->