<properties
	pageTitle="Azure AD Connect 中的多个域"
	description="本文档介绍如何使用 O365 与 Azure AD 来设置和配置多个顶级域。"
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.date="01/11/2016"
	wacn.date=""/>

#多域支持

许多人都想知道如何使用联合配置多个顶级 Office 365 或 Azure AD 域和子域。
尽管大多数配置都可以通过简单直接的方式完成，但由于有些事情是在幕后进行的，因此你应该了解一些提示与技巧，以避免发生以下问题

- 尝试为联合配置其他域时出现的错误消息
- 在为联合配置多个顶级域后，子域中的用户无法登录

## 多个顶级域
我将引导你完成示例组织 contoso.com 的设置，该组织想要添加名为 fabrikam.com 的附加域。

假设在本地系统中，我已将 AD FS 的联合服务名称配置为 fs.contoso100.com。

当我首次注册 Office 365 或 Azure AD 时，选择将 contoso.com 配置为我的第一个登录域。我可以通过使用 New-MsolFederatedDomain 的 Azure AD Connect 或 Azure AD Powershell 来实现此目的。

完成后，让我们看一下这两个新 Azure AD 域的新配置属性的默认值（可以使用 Get-MsolDomainFederationSettings 查询）：

| 属性名称 | 值 | 说明|
| ----- | ----- | -----|
|IssuerURI | http://fs.contoso100.com/adfs/services/trust|虽然它看起来像是个 URL，但此属性其实只是本地身份验证系统的名称，因此路径不需要解析为任何项目。默认情况下，Azure AD 在本地 AD FS 配置中将其设置为联合身份验证服务标识符的值。
|PassiveClientSignInUrl|https://fs.contoso100.com/adfs/ls/|This是被动登录请求要发送到的位置，它将解析为我的实际 AD FS 系统。实际上有多个“*Url”属性，但我们只需查看演示此属性与 URI 之间差异（例如 IssuerURI）的示例。

现在，假设我添加了第二个域 fabrikam.com。同样，我可以再次运行 Azure AD Connect 向导或通过 PowerShell 完成此操作。

如果尝试使用 Azure AD PowerShell 将第二个域添加为联合域，将会收到错误。

原因是 Azure AD 有一个约束：多个域的 IssuerURI 不能使用相同的值。为了克服此限制，必须针对新域使用不同的 IssuerURI。这就是 SupportMultipleDomain 参数的作用。当配合 cmdlet 来设置联合（New-、Convert- 与 Update-MsolFederatedDomain）时，此参数会导致 Azure AD 根据必须在 Azure AD 租户中保持唯一的域名来设置 IssuerURI，因此它应该是唯一的。声明规则也有变化，但稍后我们再讨论。

因此，在 Powershell 中，如果使用 SupportMultipleDomain 参数添加 fabrikam.com，

    PS C:\>New-MsolFederatedDomain -DomainName fabrikam.com –SupportMultipleDomain

将在 Azure AD 中得到以下配置：

- DomainName: fabrikam.com
- IssuerURI: http://fabrikam.com/adfs/services/trust 
- PassiveClientSignInUrl: https://fs.contoso100.com/adfs/ls/ 

请注意，根据我的域为 IssuerURI 设置一个值后，唯一的终结点 url 值仍然配置为指向我在 fs.contoso100.com 上的联合身份验证服务，就像原始 contoso.com 域一样。因此，所有域仍指向相同的 AD FS 系统。

SupportMultipleDomain 的另一个作用是确保 AD FS 系统在针对 Azure AD 颁发的令牌中包含正确的颁发者值。它通过使用用户 upn 的域部分并将其设置为 issuerURI 中的域，例如 https://{upn，来实现此目的。因此，在 Azure AD 或 Office 365 上进行身份验证期间，将使用用户令牌中的 Issuer 元素来查找 Azure AD 中的域。如果找不到匹配项，身份验证将会失败。

例如，如果用户的 UPN 是 johndoe@fabrikam.com，AD FS 颁发的令牌中的 Issuer 元素将设置为 http://fabrikam.com/adfs/services/trust。这将与 Azure AD 配置匹配，并且身份验证会成功。

以下是实现此逻辑的自定义声明规则：

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));

现在，在我的设置中，我先注册了不包含 supportMultipleDomains 开关但包含默认 IssuerURI 值的 contoso.com。当我添加 fabrikam.com 时，实际上需要确保该 contoso.com 也配置为使用 SupportMultiple 域开关，因为声明规则更新将永远不再发送默认 IssuerURI，且身份验证将因 IssuerURI 不匹配而失败。不用担心，我们将就这一点对你发出警告，然后才让你在不同域上使用 supportMultipleDomain 开关。

若要补救这种情况，我们还需要针对域 contoso.com 更新配置。  
在找出何时需要完成上述操作以及在添加第二个域时如何正确操作方面，Azure AD Connect 向导相当好用。首先，如果你已进入 SupportMultipleDomain 配置，我们不会覆盖配置。

在 PowerShell 中，必须手动提供 SupportMultipleDomain 开关。

有关从单个域过渡到多个域的详细步骤，请参阅以下说明。

完成此操作后，Azure AD 中便有两个域的配置：

- DomainName: contoso.com
- IssuerURI: http://contoso.com/adfs/services/trust 
- PassiveClientSignInUrl: https://fs.contoso100.com/adfs/ls/ 
- DomainName: fabrikam.com
- IssuerURI: http://fabrikam.com/adfs/services/trust 
- PassiveClientSignInUrl: https://fs.contoso100.com/adfs/ls/ 

用户从 contoso.com 联合登录后，fabrikam.com 域现在可正常工作。现在只剩下一个问题：用户在子域中登录。

##子域
假设我将子域 sub.contoso.com 添加 Azure AD。由于 Azure AD 管理域的方式，子域将继承父域（在本例中为 contoso.com）的设置。这意味着 user@sub.contoso.com 的 IssuerURI 需是 http://contoso.com/adfs/services/trust 。但是，以上针对

Azure AD 实施的标准规则将包含颁发者的令牌生成为 http://sub.contoso.com/adfs/services/trust ，这与域的所需值不匹配，身份验证将会失败。
幸运的是，我们对此也有解决措施，但此措施并未内置到我们的工具中。你必须手动为 Microsoft Online 更新 AD FS 信赖方信任。

你必须配置自定义声明规则，使其在构造自定义 Issuer 值时能够从用户的 UPN 后缀中删除任何子域。可以在以下步骤中找到执行此操作的确切步骤。

总而言之，你可以将多个具有不同名称的域以及子域全部联合到同一 AD FS 服务器，只需执行几个额外的步骤，即可确保为所有用户正确设置 Issuer 值。

<!---HONumber=Mooncake_0215_2016-->