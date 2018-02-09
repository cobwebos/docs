## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>测试从 iOS 应用程序查询 Microsoft Graph API

要在模拟器中运行代码，请按 Command + R。

![在模拟器中测试应用程序](media/active-directory-develop-guidedsetup-ios-test/iostestscreenshot.png)

准备好测试后，选择“调用 Microsoft Graph API”。 出现提示时，请输入用户名和密码。

### <a name="provide-consent-for-application-access"></a>许可应用程序访问
首次登录到应用程序时，系统会提示你同意允许应用程序访问你的个人资料并登录：

![许可应用程序访问](media/active-directory-develop-guidedsetup-ios-test/iosconsentscreen.png)

### <a name="view-application-results"></a>查看应用程序结果
登录后，“日志记录”部分中应显示 Microsoft Graph API 调用返回的用户配置文件信息。 

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>有关作用域和委派权限的详细信息

Microsoft Graph API 需要 **user.read** 作用域来读取用户的个人资料。 默认情况下，在注册门户上注册的每个应用程序中，都会自动添加此作用域。 Microsoft Graph 的其他 API 以及后端服务器的自定义 API 可能需要其他作用域。 Microsoft Graph API 需要 **Calendars.Read** 作用域来列出用户的日历。

若要在应用程序上下文中访问用户的日历，请将 **Calendars.Read** 委派权限添加到应用程序注册信息。 然后，将“Calendars.Read”作用域添加到“acquireTokenSilent”调用。 

>[!NOTE]
>当你增加作用域数量时，可能会提示用户另外进行许可。

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
