<properties 
	pageTitle="注册以进行 Twitter 身份验证 | Azure" 
	description="了解如何在 Azure 移动服务应用程序中使用 Twitter 身份验证。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.date="02/07/2016" 
	wacn.date=""/>

#注册应用以便在移动服务中进行 Twitter 登录

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../includes/mobile-services-selector-register-identity-provider.md)]

本主题说明如何注册你的应用程序，以便能够使用 Twitter 在 Azure 移动服务中进行身份验证。

>[AZURE.NOTE] 本教程与 [Azure 移动服务](/services/mobile-services/)有关，该解决方案可帮助你构建任意平台的可缩放移动应用程序。使用移动服务可以轻松地同步数据、对用户进行身份验证和发送推送通知。此页支持[向应用添加身份验证](/documentation/articles/mobile-services-ios-get-started-users)教程，该教程演示如何将用户登录到你的应用。如果这是你第一次体验移动服务，请先完成[移动服务入门](/documentation/articles/mobile-services-ios-get-started)教程。

若要完成本主题中的过程，你必须拥有一个包含已验证电子邮件地址的 Twitter 帐户。若要创建新的 Twitter 帐户，请转至 <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>。

1. 导航到 [Twitter 开发人员](http://go.microsoft.com/fwlink/p/?LinkId=268300)网站，使用你的 Twitter 帐户凭据登录，然后单击“新建应用”。

2. 为应用键入“名称”、“说明”和“网站”值，然后在“回叫 URL”中键入下列 URL 格式之一。
 
	+ **.NET 后端**：`https://<mobile_service>.azure-mobile.net/signin-twitter`
	+ **JavaScript 后端**：`https://<mobile_service>.azure-mobile.net/login/twitter` 

	 >[AZURE.NOTE]确保根据移动服务后端的类型使用正确的重定向 URL 路径格式。如果格式不正确，身份验证将不会成功。 
	&nbsp;

   	![][2]

3.  在页面的底部，阅读并接受条款，然后单击“创建 Twitter 应用程序”。

   	随即将会注册应用程序并显示应用程序详细信息。

6. 在应用仪表板中单击“密钥和访问令牌”选项卡，并记下“使用者密钥”和“使用者机密”的值。

    > [AZURE.NOTE] 使用者机密是一个非常重要的安全凭据。请勿与任何人分享此密钥或将密钥随应用分发。

7. 单击“设置”选项卡，向下滚动并确保选中“允许使用此应用程序进行 Twitter 登录”复选框，然后单击“更新设置”。

现在，你可以通过向移动服务提供使用者密钥和使用者机密值，使用 Twitter 登录在应用程序中进行身份验证。

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-users/
 
<!---HONumber=Mooncake_0321_2016-->