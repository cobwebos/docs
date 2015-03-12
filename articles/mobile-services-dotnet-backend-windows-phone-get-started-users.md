<properties pageTitle="身份验证入门 (Windows Phone) | 移动开发人员中心" metaKeywords="authentication, FAcebook, GOogle, Twitter, Microsoft Account, login" description="了解如何使用移动服务通过提供各种标识提供程序（包括 Google、Facebook、Twitter 和 Microsoft）对 Windows Phone 应用程序的用户进行身份验证。" metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="Glenn Gailey" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# 向移动服务应用程序添加身份验证

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

本主题说明如何通过 Windows Phone 应用程序对 Azure 移动服务中的用户进行身份验证。在本教程中，你将要使用移动服务支持的标识提供程序向快速入门项目添加身份验证。在移动服务成功完成身份验证和授权后，将显示用户 ID 值。

>[AZURE.NOTE] 本主题仅支持 Windows Phone 8.0 和 Windows Phone 8.1 Silverlight 应用程序。如果你有 Windows Phone 应用商店 8.1 或通用 Windows 应用程序，请遵循[本主题的通用 Windows 应用程序版本](/zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/)。

本教程将指导你完成在应用程序中启用身份验证的以下基本步骤：

1. [注册应用程序以进行身份验证并配置移动服务]
2. [将表权限限制给已经过身份验证的用户]
3. [向应用程序添加身份验证]
4. [在客户端上存储身份验证令牌]

本教程基于移动服务快速入门。因此，你还必须先完成[移动服务入门]教程。 


## <a name="register"></a>注册应用程序以进行身份验证并配置移动服务

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

## <a name="permissions"></a>将权限限制给已经过身份验证的用户

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li>在 Visual Studio 中打开你的客户端应用程序项目，并确保在 App.xaml.cs 中，**MobileServiceClient** 的实例已配置为使用移动服务的云 URL。</li> 
<li><p>按 F5 键运行这个基于快速入门的应用程序；验证启动该应用程序后，是否会引发状态代码为 401（"未授权"）的未处理异常。</p>
   
   	<p>发生此异常的原因是应用程序尝试以未经身份验证的用户身份访问移动服务，但 <em>TodoItem</em> 表现在要求身份验证。</p></li>
</ol>

接下来，你需要更新应用程序，以便在从移动服务请求资源之前对用户进行身份验证。

## <a name="add-authentication"></a>向应用程序添加身份验证

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

## <a name="tokens"></a>在客户端上存储授权令牌

[WACOM.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../includes/mobile-services-windows-phone-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>后续步骤

在下一教程[移动服务用户的服务端授权][使用脚本为用户授权]中，你将使用移动服务基于已进行身份验证的用户提供的用户 ID 值来筛选移动服务返回的数据。请在[移动服务 .NET 操作方法概念性参考]中了解有关如何将移动服务与 .NET 一起使用的详细信息

<!-- Anchors. -->
[注册应用程序以进行身份验证并配置移动服务]: #register
[将表权限限制给已经过身份验证的用户]: #permissions
[向应用程序添加身份验证]: #add-authentication
[在客户端上存储身份验证令牌]: #tokens
[后续步骤]: #next-steps


<!-- URLs. -->
["提交应用"页]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[我的应用程序]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[使用 Live Connect 实现对 Windows Phone 应用程序的单一登录]: /zh-cn/documentation/articles/mobile-services-windows-phone-single-sign-on
[移动服务入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[数据处理入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[身份验证入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[推送通知入门]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
[使用脚本为用户授权]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts
[JavaScript 和 HTML]: /zh-cn/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Azure 管理门户]: https://manage.windowsazure.cn/
[移动服务 .NET 操作方法概念性参考]: /zh-cn/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
[注册 Windows 应用商店应用程序包以进行 Microsoft 身份验证]: /zh-cn/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
