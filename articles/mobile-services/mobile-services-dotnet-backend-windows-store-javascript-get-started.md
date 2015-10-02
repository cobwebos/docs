<properties 
	pageTitle="适用于 Windows 应用商店应用程序的移动服务入门 | 移动开发人员中心" 
	description="遵照本教程开始使用 Azure 移动服务以 C#、VB 或 JavaScript 语言进行 Windows 应用商店开发。" 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.date="08/08/2015"
	wacn.date=""/>


# <a name="getting-started"></a>移动服务入门

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

##概述
本教程说明如何使用 Azure 移动服务向通用 Windows 应用程序添加基于云的后端服务。在本教程中，你将要创建一个新的移动服务，以及一个在新移动服务中存储应用程序数据的、以 HTML 和 JavaScript 语言编写的简单*待办事项列表*应用程序。要创建的移动服务将使用支持的 .NET 语言，你可以使用 Visual Studio 来提供服务器端业务逻辑和管理移动服务。若要创建允许以 JavaScript 编写服务器端业务逻辑的移动服务，请参阅本主题中的 JavaScript 版本。

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../includes/mobile-services-windows-universal-get-started.md)]

##先决条件

若要完成本教程，您需要以下各项：

* 有效的 Azure 帐户。如果你没有帐户，可以创建一个试用帐户，只需几分钟即可完成。有关详细信息，请参阅 [Azure 试用](/pricing/1rmb-trial/)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>。可以使用免费试用版。


## 创建新的移动服务

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 创建新的通用 Windows 应用程序

创建移动服务后，你可以在管理门户中遵照一个简易的快速入门项目来创建新应用程序或修改现有应用程序，以连接到你的移动服务。

在本部分中，你将要创建一个连接到移动服务的新的通用 Windows 应用程序。

1. 在管理门户中单击“移动服务”，然后单击你刚刚创建的移动服务。
   
2. 在快速入门选项卡中，单击“选择平台”下的“Windows”，然后展开“创建新的 Windows 应用商店应用程序”。

   	![][6]

   	此时将显示三个简单步骤，描述如何创建与移动服务连接的 Windows 应用商店应用程序。

  	![][7]

3. 在本地计算机或虚拟机上下载并安装 <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>（如果尚未这么做）。

4. 在“下载并在本地运行应用程序和服务”下面，选择 Windows 应用商店应用程序的语言，然后单击“下载”。

  	随即将会下载一个解决方案，其中包含移动服务的项目，以及已连接到移动服务的示例_待办事项列表_应用程序的项目。将压缩的项目文件保存到本地计算机，并记下保存位置。


## 针对本地移动服务测试应用程序

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[AZURE.NOTE]你可以查看访问你的移动服务以查询和插入数据的代码，这些代码在 default.js 文件中。

## 发布移动服务

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

<ol start="4">
<li><p>在共享代码项目中，打开 default.js 文件，找到创建 <a href="http://msdn.microsoft.com/library/azure/jj554219.aspx" target="_blank">WindowsAzure.MobileServiceClient</a> 实例的代码，注释掉使用 <em>localhost</em> 创建此客户端的代码，然后取消注释使用如下所示远程移动服务 URL 创建客户端的代码：</p>

        <pre><code>var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXXXX-APPLICATION-KEY-XXXXXX"
        );</code></pre>

	<p>现在，客户端将会访问已发布到 Azure 的移动服务。</p></li>

<li><p>按 <strong>F5</strong> 键以重新构建项目并启动此应用。</p></li>

<li><p>在应用程序中的“插入 TodoItem”中键入有意义的文本（例如 <em>Complete the tutorial</em>），然后单击“保存”<strong></strong><strong></strong>。</p>

<p>这样可向在 Azure 中托管的新移动服务发送 POST 请求。</p>
</li>
<li><p>（可选）在通用 Windows 解决方案中，将默认启动项目更改为其他应用程序，然后再次按 <strong>F5</strong>。</p></li>


</ol>

有关通用 Windows 应用程序的详细信息，请参阅[通过单个移动服务支持多个设备平台](/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service#shared-vs)。

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-service-startup.png

[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-startup.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-publish.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with data]: /documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
[Get started with authentication]: /documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
[Get started with push notifications]: /documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: /documentation/articles/mobile-services-win8-javascript
[Management Portal]: https://manage.windowsazure.cn/
[JavaScript version]: /documentation/articles/mobile-services-windows-store-get-started
[Get started with data in Mobile Services using Visual Studio 2012]: /documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012

<!---HONumber=71-->