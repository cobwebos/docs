<properties urlDisplayName="Get Started with Data" pageTitle="数据处理入门 (Windows 应用商店) | 移动开发人员中心" metaKeywords="" description="了解如何开始使用移动服务来利用 Windows 应用商店应用程序中的数据。" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/27/2014" ms.author="wesmc" />

# 将移动服务添加到现有应用程序

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

本主题说明如何使用 Azure 移动服务作为 Windows 应用商店应用程序的后端数据源。在本教程中，你将要为某个应用程序（该应用程序在内存中存储数据）下载一个 Visual Studio 2013 项目，创建一个新的移动服务，将该移动服务与该应用程序相集成，并查看运行该应用程序时对数据所做的更改。

在本教程中创建的移动服务是一个 .NET 后端移动服务。借助 .NET 后端，你可以对移动服务中的服务器端业务逻辑使用 .NET 语言和 Visual Studio，并且可以在本地计算机上运行和调试移动服务。若要创建允许以 JavaScript 编写服务器端业务逻辑的移动服务，请参阅本主题中的 JavaScript 后端版本。

>[AZURE.NOTE]本主题说明如何使用 Visual Studio Professional 2013 Update 3 中的工具将新的移动服务连接到通用 Windows 应用程序。你可以使用相同的步骤将移动服务连接到 Windows 应用商店或 Windows Phone 应用商店 8.1 应用程序。若要将移动服务连接到 Windows Phone 8.0 或 Windows Phone Silverlight 8.1 应用程序，请参阅[针对 Windows Phone 的数据处理入门](/zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data)。

> 如果你无法升级到 Visual Studio Professional 2013 Update 3，或者你要手动将移动服务项目添加到 Windows 应用商店应用程序解决方案，请参阅本主题的[此版本](/zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data)。

本教程将指导你完成以下基本步骤：

1. [下载 Windows 应用商店应用程序项目]
2. [在 Visual Studio 中创建新的移动服务]
3. [在本地测试移动服务项目]
4. [更新应用程序以使用移动服务]
5. [将移动服务发布到 Azure]
6. [针对 Azure 中托管的服务测试应用程序]
7. [查看 SQL Database 中存储的数据]

若要完成本教程，你需要以下各项：

* 有效的 Azure 帐户。如果你没有帐户，可以创建一个免费的试用帐户，只需几分钟即可完成。有关详细信息，请参阅 [Azure 免费试用](/zh-cn/pricing/1rmb-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.cn%2Fzh-cn%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F)。
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a>（Update 3 或更高版本）。 <br/>可以使用免费试用版。 

## <a name="download-app"></a>下载 GetStartedWithData 项目

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

## <a name="create-service"></a>在 Visual Studio 中创建新的移动服务

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

<ol start="7"><li><p>在解决方案资源管理器中，打开 GetStartedWithData.Shared 项目文件夹中的 App.xaml.cs 代码文件，并查看已添加到 Windows 应用商店应用程序条件编译块中 <strong>App</strong> 类的新静态字段，如以下示例中所示：</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.cn/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>此代码通过使用 <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">MobileServiceClient</a> 类的一个实例提供对应用程序中新移动服务的访问权限。客户端是通过提供新移动服务的 URI 和应用程序密钥来创建的。此静态字段可用于你的应用程序中的所有页面。</p>
</li>
<li><p>右键单击 Windows Phone 应用程序项目，单击"添加"<strong></strong>，单击"连接的服务..."<strong></strong>，选择刚刚创建的移动服务，然后单击"确定"<strong></strong>。 </p>
<p>将在共享的 App.xaml.cs 文件中添加相同的代码，但这一次会在 Windows Phone 应用程序条件编译块中添加。</p></li>
</ol>

此时，Windows 应用商店应用程序和 Windows Phone 应用商店应用程序都已连接到新的移动服务。下一步是测试新的移动服务项目。


## <a name="test-the-service-locally"></a>在本地测试移动服务项目

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]


## <a name="update-app"></a>更新应用程序以使用移动服务

在本部分中，你将要更新通用 Windows 应用程序，以将移动服务用作应用程序的后端服务。只需对 GetStartedWithData.Shared 项目文件夹中的 MainPage.xaml.cs 项目文件进行更改。 

[WACOM.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]


## <a name="publish-mobile-service"></a>将移动服务发布到 Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


## <a name="test-azure-hosted"></a>测试 Azure 中托管的移动服务

现在，我们可以针对 Azure 中托管的移动服务，测试这两个版本的通用 Windows 应用程序。

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

## <a name="view-stored-data"></a>查看 SQL Database 中存储的数据

[WACOM.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../includes/mobile-services-dotnet-backend-view-sql-data.md)]
 
**数据处理入门**教程到此结束。

## <a name="next-steps"> </a>后续步骤

本教程演示了使一个通用的 Windows 应用程序项目，以使用移动服务中的数据的基础知识。接下来，建议你完成下列教程之一，这些教程是基于本教程中创建的 GetStartedWithData 应用程序制作的：

* [使用脚本验证和修改数据]
  <br/>了解更多有关使用移动服务中的服务器脚本验证和更改从应用程序发送的数据的信息。

* [使用分页优化查询]
  <br/>了解如何使用查询中的分页控制单个请求中处理的数据量。

完成数据系列教程后，请试着学习下列教程之一：

* [身份验证入门]
  <br/>了解如何对应用程序用户进行身份验证。

* [推送通知入门]
  <br/>了解如何向应用程序发送一条非常简单的推送通知。

* [移动服务 .NET 操作方法概念性参考]
  <br/>了解有关如何将移动服务与 .NET 一起使用的详细信息。
  
<!-- Anchors. -->

[下载 Windows 应用商店应用程序项目]: #download-app
[在 Visual Studio 中创建新的移动服务]: #create-service
[在本地测试移动服务项目]: #test-the-service-locally
[更新应用程序以使用移动服务]: #update-app
[针对本地托管的服务测试应用程序]: #test-locally-hosted
[将移动服务发布到 Azure]: #publish-mobile-service
[针对 Azure 中托管的服务测试应用程序]: #test-azure-hosted
[查看 SQL Database 中存储的数据]: #view-stored-data
[后续步骤]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[使用脚本验证和修改数据]: /zh-cn/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts
[使用分页优化查询]: /zh-cn/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data
[移动服务入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[身份验证入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[推送通知入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/

[脱机数据同步入门]: /zh-cn/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data/

[Azure 管理门户]: https://manage.windowsazure.cn/
[管理门户]: https://manage.windowsazure.cn/
[移动服务 SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[开发人员代码示例网站]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[移动服务 .NET 操作方法概念性参考]: /zh-cn/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[MobileServiceClient 类]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 
