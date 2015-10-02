<properties 
	pageTitle="从 Microsoft Azure 中的应用商店创建 Umbraco Web 应用" 
	description="创建 Umbraco 内容管理系统并将其部署到 Azure App Service Web Apps。" 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.date="08/03/2015" 
	wacn.date=""/>

#从 Microsoft Azure 中的应用商店创建 Umbraco Web 应用#

Umbraco CMS 是一种功能齐备的开源内容管理系统，可用于创建各种规模的应用程序。Azure 应用商店提供了众多由 Microsoft、第三方公司以及开源软件计划开发的流行 Web 应用程序。凭借此库，无论是通过应用初学者工具包还是通过集成你自己的设计，只需几分钟就可创建出一个 Umbraco CMS 应用。

本文所展示的 Azure 预览门户极大地简化了资源管理。Azure 预览门户通过将来自 Microsoft 及其合作伙伴的跨平台工具、技术以及服务放置在单一工作区中加速了软件交付流程。你可以将整个应用程序作为单个资源组进行创建、管理以及分析，而不是使用独立的资源，如 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps、Visual Studio 项目或数据库。

在本教程中，您将学习：

- 如何使用 Azure 预览门户通过应用商店创建新的 Web 应用
- 如何通过使用 Umbraco CMS 构建博客网站 

##通过 Azure 预览门户中的应用商店创建 Web 应用

1. 登录 [Azure 预览门户](https://portal.azure.com/)。

2. 选择“应用商店”图标。
	
3. 在“应用商店”中，选择“Web + 移动”选项卡，然后选择“Umbraco CMS”。
	
4. 若要创建新的 Umbraco CMS Web 应用，请单击“创建”。
	
5. 接下来，配置与 Umbraco CMS 关联的所有资源。在本例中，资源是一个 Web 应用和一个 SQL Server 数据库。首先，选择“Web 应用”以配置 Web 应用设置，如“URL”、“App Service 计划”、“Web 应用设置”和“位置”。
	
	![配置资源][04AppSettings]
	
6. 现在，配置数据库。选择“数据库”，然后选择“服务器”。本示例将为 Azure 上的数据库创建 SQL Server。
	
	![在 Azure 上创建 SQL Server][05NewServer]
	
7. 配置了 Web 应用和数据库后，可通过单击第一个“Umbraco CMS”边栏选项卡底部的“创建”（如上图所示）开始部署应用程序。
	
	![单击创建][06UmbracoCMSGroup]
	
完成部署后，门户将显示 Umbraco CMS Web 应用资源组的边栏选项卡。在“摘要”部分中，单击 Web 应用名称可以该 Web 应用的属性。同样在“摘要”部分中，可以选择数据库资源来查看关联数据库的属性。

## 启动并配置 Umbraco CMS Web 应用 ##

1. 在 Web 应用的详细信息边栏选项卡上，单击“浏览”以浏览你的 Web 应用。
	
	![浏览到您的站点][08UmbracoCMSGroupRunning]
	
2. 在你浏览 Web 应用时，Umbraco CMS 将启动其安装程序向导。输入请求的信息，然后单击“自定义”。
	
	![安装 Umbraco 向导][09InstallUmbraco7]
	
3. 为 Umbraco 将使用的数据库输入详细的连接和身份验证信息。选择“Microsoft SQL Azure”作为数据库类型。你可以从 Web 应用的“站点设置”部分获取数据库的连接字符串。
	
	![配置您的数据库][10ConfigureYourDatabase]
	
4. 如果您是初次接触 Umbraco CMS，可选择初学者网站工具包。否则，请单击“不，谢谢，我不想安装初学者网站”。
	
	![安装初学者网站][11InstallAStarterWebsite]
	
5. Umbraco 安装程序将为您的应用程序完善设置。应用程序配置完成后，您将重定向至 Umbraco CMS 管理仪表板。
	
	![Umbraco CMS 仪表板][14FriendlyCMS]
	
6. 现在，创建一个要发布的示例文本页面。依次选择“内容”、“溢出”和“TextPage”。
	
	![创建文本页面][15CreateItemUnderOverflow]
	
7. 为文本页面输入标题和一些内容，如下所示。完成后，单击“保存并发布”。
	
	![输入标题和一些内容][16EnterAName]
	
8. 页面发布时请稍候。发布完成后，您会在屏幕右下方收到一个小警报。现在，可以在你的 Web 应用上浏览新内容了。
	
	![已发布的网站页面][17MyPage]
	

就这么简单！ 短短几分钟之内，你已使用 Umbraco CMS 成功创建了博客 Web 应用。

##其他资源

[Umbraco 文档](http://our.umbraco.org/documentation)

[Umbraco 视频教程](https://umbraco.com/help-and-support/video-tutorials.aspx)

[Azure 门户文档](../preview-portal.md)

[Azure 门户（第 9 频道）](http://channel9.msdn.com/Blogs/Windows-Azure/Azure-Preview-portal)

[Azure App Service Web Apps 文档](/documentation/services/websites/)

## 发生的更改
* 有关从网站更改为 App Service 的指南，请参阅：[Azure App Service 及其对现有 Azure 服务的影响](http://go.microsoft.com/fwlink/?LinkId=529714)
* 有关从门户更改为预览门户的指南，请参阅：[有关在预览门户中导航的参考](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE]如果您想要在注册 Azure 帐户之前开始使用 Azure App Service，请转到[试用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，您可以在 App Service 中立即创建一个生存期较短的入门 Web 应用。你不需要使用信用卡，也不需要做出承诺。


<!-- IMAGES -->
[01Startboard]: ./media/web-sites-gallery-umbraco/01Startboard.PNG
[02WebGallery]: ./media/web-sites-gallery-umbraco/02WebGallery.PNG
[03UmbracoCMS]: ./media/web-sites-gallery-umbraco/03UmbracoCMS.PNG
[04AppSettings]: ./media/web-sites-gallery-umbraco/04AppSettings.PNG
[05NewServer]: ./media/web-sites-gallery-umbraco/05NewServer.PNG
[06UmbracoCMSGroup]: ./media/web-sites-gallery-umbraco/06UmbracoCMSGroup.PNG
[07UmbracoCMSGroupBlade]: ./media/web-sites-gallery-umbraco/07UmbracoCMSGroupBlade.PNG
[08UmbracoCMSGroupRunning]: ./media/web-sites-gallery-umbraco/08UmbracoCMSGroupRunning.PNG
[09InstallUmbraco7]: ./media/web-sites-gallery-umbraco/09InstallUmbraco7.png
[10ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/10ConfigureYourDatabase.png
[11InstallAStarterWebsite]: ./media/web-sites-gallery-umbraco/11InstallAStarterWebsite.png
[12ConfigureYourDatabase]: ./media/web-sites-gallery-umbraco/12ConfigureYourDatabase.png
[14FriendlyCMS]: ./media/web-sites-gallery-umbraco/14FriendlyCMS.PNG
[15CreateItemUnderOverflow]: ./media/web-sites-gallery-umbraco/15CreateItemUnderOverflow.PNG
[16EnterAName]: ./media/web-sites-gallery-umbraco/16EnterAName.PNG
[17MyPage]: ./media/web-sites-gallery-umbraco/17MyPage.PNG

<!---HONumber=71-->