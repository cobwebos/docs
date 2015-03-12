<properties linkid="develop-net-tutorials-get-started-vs2013" pageTitle="Azure 网站和 ASP.NET 入门" metaKeywords="" description="本教程说明如何在 Visual Studio 2013 中创建一个 ASP.NET Web 项目，并将其部署到 Azure 网站。在不到 15 分钟内，你即可在云中启动并运行一个应用程序。" metaCanonical="" services="web-sites" documentationCenter=".NET" title="Get started with Azure  Websites and ASP.NET" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />


# Azure 网站和 ASP.NET 入门

本教程说明如何使用 Visual Studio 2013 或 Visual Studio 2013 for Web Express 创建一个 ASP.NET Web 应用程序并将其部署到 Azure 网站。本教程假设你以前未使用过 Azure 或 ASP.NET。完成本教程之后，你将能够在云中启动并运行简单的 Web 应用程序。

学习内容：

* 如何通过安装 Azure SDK 来让你的计算机可以进行 Azure 开发。
* 如何创建 Visual Studio ASP.NET Web 项目，并将其部署到 Azure 网站。
* 如何对 Web 项目进行更改并重新部署应用程序。
* 如何使用 Azure 管理门户来监视和管理网站。

完成本教程需要有一个 Azure 帐户：

* 你可以[免费建立一个 Azure 帐户](/zh-cn/pricing/1rmb-trial/?WT.mc_id=A261C142F) - 获取可用来试用付费版 Azure 服务的信用额度，甚至在用完信用额度后，你仍可以保留帐户和使用免费的 Azure 服务（如网站）。
<!--
* 你可以[激活 MSDN 订户权益](/zh-cn/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) - MSDN 订阅每月为你提供可用来试用付费版 Azure 服务的信用额度。</li>
-->
> [AZURE.NOTE]
> 如果你想要在注册帐户之前开始使用 Azure 网站，请转到 <a href="https://trywebsites.chinacloudsites.cn">https://trywebsites.chinacloudsites.cn</a>你可以通过该网站在 Azure 网站中创建一个生存期较短的 ASP.NET 入门站点。

下图演示了完整的应用程序：

![Web site home page](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

### 教程章节

* [注册 Microsoft Azure（视频）](#video)
* [设置开发环境](#set-up-the-development-environment)
* [在 Visual Studio 中创建 ASP.NET Web 应用程序](#create-an-aspnet-web-application)
* [将应用程序部署到 Azure](#deploy-the-application-to-azure)
* [更改和重新部署](#make-a-change-and-redeploy)
* [在管理门户中监视和管理站点](#monitor-and-manage-the-site-in-the-management-portal)
* [后续步骤](#next-steps)

## <a name="video"></a>注册 Microsoft Azure（视频）

在此视频中，Scott Hanselman 演示了如何轻松注册 Microsoft Azure 免费试用版。（持续时间：1:58）

> [AZURE.VIDEO sign-up-for-microsoft-azure]

[WACOM.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## 创建 ASP.NET Web 应用程序

第一步是创建 Web 应用程序项目。Visual Studio 将自动创建稍后要将项目部署到的 Azure 网站。下图说明了这两个步骤的具体操作。

![Diagram showing project creation and deployment steps](./media/web-sites-dotnet-get-started-vs2013/createdeploydiagram.png)

1. 打开 Visual Studio 2013 或 Visual Studio 2013 Express for Web。

2. 在"文件"菜单中，单击"新建项目"。

3. 在"新建项目"对话框中，单击"C#">"Web">"ASP.NET Web 应用程序"。如果需要，可以选择"Visual Basic"。

3. 确保选择 **.NET Framework 4.5** 作为目标框架。

4. 将该应用程序命名为 **MyExample**，然后单击"确定"。

	![New Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newprojdb.png)

5. 在"新建 ASP.NET 项目"对话框中，选择"MVC"模板。如果想要使用 ASP.NET Web 窗体，可以选择"Web 窗体"模板。 

	[MVC 和 Web 窗体](http://www.asp.net/get-started/websites) 是用于开发网站的 ASP.NET 框架。对于本教程，你可以选择其中之一，但是，如果选择"Web 窗体"，则稍后必须编辑 *Default.aspx*，本教程将指导你编辑 *Index.cshtml*。

7. 单击"更改身份验证"。 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13changeauth.png)

6. 在"更改身份验证"对话框中，单击"无身份验证"，然后单击"确定"。

	![No Authentication](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)

	你要创建的示例应用程序将没有需要用户登录的功能。[后续步骤](#next-steps) 部分链接到了有关实现身份验证和授权的教程。

5. 在"新建 ASP.NET 项目"对话框中，保留"Azure"下的设置不变，然后单击"确定"。 

	![New ASP.NET Project dialog box](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)

	默认设置指定 Visual Studio 将为你的 Web 项目创建 Azure 网站。在本教程的下一部分中，你要将 Web 项目部署到新创建的网站。

	（复选框标题可能是"在云中托管"或"创建远程资源"。在任何一种情况下，效果都是相同的。）
	
5. 如果你尚未登录到 Azure，Visual Studio 将提示你登录。单击"登录"。

	![Sign in to Azure](./media/web-sites-dotnet-get-started-vs2013/signin.png)

6. 在"登录到 Azure"对话框中，输入用于管理 Azure 订阅的帐户的 ID 和密码。
	
	如果你已登录，"配置 Azure 站点设置"对话框将询问你要创建哪些资源。

	![Signed in to Azure](./media/web-sites-dotnet-get-started-vs2013/configuresitesettings.png)

3. Visual Studio 提供了默认的"站点名称"，Azure 将它用作应用程序 URL 的前缀。如果需要，请输入不同的站点名称。

	完整的 URL 将包含你在此处输入的内容和 *chinacloudsites.cn*（如"站点名称"文本框的旁边所示）。例如，如果站点名称是 `MyExample6442`, the URL will be `MyExample6442.chinacloudsites.cn`。该 URL 必须是唯一的。如果其他人已经使用你输入的 URL，你将看到右边是一个红色感叹号而不是绿色复选标记，你需要输入不同的站点名称。

4. 在"区域"下拉列表中，选择离你最近的位置。

	此设置指定你的网站将在哪个 Azure 数据中心运行。对于本教程，你做的选择不会带来明显的差异；但是，对于生产站点，你希望 Web 服务器和存储帐户处于同一区域，以最大程度地减少[延迟](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090)。

5. 让数据库字段保持不变。

	在本教程中，你将不使用数据库。本教程末尾的[后续步骤](#next-steps) 部分包含说明如何使用数据库的教程的链接。

6. 单击"确定"。

	几秒钟后，Visual Studio 将在指定的文件夹中创建 Web 项目，并在指定的 Azure 区域中创建网站。  

	"解决方案资源管理器"窗口将显示新项目中的文件和文件夹。

	![Solution Explorer](./media/web-sites-dotnet-get-started-vs2013/solutionexplorer.png)

	"Web 发布活动"窗口将显示已创建的站点。

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated1.png)

	你可以在服务器资源管理器中查看站点。

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/siteinse.png)



## 将应用程序部署到 Azure

7. 在"Web 发布活动"窗口中，单击"立即将 MyExample 发布到此站点"。

	![Web site created](./media/web-sites-dotnet-get-started-vs2013/GS13sitecreated.png)

	几秒钟后，将显示"发布 Web"向导。 

	Visual Studio 将项目部署到 Azure 所需的设置已保存在 *publish profile* 中。使用该向导可以查看和更改这些设置。

8. 在"发布 Web"向导的"连接"选项卡中，单击"验证连接"，以确保 Visual Studio 可以连接到 Azure 来部署 Web 项目。

	![Validate connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnection.png)

	在连接通过验证后，"验证连接"按钮旁边会出现一个绿色复选标记。 

9. 单击"下一步"。

	![Successfully validated connection](./media/web-sites-dotnet-get-started-vs2013/GS13ValidateConnectionSuccess.png)

10. 在"设置"选项卡中，单击"下一步"。

	![Settings tab](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)

	你可以接受"配置"和"文件发布选项"的默认值。

	你可以通过"配置"下拉列表部署用于远程调试的调试版本。[后续步骤](#next-steps) 部分链接到说明如何在调试模式下远程运行 Visual Studio 的教程。

	如果展开"文件发布选项"，你将看到多个设置，它们可让你处理不适用于本教程的方案：
 
	* 删除目标处的其他文件。
	  
		删除服务器上不在你项目中的任何文件。如果你在部署到站点，你必须将不同的项目部署到前面的一个项目，你可能需要它。

	* 在发布期间预编译。 
	 
		可以减少大型站点的首次请求预热时间。

	* 从 App_Data 文件夹中排除文件。 
	 
		用于测试。有时，你不想要将 App_Data 中的 SQL Server 数据库文件部署到生产环境。
	
11. 在"预览"选项卡中，单击"开始预览"。

	![StartPreview button in the Preview tab](./media/web-sites-dotnet-get-started-vs2013/GS13Preview.png)

	该选项卡会显示将复制到服务器的文件的列表。显示预览并不是发布应用程序所必需的，但它是一个需要了解的很有用的功能。

12. 单击"发布"。

	![StartPreview file output](./media/web-sites-dotnet-get-started-vs2013/GS13previewoutput.png)

	Visual Studio 开始执行将文件复制到 Azure 服务器的过程。

	"输出"和"Web 发布活动"窗口将显示已执行的部署操作并报告已成功完成部署。

	![Output window reporting successful deployment](./media/web-sites-dotnet-get-started-vs2013/PublishOutput.png)

	成功完成部署后，默认浏览器会自动打开并指向已部署网站的 URL，并且
	你创建的应用程序现在在云中运行。浏览器地址栏中的 URL 显示正在从 Internet 加载该站点。

	![Web site running in Azure](./media/web-sites-dotnet-get-started-vs2013/GS13deployedsite.png)

13. 关闭浏览器。

## 更改和重新部署

在本教程的本部分中，你要更改主页的 **h1** 标题，在开发计算机上本地运行项目以验证更改，然后将更改部署到 Azure。

2. 在"解决方案资源管理器"中打开 *Views/Home/Index.cshtml* 或 *.vbhtml* 文件，将 **h1** 标题从"ASP.NET"更改为"ASP.NET 和 Azure"，然后保存该文件。 

	![MVC index.cshtml](./media/web-sites-dotnet-get-started-vs2013/index.png)

	![MVC h1 change](./media/web-sites-dotnet-get-started-vs2013/mvcandazure.png)

1. 按 CTRL+F5，通过在本地计算机上运行站点来查看更新的标题。

	![Web site running locally](./media/web-sites-dotnet-get-started-vs2013/localandazure.png)

	 `http://localhost` URL 显示站点正在本地计算机上运行。默认情况下，应用程序在 IIS Express 中运行。IIS Express 是 IIS 的轻型版本，专为在 Web 应用程序部署过程中使用而设计。


1. 关闭浏览器。

1. 在"解决方案资源管理器"中，右键单击该项目并选择"发布"。

	![Chooose Publish](./media/web-sites-dotnet-get-started-vs2013/choosepublish.png)

	此时将"发布 Web"向导的"预览"选项卡。如果需要更改任何发布设置，你可以选择其他选项卡，但现在你要做的无非就是重新部署相同的设置。

2. 在"发布 Web"向导中，单击"发布"。

	![Click Publish](./media/web-sites-dotnet-get-started-vs2013/clickpublish.png)

	Visual Studio 将项目部署到 Azure，并在默认浏览器中打开该站点。

	![Changed site deployed](./media/web-sites-dotnet-get-started-vs2013/deployedandazure.png)

**提示：**可以启用"Web 单键发布"工具栏以更快地完成部署。单击"视图">"工具栏"，然后选择"Web 单键发布"。在工具栏上，你可以选择一个配置文件，单击一个按钮以发布，或单击一个按钮打开"发布 Web"向导。 

![Web One Click Publish Toolbar](./media/web-sites-dotnet-get-started-vs2013/weboneclickpublish.png)

## 在管理门户中监视和管理站点

[Azure 管理门户](/zh-cn/services/management-portal/)是一个 Web 界面，可让你管理和监视 Azure 服务，例如，刚刚创建的网站。在本教程的本部分，你将了解可在该门户中执行的某些操作。

1. 在浏览器中，转到 [http://manage.windowsazure.cn](http://manage.windowsazure.cn)，然后使用你的 Azure 凭据登录。

	门户将显示你的 Azure 服务的列表。

2. 单击你的网站的名称。

	![Portal home page with new web site called out](./media/web-sites-dotnet-get-started-vs2013/portalhome.png)
  
3. 单击"仪表板"选项卡。

	"仪表板"选项卡显示使用统计信息的概述，以及许多常用站点管理功能的链接。在"速览"下，还可以找到应用程序主页的链接。

	![Portal web site dashboard tab](./media/web-sites-dotnet-get-started-vs2013/portaldashboard.png)
  
	此时，站点没有过多的通信，因此可能不会在图形中显示任何内容。如果浏览到应用程序，刷新页面几次，然后刷新门户"仪表板"页的话，你将会看到显示了一些统计信息。单击"监视"选项卡可查看更多详细信息。

4. 单击"配置"选项卡。

	[配置](/zh-cn/documentation/articles/web-sites-configure//)选项卡允许你控制用于站点的 .NET 版本、启用 [WebSockets](/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) 和[诊断日志记录](/zh-cn/documentation/articles/web-sites-enable-diagnostic-log/)等功能、设置[连接字符串值](http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)，等等。 

	![Portal web site configure tab](./media/web-sites-dotnet-get-started-vs2013/portalconfigure.png)
  
5. 单击"缩放"选项卡。

	对于网站服务的付费层，[缩放](/zh-cn/documentation/articles/web-sites-scale/)选项卡允许你控制为 Web 应用程序提供服务的计算机的大小和数量，以处理流量的变化。

	你可以手动缩放或配置条件或计划以进行自动缩放。

	![Portal website scale tab](./media/web-sites-dotnet-get-started-vs2013/portalscale.png)

这只是管理门户的一部分功能。你还可以创建新网站、删除现有站点、停止和重新启动站点，以及管理其他类型的 Azure 服务，如数据库和虚拟机。  

**提示：**有一个新的预览版管理门户最终将取代你所用的门户。有关详细信息，请参阅 [Azure 预览版门户](/zh-cn/overview/preview-portal/)。

## 后续步骤

在本教程中，你已了解如何创建简单的 Web 应用程序并将其部署到 Azure 网站。你可以通过以下相关主题和资源来详细了解具体的操作。

* 部署 Web 项目的其他方法

	在本教程中，你已了解通过一项操作创建和部署站点的最快方法。有关使用 Visual Studio 或通过[源代码管理系统](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)[自动部署](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery)进行的其他部署方法的概述，请参阅[如何部署 Azure 网站](/zh-cn/documentation/articles/web-sites-deploy/")。 

	Visual Studio 还可以生成 Windows PowerShell 脚本让你自动完成部署。有关详细信息，请参阅[使一切自动化（使用 Azure 生成真实的云应用程序）](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)。

* 如何在 Visual Studio 中管理网站

	有关可在"服务器资源管理器"中执行的站点管理功能的信息，请参阅[在 Visual Studio 中对 Azure 网站进行故障排除](/zh-cn/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/)。
<!--
* 如何对网站进行故障排除

	Visual Studio 提供了相应的功能使你能够在 Azure 日志生成时轻松实时地对其进行查看。你还可以在 Azure 中采用调试模式远程运行。有关详细信息，请参阅[在 Visual Studio 中对 Microsoft Azure 网站进行故障故障](/zh-cn/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio)。
-->
* 如何添加数据库和授权功能

	有关说明如何访问数据库以及将某些站点功能限制给已授权用户的教程，请参阅[将包含成员资格、OAuth 和 SQL Database 的安全 ASP.NET MVC 应用程序部署到 Azure 网站](/zh-cn/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database)。

* 如何添加自定义域名和 SSL

	有关如何使用 SSL 和你自己的域（例如 www.contoso.com 而不是 contoso.chinacloudsites.cn）的信息，请参阅以下资源：

	* [为 Azure 网站配置自定义域名](/zh-cn/documentation/articles/web-sites-custom-domain-name/)。 
	* [为 Azure 网站启用 HTTPS](/zh-cn/documentation/articles/web-sites-configure-ssl-certificate/)

* 空闲超时后如何避免唤醒等待时间 

	默认情况下，网站如果已处于空闲状态相当一段时间，则是未加载的状态。此后，第一次请求必须等待重新加载站点。为了避免等待，你可以启用 AlwaysOn 功能。有关详细信息，请参阅[如何配置网站](/zh-cn/documentation/articles/web-sites-configure/)中的配置选项。

* 如何添加聊天之类的实时功能

	如果要在网站中包含实时功能（如聊天服务、游戏、股票行情等），你可以使用 [ASP.NET SignalR ](http://www.asp.net/signalr) 和 [WebSockets](http://azure.microsoft.com/blog/2013/11/14/introduction-to-websockets-on-windows-azure-web-sites/) 传输方法来获得最佳性能。有关详细信息，请参阅[将 SignalR 与 Microsoft Azure 网站结合使用](http://www.asp.net/signalr/overview/signalr-20/getting-started-with-signalr-20/using-signalr-with-windows-azure-web-sites)。 

* 如何为 Web 应用程序在 Azure 网站、云服务和 VM 之间做出选择

	在 Azure 中，你可以根据本教程中所述在网站中运行 Web 应用程序，也可以在云服务或虚拟机中运行 Web 应用程序。有关详细信息，请参阅 [Azure 执行模型](/zh-cn/documentation/articles/fundamentals-application-models/)和 [Azure 网站、云服务和 VM：何时使用何种产品？](/zh-cn/documentation/articles/choose-web-site-cloud-service-vm/)
