<properties title="在使用 AD FS 进行身份验证的 Azure 网站中创建 ASP.NET MVC 业务线应用程序" pageTitle="使用 AD FS 的 Azure 网站中的 LOB 应用程序" description="了解如何在使用本地 STS 进行身份验证的 Azure 网站中创建 ASP.NET MVC LOB 应用程序。本教程将 AD FS 定位为本地 STS。" metaKeywords="Azure,Azure Websites,line of business,cloud services,enterprise,enterprise  application,adfs,ASP.NET,MVC" services="web-sites" solutions="" documentationCenter=".NET" authors="cephalin" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.devlang="dotnet" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="web" ms.date="12/23/2014" ms.author="cephalin" />

# 在使用 AD FS 进行身份验证的 Azure 网站中创建 ASP.NET MVC 业务线应用程序 #

在本文中，你将了解如何在使用本地 [Active Directory 联合身份验证服务](http://technet.microsoft.com/library/hh831502.aspx)作为标识提供程序的 [Azure 网站](/zh-cn/services/websites/)中创建 ASP.NET MVC 业务线 (LOB) 应用程序。当你想要在 Azure 网站中创建 LOB 应用程序，而你的组织要求在现场存储所有数据时，可以应用此方案。

有关 Azure 网站的不同企业身份验证和授权选项的概述，请参阅[在 Azure 网站中的 LOB 应用程序内对用户进行身份验证和授权](./web-sites-authentication-authorization)。

- [要生成的项目](#bkmk_build)
- [所需的项目](#bkmk_need)
- [使用 LOB 模板的示例应用程序](#bkmk_sample)
- [设置示例应用程序](#bkmk_setup)
- [将示例应用程序部署到 Azure 网站](#bkmk_deploy)
- [在 AD FS 管理中配置信赖方信任](#bkmk_rptrusts)
- [为特定控制器或操作的用户授权](#bkmk_authorize)
- [连接到本地数据](#bkmk_data)
- [其他资源](#bkmk_resources)

<a name="bkmk_build"></a>
## 要生成的项目 ##

你将在 Azure 网站中生成具有以下功能的基本 ASP.NET 应用程序：

- 根据 AD FS 对用户进行身份验证
- 使用`[授权]`为用户执行不同操作授权
- 用于在 Visual Studio 中进行调试和发布到 Azure 网站的静态配置（配置一次，随时调试和发布）  

<a name="bkmk_need"></a>
## 所需的项目 ##

[WACOM.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

若要完成本教程，你需要以下项目：

- 本地 AD FS 部署（有关使用的测试实验室的端到端演练，请参阅[测试实验室：Azure VM 中使用 AD FS 的独立 STS（仅用于测试）](TODO)）
- 用于在 AD FS 管理中创建信赖方信任的权限
- Visual Studio 2013

<a name="bkmk_sample"></a>
## 使用 LOB 模板的示例应用程序 ##

本教程中的示例应用程序 [WebApp-WSFederation DotNet)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) 由 Azure Active Directory 团队创建。由于 AD FS 支持 WS-Federation，因此你可以将它用作模板来轻松创建新的 LOB 应用程序。该示例应用程序具有以下功能：

- 使用 [WS-Federation](http://msdn.microsoft.com/library/bb498017.aspx) 对本地进行 AD FS 部署进行身份验证
- 登录和注销功能
- 使用 [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana)（而不是 Windows Identity Foundation，即 WIF），它代表了 ASP.NET 的未来，与 WIF 相比，它的身份验证和授权设置要简单得多

<a name="bkmk_setup"></a>
## 设置示例应用程序 ##

2.	克隆或下载 [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) 中的示例解决方案到本地目录。

	<div class="wa-note">
		<span class="wa-icon-bulb"></span>
		<p>README.md <a href="https://github.com/AzureADSamples/WebApp-WSFederation-DotNet/blob/master/README.md">中的说明</a> 说明了如何使用 Azure Active Directory 设置应用程序，但在本教程中，你将使用 AD FS 对它进行设置，因此请遵照此处的步骤。</p>
	</div>

3.	打开解决方案，然后在"解决方案资源管理器"中打开 Controllers\AccountController.cs。

	你将看到，代码只是发出身份验证质询以使用 WS-Federation 对用户进行身份验证。所有身份验证都在 App_Start.Auth.cs 中配置。

4.  打开 App_Start.Auth.cs。在 `ConfigureAuth` 方法中，记下行：

        app.UseWsFederationAuthentication(
            new WsFederationAuthenticationOptions
            {
                Wtrealm = realm,
                MetadataAddress = metadata                                      
            });

	在 OWIN 领域中，这实际上配置 WS-Federation 所需的最低要求。这肯定比 WIF 要精简得多，因为 Web.config 中的各个位置都注入了 XML。所需的唯一信息就是信赖方 (RP) 标识符和 AD FS 服务元数据文件的 URL。下面是一个示例：

	-	RP 标识符： `https://contoso.com/MyLOBApp`
	-	元数据地址： `http://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

5.	在 App_Start.Auth.cs 中，根据以下突出显示部分所示更改静态字符串定义：  
	<pre class="prettyprint">
	private static string realm = ConfigurationManager.AppSettings["ida:<mark>RPIdentifier</mark>"];
    <mark><del>private static string aadInstance = ConfigurationManager.AppSettings["ida:AADInstance"];</del></mark>
    <mark><del>private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];</del></mark>
    <mark><del>private static string metadata = string.Format("{0}/{1}/federationmetadata/2007-06/federationmetadata.xml", aadInstance, tenant);</del></mark>
    <mark>private static string metadata = string.Format("https://{0}/federationmetadata/2007-06/federationmetadata.xml", ConfigurationManager.AppSettings["ida:ADFS"]);</mark>

    <mark><del>string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant);</del></mark>
    </pre>

6.	现在，你将在 Web.config 中进行相应更改。打开 Web.config 并根据以下突出显示部分所示修改应用程序设置：  
	<pre class="prettyprint">
	&lt;appSettings&gt;
	  &lt;add key="webpages:Version" value="3.0.0.0" /&gt;
	  &lt;add key="webpages:Enabled" value="false" /&gt;
	  &lt;add key="ClientValidationEnabled" value="true" /&gt;
	  &lt;add key="UnobtrusiveJavaScriptEnabled" value="true" /&gt;
	  <mark><del>&lt;add key="ida:Wtrealm" value="[Enter the App ID URI of WebApp-WSFederation-DotNet https://contoso.onmicrosoft.com/WebApp-WSFederation-DotNet]" /&gt;</del></mark>
	  <mark><del>&lt;add key="ida:AADInstance" value="https://login.chinacloudapi.cn" /&gt;</del></mark>
	  <mark><del>&lt;add key="ida:Tenant" value="[Enter tenant name, e.g. contoso.onmicrosoft.com]" /&gt;</del></mark>
	  <mark>&lt;add key="ida:RPIdentifier" value="[Enter the relying party identifier as configured in AD FS, e.g. https://localhost:44320/]" /&gt;</mark>
	  <mark>&lt;add key="ida:ADFS" value="[Enter the FQDN of AD FS service, e.g. adfs.contoso.com]" /&gt;</mark>

	&lt;/appSettings&gt;
	</pre>

	根据相应的环境填写键值。

7.	生成应用程序，以确保没有任何错误。

就这么简单。现在，便可以配合 AD FS 运行该示例应用程序。稍后，你仍需要在 AD FS 中配置 RP 与此应用程序间的信任关系。

<a name="bkmk_deploy"></a>
## 将示例应用程序部署到 Azure 网站

现在，你需要将应用程序发布到 Azure 网站，同时保留调试环境。请注意，你将要在建立 RP 与 AD FS 之间的信任关系之前发布应用程序，因此身份验证还不起作用。但是，如果现在就执行此操作，则可以获得网站 URL，稍后你也可以使用该 URL 来配置 RP 信任。

1. 右键单击你的项目并选择"发布"。

	![](./media/web-sites-dotnet-lob-application-adfs/01-publish-website.png)

2. 选择"Microsoft Azure 网站"。
3. 如果你尚未登录到 Azure，请单击"登录"，然后使用 Azure 订阅的 Microsoft 帐户登录。
4. 登录后，单击"新建"以创建新网站。
5. 填写所有必填字段。稍后你将要连接到本地数据，因此不需要为此网站创建数据库。

	![](./media/web-sites-dotnet-lob-application-adfs/02-create-website.png)

6. 单击"创建"。创建网站后，将打开"发布 Web"对话框。
7. 在"目标 URL"中，将 **http** 更改为 **https**。将整个 URL 复制到文本编辑器。稍后将要用到它。然后单击"发布"。

	![](./media/web-sites-dotnet-lob-application-adfs/03-destination-url.png)

11. 在 Visual Studio 中，打开项目中的 **Web.Release.config**。在 `<configuration>` 标记中插入以下 XML，并将键值替换为发布网站的 URL。  
	<pre class="prettyprint">
&lt;appSettings&gt;
   &lt;add key="ida:RPIdentifier" value="<mark>[e.g. https://mylobapp.chinacloudsites.cn/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
&lt;/appSettings&gt;</pre>

完成此操作后，将在项目中配置两个 RP 标识符，一个用于 Visual Studio 中的调试环境，另一个用于 Azure 中发布的网站。你将为 AD FS 中两个环境的每一个设置 RP 信任。在调试期间，将使用 Web.config 中的应用程序设置来使**调试**配置适用于 AD FS，发布配置（默认情况下，会发布**版本**配置）后，将上载转换的 Web.config，其中包含 Web.Release.config 中的应用程序设置更改。

如果你想要附加到已发布的网站调试器（即必须上载的已发布的网站中的代码的调试符号），你可以创建的调试配置对于 Azure 调试，但使用的应用程序设置从 Web.Release.config 自己自定义 Web.config 转换（例如 Web.AzureDebug.config) 克隆。这样，你可以跨不同的环境中维护静态配置。

<a name="bkmk_rptrusts"></a>
## 在 AD FS 管理中配置信赖方信任 ##

现在，需要先在 AD FS 管理中配置 RP 信任，然后示例应用程序才能真正使用 AD FS 进行身份验证。你需要设置两个单独的 RP 信任，一个用于调试环境，另一个用于发布的网站。

<div class="wa-note">
	<span class="wa-icon-bulb"></span>
	<p>确保对环境重复以下两个步骤。</p>
</div>

4.	在 AD FS 服务器上，使用对 AD FS 具有管理权限的凭据登录。
5.	打开"AD FS 管理"。右键单击"AD FS\信任关系\信赖方信任"，然后选择"添加信赖方信任"。

	![](./media/web-sites-dotnet-lob-application-adfs/1-add-rptrust.png)

5.	在"选择数据源"页上，选择"手动输入有关信赖方的数据"。 

	![](./media/web-sites-dotnet-lob-application-adfs/2-enter-rp-manually.png)

6.	在"指定显示名称"页上，键入应用程序的显示名称，然后单击"下一步"。
7.	在"选择协议"页上，单击"下一步"。
8.	在"配置证书"页上，单击"下一步"。

	<div class="wa-note">
		<span class="wa-icon-bulb"></span>
		<p>由于你已在使用 HTTPS，因此加密令牌是可选的。如果你确实想要在此页上加密来自 AD FS 的令牌，则还必须在代码中添加令牌解密逻辑。有关详细信息，请参阅 <a href="http://chris.59north.com/post/2014/08/21/Manually-configuring-OWIN-WS-Federation-middleware-and-accepting-encrypted-tokens.aspx">"手动配置 OWIN WS-Federation 中间件和接受加密令牌"</a>。</p>
	</div>
  
5.	在转到下一步之前，需要获得 Visual Studio 项目中的一个信息片段。在项目属性中，记下应用程序的 **SSL URL**。 

	![](./media/web-sites-dotnet-lob-application-adfs/3-ssl-url.png)

6.	返回"AD FS 管理"，在"添加信赖方信任向导"的"配置 URL"页中，选择"启用对 WS-Federation 被动协议的支持"，并键入你在上一步记下的 Visual Studio 项目 SSL URL。然后单击"下一步"。

	![](./media/web-sites-dotnet-lob-application-adfs/4-configure-url.png)

	<div class="wa-note">
		<span class="wa-icon-bulb"></span>
		<p>URL 指定身份验证成功后要将客户端发送到的位置。对于调试环境，它应为 <code>https://localhost:&lt;端口&gt;/</code>。对于发布的网站，它应该是网站 URL。</p>
	</div>

7.	在"配置标识符"页上，检查是否已列出你的项目 SSL URL，然后单击"下一步"。在保留默认设置的情况下一直单击"下一步"，直到向导结束。

	<div class="wa-note">
		<span class="wa-icon-bulb"></span>
		<p>在 Visual Studio 项目的 App_Start.Auth.cs 中，此标识符将在联合身份验证期间与 <code>WsFederationAuthenticationOptions.Wtrealm</code> 的值进行匹配。默认情况下，将添加上一步中的应用程序 URL 作为 RP 标识符。</p>
	</div>

8.	现在，你已在 AD FS 中为项目完成了 RP 应用程序的配置。接下来，你要将此应用程序配置为发送应用程序所需的声明。在向导结束时，会按默认打开"编辑声明规则"对话框，方便你立即启动。至少配置以下声明（括号中为架构）：

	-	名称 (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) - ASP.NET 用来解除冻结 `User.Identity.Name`。
	-	用户主体名称 (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn) - 用于唯一标识组织中的用户。
	-	将成员身份分组为角色 (http://schemas.microsoft.com/ws/2008/06/identity/claims/role) - 可与 `[Authorize(Roles="role1, role2,...")]` 修饰符配合使用来授权控制器/操作。实际上，这可能不是大多数的高性能方法进行角色授权，尤其是在你的 AD 用户定期属于数百个安全组转换为数以百计的 SAML 令牌中的角色声明。另一种方法是在某个特定组发送单个角色声明有条件地根据用户的成员身份。但是，本教程将简化其结构。
	-	名称 ID (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier) - 可用于防伪验证。有关如何使其适用于防伪验证的详细信息，请参阅[在使用 AD FS 进行身份验证的 Azure 网站中创建 ASP.NET MVC 业务线应用程序](../web-sites-dotnet-lob-application-adfs/#bkmk_crud)中的**添加 LOB 功能**部分。

	<div class="wa-note">
		<span class="wa-icon-bulb"></span>
		<p>需要为应用程序配置的声明类型取决于应用程序的需求。有关 Azure Active Directory 应用程序支持的声明列表（即 RP 信任），请参阅 <a href="http://msdn.microsoft.com/library/azure/dn195587.aspx">"支持的令牌和声明类型"</a>。</p>
	</div>

8.	在"编辑声明规则"对话框中，单击"添加规则"。
9.	如下所示配置名称、UPN 和角色声明，然后单击"完成"。

	![](./media/web-sites-dotnet-lob-application-adfs/5-ldap-claims.png)

	接下来，你将使用 [SAML 断言中的名称标识符](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx)中演示的步骤创建一个临时名称 ID 声明。

9.	再次单击"添加规则"。
10.	选择"使用自定义规则发送声明"，然后单击"下一步"。
11.	将以下规则语言粘贴到"自定义规则"框，将规则命名为"按会话标识符"，然后单击"完成"。  
	<pre class="prettyprint">
	c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] &amp;&amp;
	c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant"]
		add(
			store = "_OpaqueIdStore",
			types = ("<mark>http://contoso.com/internal/sessionid</mark>"),
			query = "{0};{1};{2};{3};{4}",
			param = "useEntropy",
			param = c1.Value,
			param = c1.OriginalIssuer,
			param = "",
			param = c2.Value);
	</pre>

	你的自定义规则应如下所示：

	![](./media/web-sites-dotnet-lob-application-adfs/6-per-session-identifier.png)

9.	再次单击"添加规则"。
10.	选择"转换传入声明"并单击"下一步"。
11.	如下所示配置规则（使用在自定义规则中创建的声明类型），然后单击"完成"。

	![](./media/web-sites-dotnet-lob-application-adfs/7-transient-name-id.png)

	有关创建上述临时名称 ID 声明的步骤的详细信息，请参阅 [SAML 断言中的名称标识符](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx)。

12.	在"编辑声明规则"对话框中单击"应用"。现在，它应该类似于以下屏幕截图：

	![](./media/web-sites-dotnet-lob-application-adfs/8-all-claim-rules.png)

	<div class="wa-note">
		<span class="wa-icon-bulb"></span>
		<p>同样，请确保对调试环境和发布的网站重复这些步骤。</p>
	</div>

<a name="bkmk_test"></a>
## 测试应用程序的联合身份验证

现在，你可以针对 AD FS 测试应用程序的身份验证逻辑。在 AD FS 实验室环境中，我有一个属于 Active Directory (AD) 中测试组的测试用户。

![](./media/web-sites-dotnet-lob-application-adfs/10-test-user-and-group.png)

若要在调试器中测试身份验证，只需按 `F5`。如果要在发布的网站中测试身份验证，请导航到 URL。

加载 Web 应用程序之后，请单击"登录"。现在，应该会显示 AD FS 提供的登录对话框或登录页（具体取决于 AD FS 选择的身份验证方法）。下面是 Internet Explorer 11 中显示的元素。

![](./media/web-sites-dotnet-lob-application-adfs/9-test-debugging.png)

使用 AD FS 部署的 AD 域中的用户登录后，应该会在一角再次看到显示**你好，<用户名>！**的主页。以下我看到的元素。

![](./media/web-sites-dotnet-lob-application-adfs/11-test-debugging-success.png)

现在，你已在以下方面取得了成功：

- 你的应用程序已成功访问 AD FS，并且在 AD FS 数据库中找到了匹配的 RP 标识符
- AD FS 已成功对 AD 用户进行身份验证，并将你重定向回到应用程序的主页
- AD FS 已成功向应用程序发送名称声明 (<http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name>)，这可以通过网页一角显示的用户名来判断。 

如果缺少名称声明，你将会看到**你好，！**。查看 Views\Shared\_LoginPartial.cshtml，你将会发现它使用了 `User.Identity.Name` 来显示用户名。如前所述，ASP.NET 将使用经过身份验证的用户的名称声明（如果已在 SAML 令牌中提供）来解冻此属性。若要查看 AD FS 发送的所有声明，请在索引操作方法的 Controllers\HomeController.cs 中放置一个断点。对用户进行身份验证后，请检查 `System.Security.Claims.Current.Claims` 集合。

![](./media/web-sites-dotnet-lob-application-adfs/12-test-debugging-all-claims.png) 

<a name="bkmk_authorize"></a>
## 为特定控制器或操作的用户授权

由于你已在 RP 信任配置中包含组成员资格作为角色声明，因此，现在可以在控制器和操作的 `[Authorize(Roles="...")]` 修饰中直接使用这些声明。在采用创建-读取-更新-删除 (CRUD) 模式的业务线应用程序中，你可以授权特定的角色访问每个操作。目前，你只能对现有的主控制器试用此功能。

1. 打开 Controllers\HomeController.cs。
2. 使用已经身份验证用户具有的安全组成员资格，如下所示修饰 `About` 和 `Contact` 操作方法。  
	<pre class="prettyprint">
    <mark>[Authorize(Roles="Test Group")]</mark>
    public ActionResult About()
    {
        ViewBag.Message = "Your application description page.";

        return View();
    }

    <mark>[Authorize(Roles="Domain Admins")]</mark>
    public ActionResult Contact()
    {
        ViewBag.Message = "Your contact page.";

        return View();
    }
	</pre>

	由于我已在 AD FS 实验室环境中向**测试组**添加了**测试用户**，因此我将使用测试组在 `About` 上测试授权。对于 `Contact`，我将测试**测试用户**不属于的**域管理员**的反面案例。

3. 按 `F5` 启动调试器并登录，然后单击"关于"。如果经过身份验证的用户已获得该操作的授权，则可以成功地显示 `~/About/Index` 页。
4. 现在，请单击"联系人"，在本例中不应授权**测试用户**执行该操作。但是，浏览器将重定向到 AD FS，并最终显示此消息：

	![](./media/web-sites-dotnet-lob-application-adfs/13-authorize-adfs-error.png)

	如果调查事件查看器中的 AD FS 服务器上出现此错误，你将看到此异常消息：  
	<pre class="prettyprint">
	Microsoft.IdentityServer.Web.InvalidRequestException: MSIS7042: <mark>The same client browser session has made '6' requests in the last '11' seconds.</mark> Contact your administrator for details.
	   at Microsoft.IdentityServer.Web.Protocols.PassiveProtocolHandler.UpdateLoopDetectionCookie(WrappedHttpListenerContext context)
	   at Microsoft.IdentityServer.Web.Protocols.WSFederation.WSFederationProtocolHandler.SendSignInResponse(WSFederationContext context, MSISSignInResponse response)
	   at Microsoft.IdentityServer.Web.PassiveProtocolListener.ProcessProtocolRequest(ProtocolContext protocolContext, PassiveProtocolHandler protocolHandler)
	   at Microsoft.IdentityServer.Web.PassiveProtocolListener.OnGetContext(WrappedHttpListenerContext context)
	</pre>

	发生这种情况的原因是默认情况下当用户的角色未授权时 MVC 将返回 401"未授权"。这会触发对标识提供程序 (AD FS) 的重新进行身份验证请求。由于用户已经过身份验证，AD FS 将返回同一页，然后再次发出 401，并创建重定向循环。你将使用简单的逻辑重写 AuthorizeAttribute 的 `HandleUnauthorizedRequest` 方法，以显示一些有意义的内容，而不是继续重定向循环。

5. 在项目中创建名为 AuthorizeAttribute.cs 的文件，并将以下代码粘贴到其中。

		using System;
		using System.Web.Mvc;
		using System.Web.Routing;
		
		namespace WebApp_WSFederation_DotNet
		{
		    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
		    public class AuthorizeAttribute : System.Web.Mvc.AuthorizeAttribute
		    {
		        protected override void HandleUnauthorizedRequest(AuthorizationContext filterContext)
		        {
		            if (filterContext.HttpContext.Request.IsAuthenticated)
		            {
		                filterContext.Result = new System.Web.Mvc.HttpStatusCodeResult((int)System.Net.HttpStatusCode.Forbidden);
		            }
		            else
		            {
		                base.HandleUnauthorizedRequest(filterContext);
		            }
		        }
		    }
		}

	在 authenticated-but-unauthorized 的情况下，重写代码将发送 HTTP 403（禁止）而不是 HTTP 401（未授权）。

6. 再次使用 `F5` 运行调试器。现在，单击"联系人"会显示信息更丰富（但不严重）的错误消息：

	![](./media/web-sites-dotnet-lob-application-adfs/14-unauthorized-forbidden.png)

7. 再次将应用程序发布到 Azure 网站，并测试实时应用程序的行为。

<a name="bkmk_data"></a>
## 连接到本地数据

使用 AD FS 而不是 Azure Active Directory 实施业务线应用程序的一个原因是，既能符合法规要求，同时可将组织数据保留在外部。这可能意味着你的 Azure 网站必须访问本地数据库，因为不允许你使用 [SQL Database](/zh-cn/services/sql-database/) 作为网站的数据层。

Azure 网站支持通过两种方式访问本地数据库：[混合连接](/zh-cn/documentation/articles/integration-hybrid-connection-overview/)和[虚拟网络](/zh-cn/documentation/articles/web-sites-integrate-with-vnet/)。有关详细信息，请参阅[对 Azure 网站使用 VNET 集成和混合连接](http://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)。

<a name="bkmk_resources"></a>
## 其他资源

- [通过 SSL 和 Authorize 属性保护应用程序](../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/#protect-the-application-with-ssl-and-the-authorize-attribute)
- [在 Azure 网站中对 LOB 应用程序内的用户进行身份验证和授权](../web-sites-authentication-authorization/)
- [在使用 Azure Active Directory 进行身份验证的 Azure 网站中创建 ASP.NET MVC 业务线应用程序](../web-sites-dotnet-lob-application-azure-ad/)
- [在 Visual Studio 2013 中将本地组织的身份验证选项 (ADFS) 与 ASP.NET 结合使用](http://www.cloudidentity.com/blog/2014/02/12/use-the-on-premises-organizational-authentication-option-adfs-with-asp-net-in-visual-studio-2013/)
- [Vittorio Bertocci 的博客](http://blogs.msdn.com/b/vbertocci/)
- [将 VS2013 Web 项目从 WIF 迁移到 Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Active Directory 联合身份验证服务概述](http://technet.microsoft.com/library/hh831502.aspx)
- [WS-Federation 1.1 规范](http://download.boulder.ibm.com/ibmdl/pub/software/dw/specs/ws-fed/WS-Federation-V1-1B.pdf?S_TACT=105AGX04&S_CMP=LP)
