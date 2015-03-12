<properties pageTitle="使用 Azure 移动服务开发 iOS 应用程序入门" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="遵照本教程开始使用 Azure 移动服务进行 iOS 开发。 " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

# <a name="getting-started"> </a>移动服务入门

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

本教程说明如何使用 Azure 移动服务向 iOS 应用程序添加基于云的后端服务。在本教程中，你将要创建一个新的移动服务，以及一个在新移动服务中存储应用程序数据的简单待办事项列表应用程序。要创建的移动服务将使用支持的 .NET 语言，你可以使用 Visual Studio 来提供服务器端业务逻辑和管理移动服务。若要创建允许以 JavaScript 编写服务器端业务逻辑的移动服务，请参阅本主题中的 [JavaScript 后端版本]。

以下是完成的应用程序的屏幕快照：

![][0]

完成本教程需要安装 XCode 4.5 和 iOS 5.0 或更高版本。

<div class="dev-callout"><strong>注意</strong> <p>若要完成本教程，你需要一个 Azure 帐户。如果你没有帐户，可以注册 Azure 试用版并取得多达 10 个免费的移动服务，即使在试用期结束之后仍可继续使用这些服务。有关详细信息，请参阅 <a href="http://www.windowsazure.cn/zh-cn/pricing/1rmb-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.cn%2Fzh-cn%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Azure 免费试用</a>。</p></div>

## <a name="create-new-service"> </a>创建新的移动服务

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 将移动服务下载到本地计算机

在创建移动服务后，请下载可在本地计算机或虚拟机上运行的个性化移动服务项目。

1. 单击刚刚创建的移动服务，在快速入门选项卡中单击"选择平台"下的"iOS"，然后展开"创建新的 iOS 应用程序"。

	![][1]

2. 如果你尚未安装 Visual Studio，请下载和安装 Visual Studio Professional 2013 或更高版本。

3. 在"下载你的服务并将其发布到云"下面单击"下载"。

	这样可以下载实现你的移动服务的 Visual Studio 项目。将压缩的项目文件保存到本地计算机，并记下保存位置。

4. 另外，请下载发布配置文件，将下载的文件保存到本地计算机，然后记下保存位置。

## 测试移动服务

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## 发布移动服务

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## 创建新的 iOS 应用程序

在本部分中，你将要创建一个连接到移动服务的新的 iOS 应用程序。

1. 在管理门户中单击"移动服务"，然后单击你刚刚创建的移动服务。

2. 在快速入门选项卡中，单击"选择平台"下的"iOS"，然后展开"创建新的 iOS 应用程序"。

3. 下载并安装 [Xcode] v4.4 或更高版本（如果尚未这么做）。

4. 在"下载并运行应用程序"下面单击"下载"。

  	随即将会下载已连接到移动服务的示例待办事项列表应用程序的项目，以及移动服务 iOS SDK。将压缩的项目文件保存到本地计算机，并记下保存位置。

## 运行新的 iOS 应用程序

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

本主题说明了如何针对 Azure 中运行的移动服务运行新的客户端应用程序。在对本地计算机上运行的移动服务测试 iOS 应用程序之前，必须配置 Web 服务器和防火墙，以允许从 iOS 开发计算机进行访问。有关详细信息，请参阅[配置本地 Web 服务器以允许连接到本地移动服务](/zh-cn/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express)。

## <a name="next-steps"> </a>后续步骤
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

<!-- Anchors. -->
[移动服务入门]:#getting-started
[创建新的移动服务]:#create-new-service
[定义移动服务实例]:#define-mobile-service-instance
[后续步骤]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[数据处理入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[身份验证入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[推送通知入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push

[移动服务 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[管理门户]: https://manage.windowsazure.cn/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[JavaScript 后端版本]: /zh-cn/documentation/articles/mobile-services-ios-get-started 
