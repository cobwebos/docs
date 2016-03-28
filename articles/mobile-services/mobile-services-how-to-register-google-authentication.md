<properties 
	pageTitle="注册以进行 Google 身份验证 | Azure" 
	description="了解如何注册你的应用程序，以便使用 Google 在 Azure 移动服务中进行身份验证。" 
	services="mobile-services" 
	documentationCenter="android" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.date="11/30/2015" 
	wacn.date=""/>

# 注册应用程序以便在移动服务中进行 Google 登录

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../includes/mobile-services-selector-register-identity-provider.md)]

本主题说明如何注册你的应用程序，以便能够使用 Google 在 Azure 移动服务中进行身份验证。

>[AZURE.NOTE] 本教程与 [Azure 移动服务](/services/mobile-services/)有关，该解决方案可帮助你构建任意平台的可缩放移动应用程序。使用移动服务可以轻松地同步数据、对用户进行身份验证和发送推送通知。此页支持[身份验证入门](/documentation/articles/mobile-services-ios-get-started-users)教程，该教程演示如何将用户登录到你的应用程序。 
<br/>如果这是你第一次体验移动服务，请先完成[移动服务入门](/documentation/articles/mobile-services-ios-get-started)教程。

若要完成本主题中的过程，你必须拥有一个包含已验证电子邮件地址的 Google 帐户。若要新建一个 Google 帐户，请转到 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>。

3. 导航到 [Google API](http://go.microsoft.com/fwlink/p/?LinkId=268303) 网站，使用 Google 帐户凭据登录，单击“创建项目”，提供“项目名称”，然后单击“创建”。

4. 在“产品和服务”下拉菜单中单击“API Manager”，然后在“社交 API”下单击“Google+ API”>“启用 API”。

5. 单击“凭据”>“OAuth 许可屏幕”，然后选择你的“电子邮件地址”，输入“产品名称”，并单击“保存”。

6. 在“凭据”选项卡中，单击“添加凭据”>“OAuth 2.0 客户端 ID”，然后选择“Web 应用程序”。


7. 在“授权的 JavaScript 源”中键入移动服务 URL，将“授权的重定向 URI”中生成的 URL 替换为下列 URL 格式之一，然后单击“创建”：
 
	+ **.NET 后端**：`https://<mobile_service>.azure-mobile.net/signin-google`
	+ **JavaScript 后端**：`https://<mobile_service>.azure-mobile.net/login/google` 

	 >[AZURE.NOTE]确保根据移动服务后端的类型使用正确的重定向 URL 路径格式。如果格式不正确，身份验证将不会成功。

8. 在下一个屏幕上，记下客户端 ID 和客户端密钥的值。

    > [AZURE.IMPORTANT] 客户端密钥是一个非常重要的安全凭据。请勿与任何人分享此密钥或在客户端应用程序中分发它。

现在，你可以将移动服务配置为使用 Google 登录名在应用中进行身份验证。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /documentation/articles/mobile-services-windows-store-dotnet-get-started-users/

<!---HONumber=Mooncake_0321_2016-->