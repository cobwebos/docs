<properties linkid="develop-mobile-tutorials-get-started" pageTitle="适用于 Windows 应用商店应用程序的移动服务入门 | 移动开发人员中心" metaKeywords="" description="遵照本教程开始使用 Azure 移动服务以 C#、VB 或 JavaScript 语言进行 Windows 应用商店开发。 " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />



# <a name="getting-started"> </a>移动服务入门

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

本教程说明如何使用 Azure 移动服务向通用 Windows 应用程序添加基于云的后端服务。通用 Windows 应用程序解决方案包括 Windows 应用商店 8.1 和 Windows Phone 应用商店 8.1 应用程序的项目，以及常见的共享项目。有关详细信息，请参阅[生成面向 Windows 和 Windows Phone 的通用 Windows 应用程序](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx)。

在本教程中，你将要创建一个新的移动服务，以及一个在新移动服务中存储应用程序数据的简单 *待办事项列表*应用程序。要创建的移动服务将使用支持的 .NET 语言，你可以使用 Visual Studio 来提供服务器端业务逻辑和管理移动服务。若要创建允许以 JavaScript 编写服务器端业务逻辑的移动服务，请参阅本主题中的 JavaScript 后端版本。

>[WACOM.NOTE]本主题说明如何使用 Azure 管理门户创建新的移动服务项目和通用 Windows 应用程序。通过使用 Visual Studio 2013 Update 3，还可以向现有的 Visual Studio 解决方案添加新的移动服务项目。有关详细信息，请参阅[快速入门：添加移动服务（.NET 后端）](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)

>若要将移动服务添加到 Windows Phone 8.0 或 Windows Phone Silverlight 8.1 应用程序项目，请参阅[针对 Windows Phone 的数据处理入门](/zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data)。

[WACOM.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

若要完成本教程，你需要以下各项：

* 有效的 Azure 帐户。如果你没有帐户，可以注册 Azure 试用版。有关详细信息，请参阅 [Azure 免费试用](http://www.windowsazure.cn/zh-cn/pricing/1rmb-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.cn%2Fzh-cn%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>。可以使用免费试用版。

## 创建新的移动服务

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 创建新的通用 Windows 应用程序

创建移动服务后，你可以在管理门户中遵照一个简易的快速入门项目来创建新应用程序或修改现有应用程序，以连接到你的移动服务。 

在本部分中，你将要创建一个连接到移动服务的新的通用 Windows 应用程序。

1. 在管理门户中单击"移动服务"，然后单击你刚刚创建的移动服务。
   
2. 在快速入门选项卡中，单击"选择平台"下的"Windows"，然后展开"创建新的 Windows 应用商店应用程序"。

   	![][6]

   	此时将显示三个简单步骤，描述如何创建与移动服务连接的 Windows 应用商店应用程序。

  	![][7]

3. 在本地计算机或虚拟机上下载并安装 <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>（如果尚未这么做）。

4. 在"下载并在本地运行应用程序和服务"下面，选择 Windows 应用商店应用程序的语言，然后单击"下载"。 

  	随即将会下载一个解决方案，其中包含移动服务的项目，以及已连接到移动服务的示例待办事项列表应用程序的项目。将压缩的项目文件保存到本地计算机，并记下保存位置。

## 针对本地移动服务测试应用程序

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[WACOM.NOTE]你可以查看访问你的移动服务以查询和插入数据的代码，这些代码在 MainPage.xaml.cs 文件中。


## 发布移动服务

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


<ol start="4">
<li><p>在共享代码项目中，打开 App.xaml.cs 文件，找到创建 <a href="http://msdn.microsoft.com/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a> 实例的代码，注释掉使用 <em>localhost</em> 创建此客户端的代码，然后取消注释使用如下所示远程移动服务 URL 创建客户端的代码：</p>

        <pre><code>public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://todolist.azure-mobile.cn/",
            "XXXX-APPLICATION-KEY-XXXXX");</code></pre>

	<p>现在，客户端将会访问已发布到 Azure 的移动服务。</p></li>
</ol>

## 针对 Azure 中托管的移动服务测试应用程序

发布移动服务并将客户端连接到 Azure 中托管的远程移动服务后，我们可以使用 Azure 作为项存储来运行应用程序。

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]


## 后续步骤
完成快速入门后，请了解如何在移动服务中执行其他重要任务： 

* [数据处理入门]
  <br/>了解有关使用移动服务存储和查询数据的详细信息。

* [脱机数据同步入门]
  <br/>了解如何使用脱机数据同步来使应用程序保持较高的响应能力和稳健性。

* [身份验证入门]
  <br/>了解如何使用标识提供程序对应用程序的用户进行身份验证。

* [推送通知入门]
  <br/>了解如何向应用程序发送一条非常简单的推送通知。

* [移动服务 .NET 后端故障排除]
  <br/> 了解如何诊断和修复移动服务 .NET 后端可能会出现的问题。 

有关通用 Windows 应用程序的详细信息，请参阅[基于单个移动服务支持多个设备平台](/zh-cn/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs)。

<!-- Anchors. -->
[移动服务入门]:#getting-started
[创建新的移动服务]:#create-new-service
[定义移动服务实例]:#define-mobile-service-instance
[后续步骤]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-service-startup.png

[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-startup.png



<!-- URLs. -->
[数据处理入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[身份验证入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
[推送通知入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[移动服务 SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[管理门户]: https://manage.windowsazure.cn/
[JavaScript 后端版本]: /zh-cn/documentation/articles/mobile-services-windows-store-get-started
[使用 Visual Studio 2012 的移动服务中的数据处理入门]: /zh-cn/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012
[移动服务 .NET 后端故障排除]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/
