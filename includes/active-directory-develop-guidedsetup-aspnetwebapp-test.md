## <a name="test-your-code"></a>测试代码

要在 Visual Studio 中测试应用程序，请按 F5 运行项目。 浏览器随即打开到 http://<span></span>localhost:{port} 位置，然后显示“使用 Microsoft 登录”按钮。 选择该按钮，启动登录过程。

准备好运行测试后，使用 Microsoft Azure Active Directory (Azure AD) 帐户（工作或学校帐户）或个人 Microsoft 帐户（live.com、outlook.com）进行登录<span></span><span></span>。

![Microsoft 登录](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![登录 Microsoft 帐户](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>查看应用程序结果
登录后，用户将重定向到网站主页。 该主页是 Microsoft 应用程序注册门户上应用程序注册信息中指定的 HTTPS URL。 主页包括欢迎消息“Hello \<User>”、注销链接和查看用户声明链接。 用户声明链接浏览到你之前创建的“授权”控制器。

### <a name="browse-to-see-the-users-claims"></a>浏览查看用户声明
要查看用户声明，选择该链接以浏览到控制器视图（仅经过身份验证的用户可访问该视图）。

#### <a name="view-the-claims-results"></a>查看声明结果
浏览到控制器视图后，应当会显示包含用户基本属性的表格：

|属性 |值 |说明 |
|---|---|---|
|**Name** |用户全名 | 用户的名字和姓氏。
|**用户名** |user<span>@domain.com</span> | 用于标识用户的用户名。
|**主题** |主题 |唯一标识 Web 上用户的字符串。|
|**租户 ID** |Guid | 唯一表示用户的 Azure AD 组织的 guid。|

此外，还应当显示包含身份验证请求中所有声明的表格。 有关详细信息，请参阅 [Azure AD ID 令牌中的声明列表](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)。


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>测试对具有 Authorize 属性的方法的访问（可选）
要以匿名用户身份测试对用户声明的 Authorize 控制器的访问，请执行以下步骤：
1. 选择注销用户的链接并完成注销过程。
2. 在浏览器中键入 http://<span></span>localhost:{port}/authenticated，访问受 Authorize 属性保护的控制器。

#### <a name="expected-results-after-access-to-a-protected-controller"></a>访问受保护控制器后的预期结果
系统会提示进行身份验证来使用受保护的控制器视图。

## <a name="additional-information"></a>其他信息

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>保护整个网站
要保护整个网站，请在“Global.asax”文件中，向“Application_Start”方法中的“GlobalFilters”筛选器添加“AuthorizeAttribute”属性：

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-sign-in-access-to-your-application"></a>限制应用程序的登录访问权限
默认情况下，outlook.com、live.com 及其他个人帐户可登录应用程序。 默认情况下，组织中与 Azure AD 集成的工作或学校帐户也可以登录。

为了限制应用程序的用户登录访问权限，提供了以下几个选项。

#### <a name="restrict-access-to-a-single-organization"></a>将访问权限限为单个组织
可以将应用程序的登录访问仅限于单个 Azure AD 组织中的用户帐户：
1. 在“web.config”文件中，更改“租户”参数的值。 将值从“常见”更改为组织的租户名称，例如“contoso.onmicrosoft.com”。
2. 在“OWIN Startup”类中，将 ValidateIssuer 参数设置为“true”。

#### <a name="restrict-access-to-a-list-of-organizations"></a>将访问权限限制为组织列表
可以将登录访问权限仅限于允许组织列表中的 Azure AD 组织中的用户帐户：
1. 在“web.config”文件中，在“OWIN Startup”类中将 ValidateIssuer 参数设置为“true”。
2. 将 ValidIssuers 参数的值设置为允许组织列表。

#### <a name="use-a-custom-method-to-validate-issuers"></a>使用自定义方法来验证颁发者
可通过 IssuerValidator 参数实现自定义方法来验证颁发者。 有关如何使用此参数的详细信息，请阅读 MSDN 上的 [TokenValidationParameters class](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx)（TokenValidationParameters 类）。

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
