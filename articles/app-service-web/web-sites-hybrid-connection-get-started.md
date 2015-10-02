<properties 
	pageTitle="在 Azure App Service 中使用混合连接访问本地资源" 
	description="在 Azure App Service 中的 Web 应用与本地资源之间创建一个使用静态 TCP 端口的连接" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.date="08/11/2015" 
	wacn.date=""/>

#在 Azure App Service 中使用混合连接访问本地资源

您可将 Microsoft Azure 上的网站连接到使用静态 TCP 端口的任何本地资源，例如 SQL Server、MySQL、HTTP Web API、移动服务和大多数自定义 Web 服务。本文将向你介绍如何在 Azure 网站和本地 SQL Server 数据库之间创建混合连接。<!--
> [AZURE.NOTE] 混合连接功能的网站部分仅在[Azure Preview portal](https://portal.azure.com) 预览版门户中有提供。 若要在 BizTalk 服务中创建连接，请参阅混合连接 [Hybrid Connections](http://go.microsoft.com/fwlink/p/?LinkID=397274)。  

##先决条件
- Azure 订阅。若要获取免费订阅，请参阅 [Azure 免费试用](/zh-cn/pricing/1rmb-trial/)。

- 若要通过混合连接使用本地 SQL Server 或 SQL Server Express 数据库，需要在静态端口上启用 TCP/IP。建议使用 SQL Server 的默认实例，因为它使用静态端口 1433。有关安装和配置与混合连接同时使用的 SQL Server Express 的信息，请参阅[使用混合连接从 Azure 网站连接到本地 SQL Server](http://go.microsoft.com/fwlink/?LinkID=397979)。

- 其上安装本地混合连接管理器代理的计算机将在本教程稍后部分介绍。

	- 必须能够通过端口 5671 连接至 Azure
	- 必须能够访问你的本地资源的 *主机名*:*端口号*。 

> [AZURE.NOTE]本文中的分步指南假定您从将托管本地混合连接代理的计算机使用浏览器。


##本文内容##


[在 Azure 预览版门户中创建网站](#CreateSite)

[创建混合连接和 BizTalk 服务](#CreateHC)

[安装本地混合连接管理器以完成连接](#InstallHCM)

[后续步骤](#NextSteps)


## 在 Azure 门户中创建 Web 应用 ##

> [AZURE.NOTE]如果已在 Azure 预览门户中创建了希望用于此教程的网站，可直接跳至[创建混合连接和 BizTalk 服务](#CreateHC)。

1. 在 [Azure 门户](https://manage.windowsazure.cn)的左下角单击“新建”，然后选择“网站”。
	
	![新建按钮][New]
	
	![新建 Web 应用][NewWebsite]
	
2. 在“Web 应用”边栏选项卡上，提供一个 URL，然后单击“创建”。
	
	![网站名称][WebsiteCreationBlade]
	
3. 几分钟之后，Web 应用创建成功并显示其 Web 应用边栏选项卡。该分页是一个可垂直滚动的仪表板，便于您管理站点。
	
	![网站正在运行][WebSiteRunningBlade]
	
4. 若要验证该站点是否已激活，可单击“浏览”图标显示默认页面。
	
	![单击浏览查看你的 Web 应用][Browse]
	
	![默认 Web 应用页][DefaultWebSitePage]
	
接下来，你将为该 Web 应用创建混合连接和 BizTalk 服务。

<a name="CreateHC"></a>
## 创建混合连接和 BizTalk 服务 ##

1. 向下滚动 Web 应用的边栏选项卡并选择“混合连接”。
	
	![混合连接][CreateHCHCIcon]
	
2. 在“混合连接”边栏选项卡上，单击“添加”。
	
	![添加混合连接][CreateHCAddHC]
	
3. 将打开“混合连接”边栏选项卡。因为这是你的第一个混合连接，所以预先选择了“新建混合连接”选项，“创建混合连接”边栏选项卡已打开。
	
	![创建混合连接][TwinCreateHCBlades]
	
	在“创建混合连接”边栏选项卡上：- 对于“名称”，请提供连接的名称。- 对于“主机名”，请输入托管资源的本地计算机的名称。-对于“端口”，请输入本地资源使用的端口号（SQL Server 默认实例使用 1433）。- 单击“BizTalk 服务”


4. 将打开“创建 BizTalk 服务”边栏选项卡。为 BizTalk 服务输入一个名称，然后单击“确定”。
	
	![创建 BizTalk 服务][CreateHCCreateBTS]
	
	“创建 BizTalk 服务”边栏选项卡将会关闭，你将返回“创建混合连接”边栏选项卡。
	
5. 在“创建混合连接”边栏选项卡上，单击“确定”。
	
	![单击确定][CreateBTScomplete]
	
6. 完成这一流程后，门户中的“通知”区域将告知您连接已创建成功。

<!-- TODO

Everything fails at this step. I can't create a BizTalk service in the dogfood portal. I switch to the old portal
(full portal) and created the BizTalk service but it doesn't seem to let you connnect them - When you finish the
Create hybrid conn step, you get the following error
Failed to create hybrid connection RelecIoudHC. The 
resource type could not be found in the namespace 
'Microsoft.BizTaIkServices for api version 2014-06-01'.

The error indicates it couldn't find the type, not the instance.
![Success notification][CreateHCSuccessNotification]
-->
7. 在 Web 应用的边栏选项卡上，“混合连接”图标现在显示已创建了 1 个混合连接。
	
	![已创建一个混合连接][CreateHCOneConnectionCreated]
	
此时，您已完成了云混合连接基础架构中最重要的一部分。接下来，您将创建相应的本地部分。

<a name="InstallHCM"></a>
## 安装本地混合连接管理器以完成连接 ##

1. 在 Web 应用的边栏选项卡上，单击“混合连接”图标。 
	
	![混合连接图标][HCIcon]
	
2. 在“混合连接”边栏选项卡上，最近添加的终结点的“状态”列将显示“未连接”。单击该连接对其进行配置。
	
	![未连接][NotConnected]
	
	“混合连接”分页打开。
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. 在边栏选项卡上，单击“侦听器安装”。
	
	![单击侦听器安装][ClickListenerSetup]
	
4. 将打开“混合连接属性”边栏选项卡。在“在本地混合连接管理器”下，选择“单击此处安装”。
	
	![单击此处安装][ClickToInstallHCM]
	
5. 在“应用程序运行安全性警告”对话框中，选择“运行”以继续。
	
	![选择运行以继续][ApplicationRunWarning]
	
6.	在“用户帐户控制”对话框中，选择“是”。
	
	![选择是][UAC]
	
7. “混合连接管理器”开始下载并安装。
	
	![安装][HCMInstalling]
	
8. 安装完成后，单击“关闭”。
	
	![单击关闭][HCMInstallComplete]
	
	在“混合连接”边栏选项卡上，“状态”列现在显示“已连接”。
	
	![已连接状态][HCStatusConnected]

现在，混合连接基础架构已完成，接下来创建一个混合应用程序开始使用此架构。

<a name="NextSteps">
## 后续步骤 ##

- 有关创建使用混合连接的 ASP.NET web 应用程序的信息，请参阅[使用混合连接从 Azure 网站连接到本地 SQL Server](http://go.microsoft.com/fwlink/?LinkID=397979)。

- 有关结合使用混合连接与移动服务的信息，请参阅[使用混合连接从 Azure 移动服务连接到本地 SQL Server](/zh-cn/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/)。

###其他资源

[混合连接概述](/zh-cn/documentation/articles/integration-hybrid-connection-overview/) <!--
[Josh Twist introduces hybrid connections (Channel 9 video)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[Hybrid Connections  Website](/zh-cn/services/biztalk-services/)

[BizTalk Services: Dashboard, Monitor, Scale, Configure, and Hybrid Connection tabs](/zh-cn/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)

[Building a Real-World Hybrid Cloud with Seamless Application Portability (Channel 9 video)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[Connect to an on-premises SQL Server from Azure Mobile Services using Hybrid Connections (Channel 9 video)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)
-->
<!-- IMAGES -->
[New]:./media/web-sites-hybrid-connection-get-started/B01New.png
[New Website]:./media/web-sites-hybrid-connection-get-started/B02New Website.png
[ WebsiteCreationBlade]:./media/web-sites-hybrid-connection-get-started/B03 WebsiteCreationBlade.png
[ WebsiteRunningBlade]:./media/web-sites-hybrid-connection-get-started/B04 WebsiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-get-started/B05Browse.png
[Default WebsitePage]:./media/web-sites-hybrid-connection-get-started/B06Default WebsitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png

<!---HONumber=71-->