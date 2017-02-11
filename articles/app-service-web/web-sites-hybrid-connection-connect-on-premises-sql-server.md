---
title: "使用混合连接从 Azure App Service 中的 Web 应用连接到本地 SQL Server"
description: "在 Microsoft Azure 上创建一个 Web 应用并将其连接到本地 SQL Server 数据库"
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: mollybos
ms.assetid: 2b4e0539-1a0b-4aa1-8a69-b4b053c3b2e5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7e3c1440cc2b669c574c2c0160a0d282b5f27bca


---
# <a name="connect-to-on-premises-sql-server-from-a-web-app-in-azure-app-service-using-hybrid-connections"></a>使用混合连接从 Azure App Service 中的 Web 应用连接到本地 SQL Server
混合连接可以将 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web 应用连接至使用静态 TCP 端口的本地资源。 受支持的资源包括 Microsoft SQL Server、MySQL、HTTP Web API、应用服务，以及大多数自定义 Web 服务。

在本教程中，你将了解如何在 [Azure 管理门户](http://go.microsoft.com/fwlink/?LinkId=529715)中创建应用服务 Web 应用、如何使用新的混合连接功能将 Web 应用连接至本地 SQL Server 数据库，以及如何创建将使用混合连接的简单 ASP.NET 应用程序并将该应用程序部署到应用服务 Web 应用。 在 Azure 上完成的 Web 应用将用户凭据存储在本地成员资格数据库中。 本教程假定读者之前未使用过 Azure 或 ASP.NET。

> [!NOTE]
> 如果您想要在注册 Azure 帐户之前开始使用 Azure App Service，请转到 [试用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，您可以在 App Service 中立即创建一个生存期较短的入门 Web 应用。 你不需要使用信用卡，也不需要做出承诺。
> 
> 混合连接功能的 Web 应用部分仅在 [Azure 门户](https://portal.azure.com)中提供。 若要在 BizTalk 服务中创建连接，请参阅[混合连接](http://go.microsoft.com/fwlink/p/?LinkID=397274)。  
> 
> 

## <a name="prerequisites"></a>先决条件
若要完成本教程，你需要安装以下产品。 所有这些产品都提供了免费版，方便你开始针对 Azure 进行开发且无需支付任何费用。

* **Azure 订阅** - 有关免费订阅，请参阅 [Azure 免费试用](/pricing/free-trial/)。
* **Visual Studio 2013** - 若要下载 Visual Studio 2013 免费试用版，请参阅 [Visual Studio 下载](http://www.visualstudio.com/downloads/download-visual-studio-vs)。 请先安装此产品，然后继续。
* **Microsoft .NET Framework 3.5 Service Pack 1** - 如果你的操作系统是 Windows 8.1、Windows Server 2012 R2、Windows 8、Windows Server 2012、Windows 7 或 Windows Server 2008 R2，可通过“控制面板”>“程序和功能”>“打开或关闭 Windows 功能”来启用它。 另外也可从 [Microsoft 下载中心](http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22)下载。
* **SQL Server 2014 Express with Tools** - 从 [Microsoft Web 平台数据库页](http://www.microsoft.com/web/platform/database.aspx)免费下载 Microsoft SQL Server Express。 选择 **Express**（而不是 LocalDB）版本。 **Express with Tools** 版本包含本教程中将要使用的 SQL Server Management Studio。
* **SQL Server Management Studio Express** - 其中包括上述 SQL Server 2014 Express with Tools 下载，但如果需要单独安装，可从 [SQL Server Express 下载页面](http://www.microsoft.com/web/platform/database.aspx)进行下载和安装。

本教程假定你具有 Azure 订阅、已安装 Visual Studio 2013 并且已安装或启用 .NET Framework 3.5。 本教程将向您介绍如何在与 Azure 混合连接功能协作良好的配置中安装 SQL Server 2014 Express（默认实例带有静态 TCP 端口）。 开始本教程之前，如果您还未安装 SQL Server，请先从上述位置下载 SQL Server 2014 Express with Tools。

### <a name="notes"></a>说明
若要通过混合连接使用本地 SQL Server 或 SQL Server Express 数据库，需要在静态端口上启用 TCP/IP。 SQL Server 上的默认实例使用静态端口 1433，而命名的实例则不使用该端口。

要在其上安装本地混合连接管理器代理的计算机：

* 必须具有与 Azure 的出站连接，通过：

| 端口 | Why |
| --- | --- |
| 80 |**必需**：用于 HTTP 端口进行证书验证，并可选择性地用于数据连接。 |
| 443 |**可选**：用于数据连接。 如果与 443 的出站连接不可用，则使用 TCP 端口 80。 |
| 5671 和 9352 |**建议**：但可选择性地用于数据连接。 请注意这种模式通常会产生较高的吞吐量。 如果与这些端口的出站连接不可用，则使用 TCP 端口 443。 |

* 必须能够访问你的本地资源的 *hostname*:*portnumber*。

本文中的分步指南假定您从将托管本地混合连接代理的计算机使用浏览器。

如果已在符合上述条件的配置和环境中安装了 SQL Server，则可以跳到后面的部分，开始在[本地创建 SQL Server 数据库](#CreateSQLDB)。

<a name="InstallSQL"></a>

## <a name="a-install-sql-server-express-enable-tcpip-and-create-a-sql-server-database-on-premises"></a>A. 安装 SQL Server Express，启用 TCP/IP 并在本地创建一个 SQL Server 数据库
本部分介绍了如何安装 SQL Server Express、如何启用 TCP/IP 以及如何创建数据库，以便你的 Web 应用程序可与 Azure 门户结合使用。

### <a name="install-sql-server-express"></a>安装 SQL Server Express
1. 若要安装 SQL Server Express，请运行下载的 **SQLEXPRWT_x64_ENU.exe** 或 **SQLEXPR_x86_ENU.exe** 文件。 SQL Server 安装中心向导显示。
   
    ![SQL Server 安装][SQLServerInstall]
2. 选择“全新 SQL Server 独立安装或向现有安装添加功能”。 按照说明操作，接受默认选项和设置，直至“实例配置”页面显示。
3. 在“实例配置”页面上，选择“默认实例”。
   
    ![选择默认实例][ChooseDefaultInstance]
   
    默认情况下，SQL Server 的默认实例在静态端口 1433 上侦听来自 SQL Server 客户端的请求，而这正是混合连接功能所需的。 命名实例使用不受混合连接支持的动态端口和 UDP。
4. 接受“服务器配置”页面上的默认值。
5. 在“数据库引擎配置”页面的“身份验证模式”下，选择“混合模式(SQL Server 身份验证和 Windows 身份验证)”并提供密码。
   
    ![选择混合模式][ChooseMixedMode]
   
    在本教程中，您将使用 SQL Server 身份验证。 请确保记住您输入的密码，稍后将用到此密码。
6. 按向导提示逐步执行完其余操作以完成安装。

### <a name="enable-tcpip"></a>启用 TCP/IP
若要启用 TCP/IP，需要用到随 SQL Server Express 一起安装的 SQL Server 配置管理器。 继续之前，请按照[针对 SQL Server 启用 TCP/IP 网络协议](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx)中的步骤进行操作。

<a name="CreateSQLDB"></a>

### <a name="create-a-sql-server-database-on-premises"></a>在本地创建 SQL Server 数据库
您的 Visual Studio Web 应用程序需要一个可由 Azure 访问的成员资格数据库。 这要求是 SQL Server 或 SQL Server Express 数据库（不是 MVC 模板默认使用的 LocalDB 数据库），因此，接下来需要创建成员资格数据库。

1. 在 SQL Server Management Studio 中，连接至刚才安装的 SQL Server。 （如果“连接到服务器”对话框没有自动显示，请导航至左侧窗格中的“对象资源管理器”、单击“连接”，然后单击“数据库引擎”。） ![连接到服务器][SSMSConnectToServer]
   
    对于“服务器类型”，请选择“数据库引擎”。 关于“服务器名称”，可使用 **localhost** 或正在使用的计算机的名称。 选择”SQL Server 身份验证”，然后以 sa 用户名和之前创建的密码进行登录。
2. 若要使用 SQL Server Management Studio 新建数据库，请在“对象资源管理器”中右键单击“数据库”，然后单击“新建数据库”。
   
    ![创建新的数据库][SSMScreateNewDB]
3. 在“新建数据库”对话框中，输入 MembershipDB 作为数据库名称，然后单击“确定”。
   
    ![提供数据库名称][SSMSprovideDBname]
   
    请注意，此时未对数据库进行任何更改。 成员资格信息稍后将在您运行 Web 应用程序时由该程序自动进行添加。
4. 在“对象资源管理器”中，如果展开“数据库”，你会看到成员资格数据库已经创建。
   
    ![MembershipDB 已创建][SSMSMembershipDBCreated]

<a name="CreateSite"></a>

## <a name="b-create-a-web-app-in-the-azure-portal"></a>B. 在 Azure 门户中创建 Web 应用
> [!NOTE]
> 如果已在 Azure 门户中创建了希望用于此教程的 Web 应用，可直接跳至[创建混合连接和 BizTalk 服务](#CreateHC)，然后在该处继续操作。
> 
> 

1. 在 [Azure 门户](https://portal.azure.com)中，依次单击“新建” > “Web + 移动” > “Web 应用”。
   
    ![新建按钮][New]
2. 配置 Web 应用，然后单击“创建”。
   
    ![网站名称][WebsiteCreationBlade]
3. 几分钟之后，Web 应用创建成功并显示其 Web 应用边栏选项卡。 该边栏选项卡是一个可垂直滚动的仪表板，便于你管理 Web 应用。
   
    ![运行中的网站][WebSiteRunningBlade]
   
    若要验证该 Web 应用是否已激活，可单击“浏览”图标显示默认页面。

接下来，你将为该 Web 应用创建混合连接和 BizTalk 服务。

<a name="CreateHC"></a>

## <a name="c-create-a-hybrid-connection-and-a-biztalk-service"></a>C. 创建混合连接和 BizTalk 服务
1. 返回门户中、转到设置，然后依次单击“网络” > “配置混合连接终结点”。
   
    ![混合连接][CreateHCHCIcon]
2. 在“混合连接”边栏选项卡上，依次单击“添加” > “新建混合连接”。
3. 在“创建混合连接”边栏选项卡上：
   
   * 对于“名称”，为连接提供一个名称。
   * 对于“主机名”，输入你的 SQL Server 主计算机的计算机名。
   * 对于“端口”，输入 1433（SQL Server 的默认端口）。
   * 依次单击“BizTalk 服务” > “新建 BizTalk 服务”，然后输入 BizTalk 服务的名称。
     
     ![创建混合连接][TwinCreateHCBlades]
4. 单击“确定”两次。
   
    此过程完成后，“通知”区域会闪现绿色的“成功”字样，“混合连接”边栏选项卡会显示新的状态为“未连接”的混合连接。
   
    ![已创建一个混合连接][CreateHCOneConnectionCreated]

此时，您已完成了云混合连接基础架构中最重要的一部分。 接下来，您将创建相应的本地部分。

<a name="InstallHCM"></a>

## <a name="d-install-the-on-premises-hybrid-connection-manager-to-complete-the-connection"></a>D. 安装本地混合连接管理器以完成连接
[!INCLUDE [app-service-hybrid-connections-manager-install](../../includes/app-service-hybrid-connections-manager-install.md)]

现在，混合连接基础架构已完成，接下来创建一个 Web 应用程序开始使用此架构。

<a name="CreateASPNET"></a>

## <a name="e-create-a-basic-aspnet-web-project-edit-the-database-connection-string-and-run-the-project-locally"></a>E. 创建基本的 ASP.NET Web 项目，编辑数据库连接字符串，然后在本地运行该项目
### <a name="create-a-basic-aspnet-project"></a>创建基本的 ASP.NET 项目
1. 在 Visual Studio 中的“文件”菜单上，新建一个项目：
   
    ![新建 Visual Studio 项目][HCVSNewProject]
2. 在“新建项目”对话框的“模版”部分中，依次选择“Web”和“ASP.NET Web 应用程序”，然后单击“确定”。
   
    ![选择 ASP.NET Web 应用程序][HCVSChooseASPNET]
3. 在“新建 ASP.NET 项目”对话框中，选择“MVC”，然后单击“确定”。
   
    ![选择 MVC][HCVSChooseMVC]
4. 创建完项目后，应用程序自述页面显示。 请先不要运行该 Web 项目。
   
    ![自述页面][HCVSReadmePage]

### <a name="edit-the-database-connection-string-for-the-application"></a>为应用程序编辑数据库连接字符串
在此步骤中，将编辑要告知应用程序本地 SQL Server Express 数据库位置的连接字符串。 连接字符串位于应用程序的 Web.config 文件中，其中包含该应用程序的配置信息。

> [!NOTE]
> 若要确保应用程序使用在 SQL Server Express 中创建的数据库而不是 Visual Studio 默认 LocalDB 中的数据库，在运行项目之前完成此步骤至关重要。
> 
> 

1. 在“解决方案资源管理器”中，双击 Web.config 文件。
   
    ![Web.config][HCVSChooseWebConfig]
2. 编辑“connectionStrings”部分以指向本地计算机上的 SQL Server 数据库，请遵循以下示例中的语法：
   
    ![连接字符串][HCVSConnectionString]
   
    撰写连接字符串时，请记住：
   
   * 如果连接至命名实例而不是默认实例（例如 YourServer\SQLEXPRESS），必须对 SQL Server 进行配置以使用静态端口。 有关配置静态端口的信息，请参阅[如何配置 SQL Server 以在特定端口上进行侦听](http://support.microsoft.com/kb/823938)。 默认情况下，命名实例使用 UDP 和动态端口，而混合连接对此不支持。
   * 建议指定端口（默认为 1433，如示例中所示）和连接字符串，以便可确保本地 SQL Server 启用了 TCP 并使用的是正确的端口。
   * 请记住使用 SQL Server 身份验证进行连接，以在连接字符串中指定用户 ID 和密码。
3. 在 Visual Studio 中单击“保存”保存 Web.config 文件。

### <a name="run-the-project-locally-and-register-a-new-user"></a>本地运行项目并注册新用户
1. 现在，通过单击“调试”下的浏览按钮本地运行新的 Web 项目。 本示例中使用的是 Internet Explorer。
   
    ![运行项目][HCVSRunProject]
2. 在默认网页的右上角，选择“注册”注册新帐户：
   
    ![注册新帐户][HCVSRegisterLocally]
3. 输入用户名和密码：
   
    ![输入用户名和密码][HCVSCreateNewAccount]
   
    这将自动在为应用程序保存成员资格信息的本机 SQL Server 上创建数据库。 表之一 (**dbo.AspNetUsers**) 保存类似刚才输入的 Web 应用用户凭据。 您将在本教程稍后部分看到此表。
4. 关闭默认网页的浏览器窗口。 这将停止 Visual Studio 中的应用程序。

现在可以进行下一步了 — 将应用程序发布到 Azure 并对其进行测试。

<a name="PubNTest"></a>

## <a name="f-publish-the-web-application-to-azure-and-test-it"></a>F. 将 Web 应用程序发布到 Azure 并对其进行测试
现在，将你的应用程序发布到应用服务 Web 应用，然后对其进行测试，了解如何使用之前配置的混合连接将你的 Web 应用连接至本地计算机上的数据库。

### <a name="publish-the-web-application"></a>发布 Web 应用程序
1. 可在 Azure 门户中下载应用服务 Web 应用的发布配置文件。 在 Web 应用的边栏选项卡上，单击“获取发布配置文件”，然后将文件保存到你的计算机。
   
    ![下载发布配置文件][PortalDownloadPublishProfile]
   
    接下来，将该文件导入您的 Visual Studio Web 应用程序。
2. 在 Visual Studio 中，右键单击“解决方案资源管理器”中的项目名称并选择“发布”。
   
    ![选择发布][HCVSRightClickProjectSelectPublish]
3. 在“发布 Web”对话框中的“配置文件”选项卡上，选择“导入”。
   
    ![导入][HCVSPublishWebDialogImport]
4. 浏览至下载的发布配置文件，将其选定，然后单击“确定”。
   
    ![浏览至配置文件][HCVSBrowseToImportPubProfile]
5. 你的发布信息将导入并显示在对话框的“连接”选项卡上。
   
    ![单击发布][HCVSClickPublish]
   
    单击“发布” 。
   
    发布完成后，浏览器启动并显示的 ASP.NET 应用程序对于你而言似曾相识 -- 只不过该网站运行于 Azure 云中！

接下来，将使用实时 Web 应用程序查看其正在使用的混合连接。

### <a name="test-the-completed-web-application-on-azure"></a>在 Azure 上测试已完成的 Web 应用程序
1. 在 Azure 上的网页的右上角，选择“登录”。
   
    ![测试登录][HCTestLogIn]
2. 你的应用服务 Web 应用现已连接至本地计算机上 Web 应用程序的成员资格数据库。 若要对此进行验证，请输入与之前在本地数据库中所输相同的凭据登录。
   
    ![问候语][HCTestHelloContoso]
3. 若要进一步测试新的混合连接，请注销你的 Azure Web 应用程序，并注册另一个用户。 提供新的用户名和密码，然后单击“注册”。
   
    ![测试注册其他用户][HCTestRegisterRelecloud]
4. 若要验证新用户的凭据是否已通过混合连接存储在本地数据库中，请在本地计算机上打开 SQL Management Studio。 在“对象资源管理器”中，依次展开“MembershipDB”数据库和“表”。 右键单击“dbo.AspNetUsers”成员资格表，然后选择“选择前 1000 行”查看结果。
   
    ![查看结果][HCTestSSMSTree]
5. 本地成员资格表现在显示了两个帐户 — 一个是本地创建的，另一个是在 Azure 云中创建的。 在云中创建的帐户已通过 Azure 的混合连接功能保存至本地数据库。
   
    ![本地数据库中注册的用户][HCTestShowMemberDb]

你现在已创建并部署了一个 ASP.NET Web 应用程序，此程序在本地 SQL Server 数据库与 Azure 云中的 Web 应用之间使用混合连接。 祝贺你！

## <a name="see-also"></a>另请参阅
[混合连接概述](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist 介绍混合连接（频道 9 视频）](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[混合连接概述](/services/biztalk-services/)

[BizTalk 服务：“仪表板”、“监视”、“缩放”、“配置”和“混合连接”选项卡](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)

[借助无缝应用程序可移植性生成真实环境混合云（频道 9 视频）](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[在 Azure App Service 中使用混合连接访问本地资源](web-sites-hybrid-connection-get-started.md)

[ASP.NET 标识概述](http://www.asp.net/identity)

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

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



<!--HONumber=Nov16_HO3-->


