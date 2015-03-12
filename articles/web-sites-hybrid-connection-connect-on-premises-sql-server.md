<properties linkid="web-sites-hybrid-connection-getting-started" title="混合连接分步指南：从 Azure 网站连接到在本地 SQL Server" pageTitle="混合连接分步指南：从 Azure 网站连接到在本地 SQL Server" description="在 Microsoft Azure 上创建一个网站并将其连接至本地 SQL Server 数据库" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

# 使用混合连接从 Azure 网站连接至本地 SQL Server

## 介绍##
混合连接可以将 Microsoft Azure 网站连接至使用静态 TCP 端口的本地资源。受支持的资源包括 Microsoft SQL Server、MySQL、HTTP Web API、移动服务，以及大多数自定义 Web 服务。 

在本教程中，您将了解如何在 Azure 门户中创建网站、使用新的混合连接功能将该网站连接至您的本地 SQL Server 数据库以及创建将使用混合连接的简单 ASP.NET Web 应用程序并将该应用程序部署到 Azure 网站。在 Azure 上完成的网站将用户凭据存储在本地成员资格数据库中。本教程假定读者之前未使用过 Azure 或 ASP.NET。

<!--
> [AZURE.NOTE] 混合连接功能的网站部分仅在[Azure 预览版门户](https://portal.azure.com)中有提供。若要在 BizTalk 服务中创建连接，请参阅[混合连接](http://go.microsoft.com/fwlink/p/?LinkID=397274)。  
--¡·
## 先决条件##
若要完成本教程，你需要安装以下产品。所有这些产品都提供了免费版，方便你开始针对 Azure 进行开发且无需支付任何费用。

- **Azure 订阅** - 有关免费订阅，请参阅[Azure 免费试用](/zh-cn/pricing/1rmb-trial/)。

- **Visual Studio 2013** - 若要下载 Visual Studio 2013 免费试用版，请参阅[Visual Studio 下载](http://www.visualstudio.com/zh-cn/downloads/download-visual-studio-vs)。请先安装此产品，然后继续。

- **Microsoft .NET Framework 3.5 Service Pack 1**- 如果您的操作系统是 Windows 8.1、Windows Server 2012 R2、Windows 8、Windows Server 2012、Windows 7 或 Windows Server 2008 R2，可通过"控制面板">"程序和功能"> 打开或关闭 Windows 功能来启用它。另外，您可以从[Microsoft 下载中心](http://www.microsoft.com/zh-cn/download/details.aspx?displaylang=en&id=22)进行下载。

- **SQL Server 2014 Express with Tools**- 从[Microsoft Web 平台数据库页面](http://www.microsoft.com/web/platform/database.aspx)免费下载 Microsoft SQL Server Express。选择**Express**（而不是 LocalDB）版本。**Express with Tools**版本包含本教程中将要使用的 SQL Server Management Studio。

- **SQL Server Management Studio Express**- 其中包括上述 SQL Server 2014 Express with Tools 下载，但若需要单独安装，可从[SQL Server Express 下载页面](http://www.microsoft.com/web/platform/database.aspx)进行下载和安装。

本教程假定您订阅了 Azure、已安装 Visual Studio 2013 并安装或启用了 .NET Framework 3.5。本教程将向您介绍如何在与 Azure 混合连接功能协作良好的配置中安装 SQL Server 2014 Express（默认实例带有静态 TCP 端口）。开始本教程之前，如果您还未安装 SQL Server，请先从上述位置下载 SQL Server 2014 Express with Tools。

### 说明 ###
若要通过混合连接使用本地 SQL Server 或 SQL Server Express 数据库，需要在静态端口上启用 TCP/IP。SQL Server 上的默认实例使用静态端口 1433，而命名的实例则不使用该端口。 

要在其上安装本地混合连接管理器代理的计算机：

- 必须具有与 Azure 的出站连接，通过：

> <table border="1">
    <tr>
       <th><strong>Port</strong></th>
        <th>Why</th>
    </tr>
    <tr>
        <td>80</td>
        <td><strong>必选</strong> 用于证书验证的 HTTP 端，以及可选用于数据连接。</td>
    </tr>
    <tr>
        <td>443</td>
        <td><strong>可选</strong> 用于数据连接。如果与 443 的出站连接不可用，则使用 TCP 端口 80。</td>
    </tr>
	<tr>
        <td>5671 和 9352</td>
        <td><strong>建议</strong> 但可选用于数据连接。请注意这种模式通常会产生较高的吞吐量。如果与这些端口的出站连接不可用，则使用 TCP 端口 443。</td>
	</tr>
</table>

- 必须能够访问您的本地资源的 *hostname*:portnumber。 

本文中的分步指南假定您从将托管本地混合连接代理的计算机使用浏览器。

如果已在符合上述条件的配置和环境中安装了 SQL Server，则可以跳到后面的部分，开始[在本地创建 SQL Server 数据库](#CreateSQLDB)。

## 本文内容##
[A. 安装 SQL Server Express，启用 TCP/IP 并在本地创建一个 SQL Server 数据库](#InstallSQL)

[B. 在 Azure 预览版门户中创建网站](#CreateSite)

[C. 创建混合连接和 BizTalk 服务](#CreateHC)

[D. 安装本地混合连接管理器以完成连接](#InstallHCM)

[E. 创建基本的 ASP.NET Web 项目、编辑数据库连接字符串并本地运行项目](#CreateASPNET)

[F. 将 Web 应用程序发布到 Azure 并对其进行测试](#PubNTest)

<a name="InstallSQL"></a>
## A. 安装 SQL Server Express，启用 TCP/IP 并在本地创建一个 SQL Server 数据库 ##

本节将向您介绍如何安装 SQL Server Express、启用 TCP/IP 并创建一个数据库，以便您的 Web 应用程序可与 Azure 预览版环境结合使用。

### 安装 SQL Server Express###

1. 若要安装 SQL Server Express，请运行下载的**SQLEXPRWT_x64_ENU.exe**或**SQLEXPR_x86_ENU.exe**文件。SQL Server 安装中心向导显示。
	
	![SQL Server Install][SQLServerInstall]
	
2. 选择**全新 SQL Server 独立安装或向现有安装添加功能**。按照说明操作，接受默认选项和设置，直至**实例配置**页面显示。
	
3. 在**实例配置**页面上，选择**默认实例**。
	
	![Choose Default Instance][ChooseDefaultInstance]
	
	默认情况下，SQL Server 的默认实例在静态端口 1433 上侦听来自 SQL Server 客户端的请求，而这正是混合连接功能所需的。命名实例使用不受混合连接支持的动态端口和 UDP。
	
4. 接受**服务器配置**页面上的默认值。
	
5. 在**数据库引擎配置**页面上，在**身份验证模式**下选择**混合模式（SQL Server 身份验证和 Windows 身份验证）**并输入密码。
	
	![Choose Mixed Mode][ChooseMixedMode]
	
	在本教程中，您将使用 SQL Server 身份验证。请确保记住您输入的密码，稍后将用到此密码。
	
6. 按向导提示逐步执行完其余操作以完成安装。

### 启用 TCP/IP###
若要启用 TCP/IP，需要用到随 SQL Server Express 一起安装的 SQL Server 配置管理器。继续之前，请按照针对 SQL Server [启用 TCP/IP 网络协议](http://technet.microsoft.com/library/hh231672.aspx)的步骤进行。

<a name="CreateSQLDB"></a>
### 在本地创建 SQL Server 数据库###

您的 Visual Studio Web 应用程序需要一个可由 Azure 访问的成员资格数据库。这要求是 SQL Server 或 SQL Server Express 数据库（不是 MVC 模板默认使用的 LocalDB 数据库），因此，接下来需要创建成员资格数据库。 

1. 在 SQL Server Management Studio 中，连接至刚才安装的 SQL Server。（如果**连接到服务器**对话框没有自动显示，请导航到左侧窗格中的**对象资源管理器**，单击**连接**，然后单击**数据库引擎**。） 	
	![Connect to Server][SSMSConnectToServer]
	
	关于**服务器类型**，请选择**数据库引擎**。关于**服务器名称**，可使用**localhost**或正在使用的计算机的名称。选择**SQL Server 身份验证**，然后以 sa 用户名和之前创建的密码进行登录。 
	
2. 若要通过使用 SQL Server Management Studio 新建数据库，请右键单击"对象资源管理器"中的**数据库**，然后单击**新建数据库**。
	
	![Create new database][SSMScreateNewDB]
	
3. 在**新建数据库**对话框中，输入 MembershipDB 作为数据库名称，然后单击**确定**。 
	
	![Provide database name][SSMSprovideDBname]
	
	请注意，此时未对数据库进行任何更改。成员资格信息稍后将在您运行 Web 应用程序时由该程序自动进行添加。
	
4. 在"对象资源管理"中，如果展开**数据库**，您会看到成员资格数据库已经创建。
	
	![MembershipDB created][SSMSMembershipDBCreated]
	
<a name="CreateSite"></a>
## B.在 Azure 门户中创建网站 ##

> [WACOM.NOTE] 如果已在 Azure 门户中创建了希望用于此教程的网站，可直接跳至[创建混合连接和 BizTalk 服务]，(#CreateHC) 并从此处继续。

1. 在[Azure 门户](https://manage.windowsazure.cn)的左下角处，单击**新建**，然后选择**网站**。
	
	![New button][New]
	
	![New website][NewWebsite]
	
2. 在**网站**分页上为您的网站提供名称，然后单击**创建**。 
	
	![Website name][WebsiteCreationBlade]
	
3. 几分钟之后，网站创建成功并显示其网站分页。该分页是一个可垂直滚动的仪表板，便于您管理站点。
	
	![Website running][WebSiteRunningBlade]
	
4. 若要验证该站点是否已激活，可单击**浏览**图标显示默认页面。
	
	![Click browse to see your website][Browse]
	
	![Default website page][DefaultWebSitePage]
	
接下来，您将为该网站创建混合连接和 BizTalk 服务。

<a name="CreateHC"></a>
## C. 创建混合连接和 BizTalk 服务 ##

1. 返回到预览版门户，向下滚动网站的分页并选择**混合连接**。
	
	![Hybrid connections][CreateHCHCIcon]
	
2. 在"混合连接"分页上，单击**添加**。
	
	![Add a hybrid connnection][CreateHCAddHC]
	
3. **添加混合连接**分页打开。因为这是您的第一个混合连接，所以预先选择了**新建混合连接**选项，**创建混合连接**分页将打开。
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	在**创建混合连接分页**上：
	- 在**名称**处为连接提供一个名称。
	- 在**主机名**处输入您的 SQL Server 主机的计算机名。
	- 在**端口**处输入 1433（SQL Server 的默认端口）。
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
## D. 安装本地混合连接管理器以完成连接 ##

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

现在，混合连接基础架构已完成，接下来创建一个 Web 应用程序开始使用此架构。

<a name="CreateASPNET"></a>
## E. 创建基本的 ASP.NET Web 项目、编辑数据库连接字符串并本地运行项目 ##

### 创建基本的 ASP.NET 项目###
1. 在 Visual Studio 中的**文件**菜单上，创建一个新项目：
	
	![New Visual Studio project][HCVSNewProject]
	
2. 在**新建项目**对话框的**模版**部分中，依次选择**Web**和**ASP.NET Web 应用程序**，然后单击**确定**。
	
	![Choose ASP.NET Web Application][HCVSChooseASPNET]
	
3. 在**新建 ASP.NET 项目**对话框中，选择**MVC**，然后单击**确定**。
	
	![Choose MVC][HCVSChooseMVC]
	
4. 创建完项目后，应用程序自述页面显示。请先不要运行该 Web 项目。
	
	![Readme page][HCVSReadmePage]

### 为应用程序编辑数据库连接字符串###

在此步骤中，将编辑要告知应用程序本地 SQL Server Express 数据库位置的连接字符串。连接字符串位于应用程序的 Web.config 文件中，其中包含该应用程序的配置信息。 

> [AZURE.NOTE] 若要确保应用程序使用在 SQL Server Express 中创建的数据库而不是 Visual Studio 默认 LocalDB 中的数据库，在运行项目之前完成此步骤至关重要。

1. 在"解决方案资源管理器"中，双击 Web.config 文件。
	
	![Web.config][HCVSChooseWebConfig]
	
2. 编辑**connectionStrings**部分以指向本地计算机上的 SQL Server 数据库，请遵循以下示例中的语法： 
	
	![Connection string][HCVSConnectionString]
	
	撰写连接字符串时，请记住：
	
	- 如果连接至命名实例而不是默认实例（例如 YourServer\SQLEXPRESS），必须对 SQL Server 进行配置以使用静态端口。有关配置静态端口的信息，请参见[如何配置 SQL Server 以在特定端口上进行侦听](http://support.microsoft.com/kb/823938)。默认情况下，命名实例使用 UDP 和动态端口，而混合连接对此不支持。 
	
	- 建议指定端口（默认为 1433，如示例中所示）和连接字符串，以便可确保本地 SQL Server 启用了 TCP 并使用的是正确的端口。 
	
	- 请记住使用 SQL Server 身份验证进行连接，以在连接字符串中指定用户 ID 和密码。
	
3. 在 Visual Studio 中单击**保存**以保存 Web.config 文件。

### 本地运行项目并注册新用户 ###

1. 现在，通过单击"调试"下的浏览按钮本地运行新的 Web 项目。本示例中使用的是 Internet Explorer。
	
	![Run project][HCVSRunProject]
	
2. 在默认网页的右上方，选择**注册**注册新帐户：
	
	![Register a new account][HCVSRegisterLocally]
	
3. 输入用户名和密码：
	
	![Enter user name and password][HCVSCreateNewAccount]
	
	这将自动在为应用程序保存成员资格信息的本机 SQL Server 上创建数据库。表之一 (**dbo.AspNetUsers**) 保存类似刚才输入的网站用户凭据。您将在本教程稍后部分看到此表。
	
4. 关闭默认网页的浏览器窗口。这将停止 Visual Studio 中的应用程序。

现在可以进行下一步了 - 将应用程序发布到 Azure 并对其进行测试。

<a name="PubNTest"></a>
## F. 将 Web 应用程序发布到 Azure 并对其进行测试 ##

现在，将您的应用程序发布到 Azure 上的网站，然后对其进行测试以查看如何使用之前配置的混合连接将您的网站应用程序连接至本地计算机上的数据库。 

### 发布 Web 应用程序###

1. 可在 Azure 门户中下载网站的发布配置文件。在网站的分页上，选择**下载发布配置文件**，然后将文件保存到您的计算机。
	
	![Download publish profile][PortalDownloadPublishProfile]
	
	![Publish profile in downloads folder][HCVSPublishProfileInDownloadsFolder]
	
	接下来，将该文件导入您的 Visual Studio Web 应用程序。 
	
2. 在 Visual Studio 中，右键单击"解决方案资源管理器"中的项目名称并选择**发布**。
	
	![Select publish][HCVSRightClickProjectSelectPublish]
	
3. 在**发布 Web**对话框中的**配置文件**选项卡上，选择**导入**。
	
	![Import][HCVSPublishWebDialogImport]
	
4. 浏览至下载的发布配置文件，将其选定然后单击**确定**。
	
	![Browse to profile][HCVSBrowseToImportPubProfile]
	
5. 您的发布信息获得导入并显示在对话框的**连接**选项卡上。 
	
	![Click Publish][HCVSClickPublish]
	
	单击"发布"****。
	
	发布完成后，浏览器启动并显示的网站对于您而言似曾相识 - 只不过该网站运行于 Azure 云中！

接下来，将使用实时 Web 应用程序查看其正在使用的混合连接。

### 在 Azure 上测试已完成的 Web 应用程序 ###
1. 在 Azure 上的网页的右上角选择**登录**。
	
	![Test log in][HCTestLogIn]
	
2. 您的 Azure 网站现已连接至本地计算机上 Web 应用程序的成员资格数据库。若要对此进行验证，请输入与之前在本地数据库中所输相同的凭据登录。
	
	![Hello greeting][HCTestHelloContoso]
	
3. 若要进一步测试新的混合连接，请注销你的 Azure Web 应用程序，并注册另一个用户。提供新的用户名和密码，然后单击**注册**。
	
	![Test register another user][HCTestRegisterRelecloud]
	
4. 若要验证新用户的凭据是否已通过混合连接存储在本地数据库中，请在本地计算机上打开 SQL Management Studio。在"对象资源管理器"中，依次展开**MembershipDB**数据库和**表**。右键单击**dbo.AspNetUsers**成员资格表，选择**选择前 1000 行**以查看结果。
	
	![View the results][HCTestSSMSTree]
	
5. 本地成员资格表现在显示了两个帐户 - 一个是本地创建的，另一个是在 Azure 云中创建的。在云中创建的帐户已通过 Azure 的混合连接功能保存至本地数据库。
	
	![Registered users in on-premises database][HCTestShowMemberDb]
	
您现在已创建并部署了一个 ASP.NET Web 应用程序，此程序在本地 SQL Server 数据库与 Azure 云中的网站之间使用混合连接。祝贺您！

## 另请参阅##
[混合连接概述](/zh-cn/documentation/articles/integration-hybrid-connection-overview/)
<!--
[Josh Twist 介绍混合连接（频道 9 视频）](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[混合连接网站](/zh-cn/services/biztalk-services/)

[BizTalk 服务："仪表板"、"监视"、"缩放"、"配置"和"混合连接"选项卡](/zh-cn/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)
-->
[借助无缝应用程序可移植性构建真实环境混合云（频道 9 视频）](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[使用混合连接从 Azure 移动服务连接到本地 SQL Server](/zh-cn/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/)

[使用混合连接 从 Azure 移动服务连接到本地 SQL Server（频道 9 视频）](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

[ASP.NET 标识概述](http://www.asp.net/identity)

<!-- IMAGES -->
[SQLServerInstall]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
[ChooseDefaultInstance]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
[ChooseMixedMode]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
[SSMSConnectToServer]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
[SSMScreateNewDB]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
[SSMSprovideDBname]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
[SSMSMembershipDBCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
[New]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
[HCVSNewProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
[HCVSChooseASPNET]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
[HCVSChooseMVC]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
[HCVSReadmePage]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
[HCVSChooseWebConfig]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
[HCVSConnectionString]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
[HCVSRunProject]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
[HCVSRegisterLocally]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
[HCVSCreateNewAccount]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
[PortalDownloadPublishProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
[HCVSPublishProfileInDownloadsFolder]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
[HCVSRightClickProjectSelectPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
[HCVSPublishWebDialogImport]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
[HCVSBrowseToImportPubProfile]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
[HCVSClickPublish]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
[HCTestLogIn]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
[HCTestHelloContoso]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
[HCTestRegisterRelecloud]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
[HCTestSSMSTree]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
[HCTestShowMemberDb]:./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png
