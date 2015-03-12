<properties linkid="web-sites-hybrid-connection" title="混合连接：将 Azure 网站连接到本地资源" pageTitle="混合连接：将 Azure 网站连接到本地资源" description="在 Azure 网站和使用静态 TCP 端口的本地资源之间创建连接" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

# 使用混合连接将 Azure 网站连接到本地资源

您可将 Microsoft Azure 上的网站连接到使用静态 TCP 端口的任何本地资源，例如 SQL Server、MySQL、HTTP Web API、移动服务和大多数自定义 Web 服务。本文将向您介绍如何在 Azure 网站和本地 SQL Server 数据库之间创建混合连接。
<!--
> [AZURE.NOTE] 混合连接功能的网站部分仅在[Azure 预览版门户](https://portal.azure.com)中有提供。若要在 BizTalk 服务中创建连接，请参阅[混合连接](http://go.microsoft.com/fwlink/p/?LinkID=397274)。  
-->

## 先决条件
- Azure 订阅。若要获取免费订阅，请参见[Azure 免费试用](/zh-cn/pricing/1rmb-trial/)。

- 若要通过混合连接使用本地 SQL Server 或 SQL Server Express 数据库，需要在静态端口上启用 TCP/IP。建议使用 SQL Server 的默认实例，因为它使用静态端口 1433。有关安装和配置与混合连接同时使用的 SQL Server Express 的信息，请参阅[使用混合连接从 Azure 网站连接到本地 SQL Server](http://go.microsoft.com/fwlink/?LinkID=397979)。

- 其上安装本地混合连接管理器代理的计算机将在本教程稍后部分介绍。

	- 必须能够通过端口 5671 连接至 Azure
	- 必须能够访问您的本地资源的 *hostname*:portnumber。 

> [AZURE.NOTE] 本文中的分步指南假定您从将托管本地混合连接代理的计算机使用浏览器。


## 本文内容##


[在 Azure 预览版门户中创建网站](#CreateSite)

[创建混合连接和 BizTalk 服务](#CreateHC)

[安装本地混合连接管理器以完成连接](#InstallHCM)

[后续步骤](#NextSteps)


## 在 Azure 预览版门户中创建网站 ##

> [AZURE.NOTE] 如果已在 Azure 预览版门户中创建了希望用于此教程的网站，可直接跳至[创建混合连接和 BizTalk 服务](#CreateHC)， 并从这里开始。

1. 在[Azure 门户](https://manage.windowsazure.cn)的左下角处，单击**新建**，然后选择**网站**。
	
	![New button][New]
	
	![New website](./media/web-sites-hybrid-connection-get-started/B02New Website.png)
	
2. 在**网站**分页上为您的网站提供名称，然后单击**创建**。 
	
	![Website name](./media/web-sites-hybrid-connection-get-started/B03 WebsiteCreationBlade.png)
	
3. 几分钟之后，网站创建成功并显示其网站分页。该分页是一个可垂直滚动的仪表板，便于您管理站点。
	
	![Website running](./media/web-sites-hybrid-connection-get-started/B04 WebsiteRunningBlade.png)
	
4. 若要验证该站点是否已激活，可单击**浏览**图标显示默认页面。
	
	![Click browse to see your website][Browse]
	
	![Default website page](./media/web-sites-hybrid-connection-get-started/B06Default WebsitePage.png)
	
接下来，您将为该网站创建混合连接和 BizTalk 服务。

<a name="CreateHC"></a>
## 创建混合连接和 BizTalk 服务 ##

1. 返回门户，向下滚动网站的分页并选择**混合连接**。
	
	![Hybrid connections][CreateHCHCIcon]
	
2. 在"混合连接"分页上，单击**添加**。
	
	![Add a hybrid connnection][CreateHCAddHC]
	
3. **添加混合连接**分页打开。因为这是您的第一个混合连接，所以预先选择了**新建混合连接**选项，**创建混合连接**分页将打开。
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	在**创建混合连接分页**上：
	- 在**名称**处为连接提供一个名称。
	- 在**主机名**处输入托管您的资源的本地计算机的名称。
	- 在**端口**处输入本地资源使用的端口号（SQL Server 默认实例使用 1433）。
	- 单击**Biz Talk 服务**


4. **Biz Talk 服务**分页打开。为 BizTalk 服务输入一个名称，然后单击**确定**。
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	**创建 Biz Talk 服务**分页关闭，您将返回**创建混合连接**分页。
	
5. 在"创建混合连接"分页上，单击**确定**。 
	
	![Click OK][CreateBTScomplete]
	
6. 完成这一流程后，门户中的"通知"区域将告知您连接已创建成功。
	
	![Success notification][CreateHCSuccessNotification]
	
7. 在网站分页上，**混合连接**图标现在显示已创建了一个混合连接。
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
此时，您已完成了云混合连接基础架构中最重要的一部分。接下来，您将创建相应的本地部分。

<a name="InstallHCM"></a>
## 安装本地混合连接管理器以完成连接 ##

1. 在网站分页上，单击"混合连接"图标 
	
	![Hybrid connections icon][HCIcon]
	
2. 在**混合连接**分页上，最近添加的终结点的**状态**栏显示**未连接**。单击该连接对其进行配置。
	
	![Not connected][NotConnected]
	
	"混合连接"分页打开。
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. 在该分页上，单击**侦听器安装**。
	
	![Click Listener Setup][ClickListenerSetup]
	
4. **混合连接属性**分页打开。在**本地混合连接管理器**下，选择**单击此处进行安装**。
	
	![Click here to install][ClickToInstallHCM]
	
5. 在"应用程序运行安全性警告"对话框中，选择**运行**以继续。
	
	![Choose Run to continue][ApplicationRunWarning]
	
6.	在**用户帐户控制**对话框中，选择**是**。
	
	![Choose Yes][UAC]
	
7. "混合连接管理器"开始下载并安装。 
	
	![Installing][HCMInstalling]
	
8. 安装完成后，单击**关闭**。
	
	![Click Close][HCMInstallComplete]
	
	在**混合连接**分页上，**状态**栏现在显示**已连接**。 
	
	![Connected Status][HCStatusConnected]

现在，混合连接基础架构已完成，接下来创建一个混合应用程序开始使用此架构。 

<a name="NextSteps"></a>
## 后续步骤 ##

- 有关创建使用混合连接的 ASP.NET web 应用程序的信息，请参见[使用混合连接从 Azure 网站连接到本地 SQL Server](http://go.microsoft.com/fwlink/?LinkID=397979)。

- 有关结合使用混合连接与移动服务的信息，请参见[使用混合连接从 Azure 移动服务连接到本地 SQL Server](/zh-cn/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/)。

### 其他资源

[混合连接概述](/zh-cn/documentation/articles/integration-hybrid-connection-overview/)
<!--
[Josh Twist 介绍混合连接（频道 9 视频）](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[混合连接网站](/zh-cn/services/biztalk-services/)

[BizTalk 服务："仪表板"、"监视"、"缩放"、"配置"和"混合连接"选项卡](/zh-cn/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)

[借助无缝应用程序可移植性构建真实环境混合云（频道 9 视频）](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[使用混合连接 从 Azure 移动服务连接到本地 SQL Server（频道 9 视频）](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)
-->
<!-- IMAGES -->
[New]:./media/web-sites-hybrid-connection-get-started/B01New.png
[Browse]:./media/web-sites-hybrid-connection-get-started/B05Browse.png
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
