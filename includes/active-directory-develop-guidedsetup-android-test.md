## <a name="test-your-code"></a>测试代码

1. 将代码部署到设备/模拟器。

2. 准备好测试你的应用程序后，使用 Azure Active Directory 帐户（工作或学校帐户）或 Microsoft 帐户（live.com、outlook.com）进行登录。 

    ![测试应用程序](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![输入用户名和密码](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="provide-consent-for-application-access"></a>许可应用程序访问
首次登录到应用程序时，还会提示你同意允许应用程序访问你的个人资料并使你登录，如下所示： 

![许可应用程序访问](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="view-application-results"></a>查看应用程序结果
在登录后，应当会看到由 Microsoft Graph API 调用返回的结果。 调用 Microsoft Graph API **me** 终结点将返回[用户个人资料](https://graph.microsoft.com/v1.0/me)。 有关常用 Microsoft Graph 终结点的列表，请参阅 [Microsoft Graph API 开发人员文档](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries)。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>有关作用域和委派权限的详细信息

Microsoft Graph API 需要 *user.read* 作用域来读取用户的个人资料。 默认情况下，在应用程序注册门户中注册的每个应用程序中，都会自动添加此作用域。 Microsoft Graph 的其他 API 以及后端服务器的自定义 API 可能需要其他作用域。 Microsoft Graph API 需要 *Calendars.Read* 作用域来列出用户的日历。 

若要在应用程序上下文中访问用户的日历，请将 *Calendars.Read* 委派权限添加到应用程序注册信息。 然后，将 *Calendars.Read* 作用域添加到 `acquireTokenSilent` 调用。 

>[!NOTE]
>当你增加作用域数量时，可能会提示用户另外进行许可。

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
