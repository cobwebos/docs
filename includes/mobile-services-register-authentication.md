
1. 单击“Azure 门户”>“移动服务”> 你的移动服务 >“仪表板”，并记下“移动服务 URL”值。

2. 使用一个或多个以下身份验证提供者注册你的应用程序：
   * [Google](mobile-services-how-to-register-google-authentication.md)
   * [Facebook](mobile-services-how-to-register-facebook-authentication.md)
   * [Twitter](mobile-services-how-to-register-twitter-authentication.md)
   * [Microsoft](mobile-services-how-to-register-microsoft-authentication.md)
   * [Azure Active Directory](mobile-services-how-to-register-active-directory-authentication.md)。 
   
   记下标识提供者生成的客户端标识和客户端机密值。不要分发或共享客户端机密。

3. 在 Azure 门户中，单击“移动服务”> 你的移动服务 >“标识”> 你的标识提供者设置，然后输入你的提供者的客户端 ID 和密钥值。 
 
现在，你已将应用和移动服务配置为使用你的身份验证提供者。可以选择对你想要支持的其他每个标识提供者重复所有这些步骤。

    > [AZURE.IMPORTANT] Verify that you've set the correct redirect URI on your identity provider's developer site. As described in the linked instructions for each provider above, the redirect URI may be different for a .NET backend service vs. for a JavaScript backend service. An incorrectly configured redirect URI may result in the login screen not being displayed properly and the app malfunctioning in unexpected ways.

<!---HONumber=74-->