<properties
	pageTitle="使用 Azure AD 应用程序代理发布应用 | Microsoft Azure"
	description="介绍如何使用 Azure AD 应用程序代理发布本地应用程序。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.date="06/02/2015"
	wacn.date=""/>


# 使用 Azure AD 应用程序代理发布应用程序

> [AZURE.NOTE] 应用程序代理是一项仅当升级到高级版或基本版的 Azure Active Directory 才可用的功能。有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-editions.md)。

启用 Microsoft Azure Active Directory (AD) 应用程序代理后，你可以发布应用程序供专用网络外部的用户访问。

本文将指导你完成发布要在本地网络上运行的应用程序的步骤，你要为该网络启用从网络外部进行安全远程访问。

> [AZURE.NOTE] 验证连接器正常运行的标准是，你发布的第一个应用程序可以通过专用网络从任何网站进行访问，这是为了确保用户可以通过 Internet 访问该应用程序，然后你才能发布实际的应用程序。


## 使用向导发布应用

1. 打开你选择的浏览器并转到 Azure 经典门户。
2. 在 Azure 经典门户的左窗格中，单击“Active Directory”选项卡。
3. 单击你已在其中启用应用程序代理并要为其发布应用程序的目录（例如，Wingtip Toys）。
4. 单击“应用程序”选项卡，然后单击屏幕底部的“添加”按钮

	![添加应用程序](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

5. 在“要执行什么操作?”对话框中，单击“发布可在网络外部访问的应用程序”。

	![可从网络外部访问的新应用程序](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

6. 按照屏幕上的说明提供有关应用程序的以下信息：

| **属性** | **详细信息** |
|---|---|
| 外部 URL | 这是用于从专用网络外部访问应用程序的云服务的 URL。该 URL 将基于你提供的名称使用后缀 msappproxy.net 自动生成。 |
| 预身份验证方法 | 设置你希望应用程序使用的预身份验证方法的类型：<br><br> a.Azure Active Directory — 当用户尝试访问应用程序时，应用程序代理将重定向用户以登录要对用户进行身份验证的 Azure AD，从而确保用户拥有目录和应用程序所需的权限。<br><br> b.传递 — 不执行预身份验证。 |
| 外部 URL 协议 | 默认情况下，使用 HTTPS 协议发布应用程序。该服务会自动重定向键入包含 http 的 URL 的用户。<br><br> 若要为内部应用程序启用 HTTP，必须将预身份验证方法设置为“传递”，然后便可以将外部 URL 协议从“HTTPS”更改为“HTTP”。请注意，使用 HTTP 发布应用程序可能会为你的应用程序和用户带来安全问题。<br><br> 你可以插入自定义域，而不是使用默认后缀 msappproxy.net。有关详细信息，请参阅[使用自定义](active-directory-application-proxy-custom-domains.md)。 |
| 内部 URL | 这是应用程序代理连接器用于内部访问应用程序的内部 URL。这应该是从专用网络内部访问发布的应用程序时应该使用的应用程序 URL。这是不带空格或符号的有效 URL。<br><br> 你可以指定后端服务器上要发布的特定路径，而服务器的其余部分则不发布。例如，这样你就可以发布位于相同的 SharePoint 服务器，但具有不同的名称和访问规则的不同网站。<br><br> 路径在内部 URL 字段中指定，而且可在外部 URL 中看到。内部路径和外部路径必须相同。 |

  ![应用程序属性](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)

  若要完成向导，请单击屏幕底部的复选标记。现已在 Azure AD 中定义应用程序。


## 将用户和组分配到应用程序

1. 对于已经过预身份验证的应用程序，你必须分配有权访问该应用程序的用户和组。对于传递应用，所有用户都有访问权限。但是，若要使某个用户能够在其应用程序列表中看到该应用，你必须为该用户分配该应用。

2. 完成“添加应用程序”向导后，将显示“应用程序代理快速启动”页。若要分配用户，请单击“分配用户”。

	![应用程序代理快速启动屏幕截图 — 分配用户](./media/active-directory-application-proxy-publish/quickstart.png)

3. 选择要分配到此应用的每个用户或组，然后单击“分配”。

> [AZURE.NOTE] 对于集成 Windows 身份验证应用程序，只能分配已从本地 Active Directory 同步的用户和组。对于使用 Azure Active Directory 应用程序代理发布的应用程序，不能分配使用 Microsoft 帐户登录的用户以及来宾。确保你要分配的用户使用属于你要发布的应用程序所在的域的一部分的凭据登录。


## 高级配置

1. 可以从“配置”页修改已发布的应用或配置高级选项，例如，向本地应用程序进行 SSO。

	![高级配置](./media/active-directory-application-proxy-publish/advancedconfig.png)

2. 选择应用，然后单击“配置”。可以使用以下选项：

**设置** | **详细信息**
---|---
名称 | 为应用程序提供一个描述性的名称
外部 URL | 这是用于从专用网络外部访问应用程序的云服务的 URL。该 URL 将基于你提供的名称使用后缀 msappproxy.net 自动生成。
预身份验证方法 | 设置你希望应用程序使用的预身份验证方法的类型：<br><br> a.Azure Active Directory — 当用户尝试访问应用程序时，应用程序代理将重定向用户以登录要对用户进行身份验证的 Azure AD，从而确保用户拥有目录和应用程序所需的权限。<br><br> b.传递 — 不执行预身份验证。
外部 URL 协议 | 默认情况下，使用 HTTPS 协议发布应用程序。该服务会自动重定向键入包含 http 的 URL 的用户。<br><br> 若要为内部应用程序启用 HTTP，必须将预身份验证方法设置为“传递”，然后便可以将外部 URL 协议从“HTTPS”更改为“HTTP”。请注意，使用 HTTP 发布应用程序可能会为你的应用程序和用户带来安全问题。<br><br> 你可以插入自定义域，而不是使用默认后缀 msappproxy.net。有关详细信息，请参阅[使用自定义](active-directory-application-proxy-custom-domains.md)。
内部 URL | 这是应用程序代理连接器用于内部访问应用程序的内部 URL。这应该是从专用网络内部访问发布的应用程序时应该使用的应用程序 URL。这是不带空格或符号的有效 URL。<br><br> 你可以指定后端服务器上要发布的特定路径，而服务器的其余部分则不发布。例如，这样你就可以发布位于相同的 SharePoint 服务器，但具有不同的名称和访问规则的不同网站。<br><br> 路径在内部 URL 字段中指定，而且可在外部 URL 中看到。内部路径和外部路径必须相同。
转换标头中的 URL | 针对不需要转换 HTTP 主机标头的应用程序（例如某些 SharePoint 配置），请将此项设置为“否”。这会同时为请求和响应标头禁用标头转换。
内部身份验证方法 | 如果你使用应用程序代理进行预身份验证，则可以设置内部身份验证方法，以使用户能够使用此应用程序的单一登录 (SSO)。<br><br> 如果应用程序使用 IWA 并且你可以将 Kerberos 约束委派 (KCD) 配置为针对此应用程序启用 SSO，请选择“集成的 Windows 身份验证(IWA)”。必须通过 KCD 配置使用 IWA 的应用程序，否则，应用程序代理将无法发布这些应用程序。<br><br> 如果应用程序不使用 IWA，请选择“无”。<br><br> 有关详细信息，请参阅[使用应用程序代理进行单一登录](active-directory-application-proxy-sso-using-kcd.md)。
内部应用程序 SPN | 这是在本地应用程序代理中配置的内部应用程序服务主体名称 (SPN)。应用程序代理连接器使用 SPN 提取使用 Kerberos 约束委派 (KCD) 的应用程序的 Kerberos 令牌。<br><br> 有关详细信息，请参阅[启用单一登录](active-directory-application-proxy-sso-using-kcd.md)。

使用 Azure Active Directory 应用程序代理发布应用程序后，这些应用程序将出现在 Azure AD 的“应用程序”列表中，你可以在该处管理这些应用程序。

如果在发布应用程序后禁用应用程序代理服务，则不会删除应用程序，但再也不能从专用网络外部访问应用程序。

若要查看应用程序并确保它可供访问，请双击应用程序的名称。如果“应用程序代理”服务已禁用并且该应用程序不可用，则会在屏幕顶部显示一条警告消息。

若要删除某个应用程序，请在列表中选择该应用程序，然后单击“删除”。

## 另请参阅
应用程序代理还有其他用途：

- [启用应用程序代理](active-directory-application-proxy-enable.md)
- [使用你自己的域名发布应用程序](active-directory-application-proxy-custom-domains.md)
- [启用单一登录](active-directory-application-proxy-sso-using-kcd.md)
- [启用条件性访问](active-directory-application-proxy-conditional-access.md)
- [使用声明感知应用程序](active-directory-application-proxy-claims-aware-apps.md)
- [解决使用应用程序代理时遇到的问题](active-directory-application-proxy-troubleshoot.md)

## 了解有关应用程序代理的详细信息
- [查看我们的联机帮助](active-directory-application-proxy-enable.md)
- [参阅应用程序代理博客](http://blogs.technet.com/b/applicationproxyblog/)
- [观看第 9 频道上的视频！](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 其他资源
- [有关 Azure Active Directory 中应用程序管理的文章索引](/documentation/articles/active-directory-apps-index)
- [关于 Kerberos 约束委派](http://technet.microsoft.com/library/cc995228.aspx)

<!---HONumber=Mooncake_0509_2016-->