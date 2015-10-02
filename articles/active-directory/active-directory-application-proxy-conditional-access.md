<properties
	pageTitle="使用 Azure AD 应用程序代理发布的应用程序的条件性访问"
	description="介绍如何设置使用 Azure AD 应用程序代理发布远程访问的应用程序的条件性访问。"
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.date="08/09/2015"
	wacn.date=""/>

# 使用条件性访问
> [AZURE.NOTE]应用程序代理是一项仅当升级到高级版或基本版的 Azure Active Directory 才可用的功能。有关详细信息，请参阅 [Azure Active Directory 版本](https://msdn.microsoft.com/library/azure/dn532272.aspx)。

现在你可以启用访问规则，以便为访问使用应用程序代理发布的应用程序的用户和组授予条件性访问权限。这使你可以： - 要求每个应用程序进行 Multi-Factor Authentication - 仅当用户不在工作时要求 Multi-Factor Authentication - 阻止用户不在工作时访问应用程序

这些规则可以应用于所有用户和组，或仅应用于特定用户和组。默认情况下，规则将应用于有权访问应用程序的所有用户。但是规则也可以限制为属于指定安全组成员的用户。当用户访问使用 OAuth 2.0、OpenID Connect、SAML 或 WS-Federation 的联合应用程序时，则会计算访问规则。此外，当刷新令牌用于获取访问令牌时，则会在使用 OAuth 2.0 和 OpenID Connect 的情况下计算访问规则。

## 条件性访问先决条件

- Azure Active Directory Premium 订阅 
- 联合或托管的 Azure Active Directory 租户 
- 联合租户需要启用 Multi-Factor Authentication (MFA) 

![](http://i.imgur.com/rv28onQ.png)

## 配置每个应用程序 Multi-Factor Authentication
1. 在 Azure 管理门户中，以管理员身份进行登录。
2. 转到 Active Directory，并选择你要在其中启用应用程序代理的目录。
3. 单击“应用程序”，然后向下滚动到“访问规则”部分。使用联合身份验证的使用应用程序代理发布的应用程序才会显示与访问规则部分。
4. 通过选择“打开”“启用访问规则”来启用规则。
5. 指定向其应用规则的用户和组。使用“添加组”按钮选择访问规则会应用的一个或多个组。此对话框还可以用于删除所选组。当选择规则应用于组时，仅会对属于指定安全组之一的用户强制执行访问规则。<br> 若要从规则中显式排除安全组，请查看“例外”并指定一个或多个组。属于“例外”列表中的组成员的用户不需要执行 Multi-Factor Authentication。<br>如果已使用每个用户 Multi-Factor Authentication 功能配置用户，则此设置会优先于应用程序的 Multi-Factor Authentication 规则。这意味着已配置为每个用户 Multi-Factor Authentication 的用户将需要执行 Multi-Factor Authentication，即使他们已从应用程序的 Multi-Factor Authentication 规则中免除。[了解有关 Multi-Factor Authentication 和每个用户设置的详细信息](/documentation/articles/multi-factor-authentication)。 
6. 选择你想要设置的访问规则：
- **要求 Multi-Factor Authentication**：向其应用访问规则的用户要求完成 Multi-Factor Authentication，然后才能访问规则应用的应用程序。
- **不工作时要求 Multi-Factor Authentication**：尝试从受信任的 IP 地址访问应用程序的用户不必执行 Multi-Factor Authentication。可以在 Multi-Factor Authentication 设置页上配置受信任的 IP 地址范围。
- **不工作时阻止访问**：尝试从公司网络外部访问应用程序的用户不能访问应用程序。


## 为联合服务配置 MFA
对于联合租户，Multi-Factor Authentication (MFA) 可能通过 Azure Active Directory 或本地 AD FS 服务器执行。默认情况下，MFA 会在 Azure Active Directory 托管的任意页上发生。若要配置本地 MFA，请运行 Windows PowerShell 并使用 –SupportsMFA 属性设置 Azure AD 模块。以下示例演示如何在 contoso.com 租户上使用 [Set-MsolDomainFederationSettings cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) 启用本地 MFA：`Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true ` 除了设置此标志之外，必须配置联合租户 AD FS 实例，以执行 Multi-Factor Authentication。按照说明[在本地部署 Microsoft Azure Multi-Factor Authentication](http://technet.microsoft.com/library/dn280946.aspx)。
## 其他资源

* [以组织身份注册 Azure](/documentation/articles/sign-up-organization)
* [Azure 标识](/documentation/articles/fundamentals-identity)

<!---HONumber=71-->