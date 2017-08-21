
## <a name="test-your-code"></a>测试代码

如果使用的是 Visual Studio，请按 `F5` 运行项目。 浏览器将打开 http://localhost:{port} 并将你重定向到该网址，其中你将看到“调用 Microsoft Graph API”按钮。

如果使用的不是 Visual Studio，请确保已启动 Web 服务器，并且已在 Web 服务器中配置了包含 JavaScript Web 应用程序的文件夹。 打开浏览器并键入 http://localhost:{port}/path - 其中 port 对应于 Web 服务器正在侦听的端口，path 是 index.html 的路径。

单击“调用 Microsoft Graph API”按钮。 如果是首次执行此操作，将显示弹出窗口，提示用户登录。
 
![示例屏幕截图](media/active-directory-singlepageapp-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="consent"></a>同意
首次登录应用程序时，将看到如下所示的许可屏幕，请显式接受：

 ![许可屏幕](media/active-directory-singlepageapp-javascriptspa-test/javascriptspaconsent.png)

由于你正在查询 Microsoft Graph API，因此可能会看到另一个许可页面。 这是动态许可导致的 - 应用程序请求的每个作用域都需要一个许可。 本指南生成的示例应用程序已提供 user.read 作用域，因此需要同意此应用程序读取用户的配置文件。

> [!IMPORTANT]
> 目前，由于 msal javascript 的已知问题，请在浏览器（如 Chrome 和 Firefox）中禁用弹出窗口阻止程序，以便动态许可屏幕能够正常工作。 首次使用 `acquireTokenPopup` 调用 Microsoft Graph API 时，需要禁用弹出窗口阻止程序。

### <a name="expected-results"></a>预期结果
将显示 Microsoft Graph API 调用响应返回的用户配置文件信息。
 
 ![结果](media/active-directory-singlepageapp-javascriptspa-test/javascriptsparesults.png)

还将显示有关在“访问令牌”和“ID 令牌声明”框中获取的令牌的基本信息。

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>有关作用域和委派权限的详细信息

Microsoft Graph API 需要 `user.read` 作用域来读取用户的配置文件。 默认情况下，在我们的注册门户上注册的每个应用程序中，都会自动添加此作用域。 Microsoft Graph 的某些其他 API 及后端服务器的自定义 API 可能需要其他作用域。 例如，对于 Microsoft Graph，需要作用域 `Calendars.Read` 才能列出用户日历。 若要在应用程序上下文中访问用户的日历，则需将 `Calendars.Read` 委派权限添加到应用程序注册信息，然后将 `Calendars.Read` 作用域添加到 `acquireTokenSilent` 调用。 增加作用域数量时，用户可能收到其他许可的提示。

如果后端 API 不需要作用域（不推荐），则可以将 `clientId` 用作 `acquireTokenSilent` 和/或 `acquireTokenPopup` 调用中的作用域。

<!--end-collapse-->
