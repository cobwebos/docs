<!-- not suitable for Mooncake -->

<properties 
	pageTitle="在 Azure Web 应用中使用混合连接访问本地资源" 
	description="在 Azure 中的 Web 应用与本地资源之间创建一个使用静态 TCP 端口的连接" 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.date="02/03/2016"
	wacn.date=""/>

#在 Azure 中使用混合连接访问本地资源

你可以将 Azure Web 应用连接到任何使用静态 TCP 端口的本地资源，例如 SQL Server、MySQL、HTTP Web API 和大多数自定义 Web 服务。本文将向你介绍如何在 Azure 和本地 SQL Server 数据库之间创建混合连接。

> [AZURE.NOTE] 混合连接功能的 Web Apps 部分仅在 [Azure 管理门户](https://manage.windowsazure.cn)中提供。若要在 BizTalk 服务中创建连接，请参阅[混合连接](/documentation/articles/integration-hybrid-connection-overview/)。
> 
> 此内容也适用于 Azure Web 应用中的 Mobile Apps。

## 先决条件
- Azure 订阅。若要获取免费订阅，请参阅 [Azure 试用版](/pricing/1rmb-trial/)。 
 
	如果想要在注册 Azure 帐户之前开始使用 Azure，请转到[试用 Azure Web 应用](https://tryappservice.azure.com/)，你可以在 Azure 中立即创建一个生存期较短的入门 Web 应用。你不需要使用信用卡，也不需要做出承诺。

- 若要通过混合连接使用本地 SQL Server 或 SQL Server Express 数据库，需要在静态端口上启用 TCP/IP。建议使用 SQL Server 的默认实例，因为它使用静态端口 1433。有关安装和配置与混合连接同时使用的 SQL Server Express 的信息，请参阅[使用混合连接从 Azure 网站连接到本地 SQL Server](/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server/)。

- 其上安装本地混合连接管理器代理的计算机将在本教程稍后部分介绍。

	- 必须能够通过端口 5671 连接至 Azure
	- 必须能够访问你的本地资源的 *主机名*:*端口号*。 

> [AZURE.NOTE] 本文中的分步指南假定您从将托管本地混合连接代理的计算机使用浏览器。


## 在 Azure 管理门户中创建 Web 应用 ##

> [AZURE.NOTE] 如果已在 Azure 管理门户中创建了希望用于此教程的 Web 应用或移动应用后端，可直接跳至[创建混合连接和 BizTalk 服务](#CreateHC)，然后在该处开始操作。

1. 在 [Azure 管理门户](https://manage.windowsazure.cn)的左上角，单击“新建”>“Web + 移动”>“Web 应用”。
	
	![新的 Web 应用][NewWebsite]
	
2. 在“Web 应用”边栏选项卡上，提供一个 URL，然后单击“创建”。
	
	![网站名称][WebsiteCreationBlade]
	
3. 几分钟之后，Web 应用创建成功并显示其 Web 应用边栏选项卡。该分页是一个可垂直滚动的仪表板，便于您管理站点。
	
	![运行中的网站][WebSiteRunningBlade]
	
4. 若要验证该站点是否已激活，可单击“浏览”图标显示默认页面。
	
	![单击“浏览”查看你的 Web 应用][Browse]
	
	![默认 Web 应用页面][DefaultWebSitePage]
	
接下来，你将为该 Web 应用创建混合连接和 BizTalk 服务。

<a name="CreateHC"></a>
## 创建混合连接和 BizTalk 服务 ##

1. 在 Web 应用边栏选项卡中，单击“所有设置”>“网络”>“配置混合连接终结点”。
	
	![混合连接][CreateHCHCIcon]
	
2. 在“混合连接”边栏选项卡上，单击“添加”。
	
	<!-- ![Add a hybrid connnection][CreateHCAddHC]
	-->
	
3. 将打开“混合连接”边栏选项卡。因为这是你的第一个混合连接，所以预先选择了“新建混合连接”选项，“创建混合连接”边栏选项卡已打开。
	
	![创建混合连接][TwinCreateHCBlades]
	
	在“创建混合连接”边栏选项卡上：
	- 在“名称”处为连接提供一个名称。
	- 在“主机名”处输入托管你的资源的本地计算机的名称。
	- 在“端口”处输入本地资源使用的端口号（SQL Server 默认实例使用 1433）。
	- 单击“Biz Talk 服务”


4. 将打开“创建 BizTalk 服务”边栏选项卡。为 BizTalk 服务输入一个名称，然后单击“确定”。
	
	![创建 BizTalk 服务][CreateHCCreateBTS]
	
	“创建 BizTalk 服务”边栏选项卡将会关闭，你将返回“创建混合连接”边栏选项卡。
	
5. 在“创建混合连接”边栏选项卡上，单击“确定”。
	
	![单击“确定”][CreateBTScomplete]
	
6. 完成这一流程后，门户中的“通知”区域将告知你连接已创建成功。
	<!--- TODO
	
	Everything fails at this step. I can't create a BizTalk service in the dogfood portal. I switch to the Management Portal
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

1. 在 Web 应用的边栏选项卡中，单击“所有设置”>“网络”>“配置混合连接终结点”。 
	
	![混合连接图标][HCIcon]
	
2. 在“混合连接”边栏选项卡上，最近添加的终结点的“状态”列将显示“未连接”。单击该连接对其进行配置。
	
	![未连接][NotConnected]
	
	“混合连接”分页打开。
	
	![未连接分页][NotConnectedBlade]
	
3. 在边栏选项卡上，单击“侦听器安装”。
	
	![单击侦听器安装][ClickListenerSetup]
	
4. 将打开“混合连接属性”边栏选项卡。在“在本地混合连接管理器”下，选择“单击此处安装”。
	
	![单击此处进行安装][ClickToInstallHCM]
	
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

>[AZURE.NOTE]以下部分介绍如何将混合连接用于 Mobile Apps .NET 后端项目。

## 配置 Mobile Apps .NET 后端项目以连接到 SQL Server 数据库

在 Azure Web 应用中，Mobile Apps .NET 后端项目就是一个 ASP.NET Web 应用，只不过额外安装和初始化了 Mobile Apps SDK。若要将 Web 应用用作 Mobile Apps 后端，必须[下载并初始化 Mobile Apps .NET 后端 SDK](/documentation/articles/app-service-mobile-dotnet-backend-how-to-use-server-sdk#install-sdk)。

对于 Mobile Apps，还需定义本地数据库的连接字符串，并修改后端以使用此连接。

1. 在 Visual Studio 的“解决方案资源管理器”中打开适用于 Mobile Apps .NET 后端的 Web.config 文件，找到 **connectionStrings** 节，添加类似于以下内容的新 SqlClient 项目，此项目指向本地 SQL Server 数据库：

	    <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

	请记得将这个字符串中的 `<**secure_password**>` 替换成你为 *HybridConnectionLogin* 创建的密码。

3. 在 Visual Studio 中单击“保存”以保存 Web.config 文件。

	> [AZURE.NOTE]在本地计算机上运行时，使用此连接设置。在 Azure 中运行时，则以门户中定义的连接设置覆盖此设置。

4. 展开“Models”文件夹并打开文件名以 *Context.cs* 结尾的数据模型文件。

6. 修改 **DbContext** 实例构造函数，以将 `OnPremisesDBConnection` 值传递到类似于以下代码段的基本 **DbContext** 构造函数：

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }

	现在，服务将使用新的 SQL Server 数据库连接。

## 更新移动应用后端以使用本地连接字符串

接下来，你需要为这个新的连接字符串添加应用设置，使之能够在 Azure 中使用。

1. 回到适用于移动应用的 Web 应用后端代码中的 [Azure 管理门户](https://manage.windowsazure.cn)，单击“所有设置”，然后单击“应用程序设置”。

3. 在“Web 应用设置”边栏选项卡中，向下滚动到“连接字符串”，然后添加新的名称为 `OnPremisesDBConnection` 且值类似于 `Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>` 的 **SQL Server** 连接字符串。

	将 `<**secure_password**>` 替换为本地数据库的安全密码。

	![本地数据库的连接字符串](./media/web-sites-hybrid-connection-get-started/set-sql-server-database-connection.png)

2. 按“保存”以保存混合连接以及刚刚创建的连接字符串。

此时可重新发布服务器项目，并使用现有的 Mobile Apps 客户端对新连接进行测试。数据将使用混合连接通过本地数据库读取和写入。

<a name="NextSteps">
## 后续步骤 ##

- 有关创建使用混合连接的 ASP.NET web 应用程序的信息，请参阅[使用混合连接从 Azure 网站连接到本地 SQL Server](/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server/)。

### 其他资源

[混合连接概述](/documentation/articles/integration-hybrid-connection-overview/)

[Josh Twist 介绍混合连接（频道 9 视频）](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[混合连接网站](/home/features/biztalk-services/)

[BizTalk 服务：“仪表板”、“监视”、“缩放”、“配置”和“混合连接”选项卡](/documentation/articles/biztalk-dashboard-monitor-scale-tabs)

[借助无缝应用程序可移植性构建真实环境混合云（频道 9 视频）](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[使用混合连接 从 Azure 移动服务连接到本地 SQL Server（频道 9 视频）](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## 发生的更改
* 有关从网站更改为 Azure 的指南，请参阅：[Azure 及其对现有 Azure 服务的影响](/documentation/services/web-sites/)

<!-- IMAGES -->
[New]: ./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]: ./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]: ./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]: ./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]: ./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]: ./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]: ./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]: ./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]: ./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]: ./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]: ./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]: ./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]: ./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]: ./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]: ./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]: ./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]: ./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]: ./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]: ./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]: ./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]: ./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]: ./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]: ./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png

<!---HONumber=Mooncake_0328_2016-->