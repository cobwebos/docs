## <a name="test-your-code"></a>测试代码

### <a name="test-with-visual-studio"></a>使用 Visual Studio 进行测试
如果使用的是 Visual Studio，请按 F5 运行项目。 浏览器随即打开到 http://<span></span>localhost:{port} 位置，然后显示“调用 Microsoft Graph API”按钮。

<p/><!-- --> 

### <a name="test-with-python-or-other-web-server"></a>使用 Python 或其他 Web 服务器进行测试
如果使用的不是 Visual Studio，请确保 Web 服务器已启动。 配置服务器侦听基于“index.html”文件位置的 TCP 端口。 对于 Python，请从应用程序文件夹运行命令提示符终端，开始侦听端口：
 
```bash
python -m http.server 8080
```
打开浏览器并键入 http://<span></span>localhost:8080 或 http://<span></span>localhost:{port}，其中 port 为 Web 服务器正在侦听的端口。 随即会显示 index.html 文件的内容和“调用 Microsoft Graph API”按钮。

## <a name="test-your-application"></a>测试应用程序

浏览器载入 index.html 文件后，选择“调用 Microsoft Graph API”。 首次运行应用程序时，浏览器会将你重定向到 Microsoft Azure Active Directory (Azure AD) v2.0 终结点，然后系统会提示你登录：
 
![登录 JavaScript SPA 帐户](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>许可应用程序访问

首次登录到应用程序时，系统会提示你同意允许应用程序访问你的个人资料并登录：

![许可应用程序访问](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>查看应用程序结果
登录后，“Graph API 调用响应”框中应当会显示用户配置文件信息。
 
![Microsoft Graph API 调用的预期结果](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

还应当显示有关在“访问令牌”和“ID 令牌声明”框中获取的令牌的基本信息。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>有关作用域和委派权限的详细信息

Microsoft Graph API 需要 **user.read** 作用域来读取用户的个人资料。 默认情况下，在注册门户上注册的每个应用程序中，都会自动添加此作用域。 Microsoft Graph 的其他 API 以及后端服务器的自定义 API 可能需要其他作用域。 Microsoft Graph API 需要 **Calendars.Read** 作用域来列出用户的日历。

若要在应用程序上下文中访问用户的日历，请将 **Calendars.Read** 委派权限添加到应用程序注册信息。 然后，将“Calendars.Read”作用域添加到“acquireTokenSilent”调用。 

>[!NOTE]
>当你增加作用域数量时，可能会提示用户另外进行许可。

如果后端 API 不需要作用域（不推荐），则可以使用“clientId”作为“acquireTokenSilent”和“acquireTokenRedirect”调用中的作用域。

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
