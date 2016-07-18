<properties
	pageTitle="适用于 SaaS 应用的 Azure 条件性访问预览版 | Azure"
	description="使用 Azure AD 中的条件性访问功能可以配置基于应用程序的 Multi-Factor Authentication，以及阻止不在受信任网络中的用户的访问。"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.date="06/15/2016"
	wacn.date=""/>

# SaaS 应用程序的 Azure 条件性访问预览

适用于 SaaS 应用的 Azure 条件性访问功能已发布公共预览版。使用该预览版可以配置基于应用程序的 Multi-Factor Authentication，以及阻止不在受信任网络中的用户的访问。

可以将 Multi-Factor Authentication 规则应用于已分配给该应用程序的所有用户，或者仅应用于指定安全组中的用户。如果用户是从组织网络内部的 IP 地址访问应用程序，则可能不需要进行 Multi-Factor Authentication。
这些功能可供已购买 Azure Active Directory Premium 许可证的客户使用。

## 方案先决条件
* Azure Active Directory Premium 许可证

* 联合的或托管的 Azure Active Directory 租户

* 联合租户要求启用 Multi-Factor Authentication。

## 此预览版中的已知问题
此预览版仅适用于预先集成的联合身份验证 SaaS 应用程序、使用密码单一登录的应用程序、已注册的开发和业务线应用程序，以及 Azure AD 应用程序代理。我们正在为其他应用程序提供支持。

##配置基于应用程序的访问规则

本部分介绍如何配置基于应用程序的访问规则。

1. 使用身为 Azure AD 全局管理员的帐户登录到 Azure 经典门户。
2. 在左窗格中选择“Active Directory”。
3. 在“目录”选项卡中选择你的目录。
4. 选择“应用程序”选项卡。
5. 选择要为其配置规则的应用程序。
6. 选择“配置”选项卡。
7. 向下滚动到访问规则部分。选择所需的访问规则。
8. 指定要向其应用规则的用户。
9. 选择将“启用”设置为“打开”以启用该策略。

##了解访问规则

本部分详细说明在 Azure 条件性应用程序访问预览版中支持的访问规则。
### 指定要向其应用访问规则的用户

默认情况下，该策略将应用于拥有该应用程序访问权限的所有用户。但是，也可以将该策略的应用范围限制为指定安全组成员的用户。可以使用“添加组”按钮，从组选择对话框中选择一个或多个要向其应用访问规则的组。此对话框还可以用于删除所选组。当选择规则应用于组时，仅会对属于指定安全组之一的用户强制执行访问规则。

也可以通过选择“排除”选项并指定一个或多个安全组，从策略中显式排除安全组。属于“排除”列表中某个组的成员的用户将不受 Multi-Factor Authentication 要求的约束，即使这些用户是访问规则要应用到的组的成员。
下面所示的访问规则将要求“管理员”组中的所有用户在访问应用程序时使用 Multi-Factor Authentication。

![设置使用 MFA 的条件性访问规则](./media/active-directory-conditional-access/conditionalaccess-saas-apps.png)

## 使用 MFA 的条件性访问规则
如果使用针对用户的 Multi-Factor Authentication 功能对某个用户进行了配置，则针对该用户的这个设置将与应用的 Multi-Factor Authentication 规则合并。这意味着，如果将某个用户配置为进行针对用户的 Multi-Factor Authentication，则该用户必须执行 Multi-Factor Authentication，即使该用户已被排除在应用程序 Multi-Factor Authentication 规则之外。了解有关 Multi-Factor Authentication 和每个用户设置的详细信息。

### 访问规则选项
当前预览版支持以下选项：

* **要求多重身份验证**：使用此选项时，将要求要向其应用访问规则的用户先完成 Multi-Factor Authentication，然后才能访问策略所应用到的应用程序。

* **未在工作地时要求多重身份验证**：使用此选项时，来自受信任 IP 地址的用户不需要执行 Multi-Factor Authentication。可以在 Multi-Factor Authentication 设置页面上，或是在目录配置选项卡上使用配置公用 IP 地址范围，来配置受信任 IP 地址范围。

* **不使用时阻止访问**：使用此选项时，将阻止不是来自受信任 IP 地址的用户。可以在 Multi-Factor Authentication 设置页上配置受信任的 IP 地址范围。

### 设置规则状态
使用访问规则状态可以启用或禁用规则。当访问规则处于禁用状态时，不会强制实施 Multi-Factor Authentication 要求。

### 访问规则的评估

当用户访问使用 OAuth 2.0、OpenID Connect、SAML 或 WS-Federation 的联合应用程序时，则会计算访问规则。此外，当刷新令牌用于获取访问令牌时，则会在使用 OAuth 2.0 和 OpenID Connect 的情况下计算访问规则。如果在使用刷新令牌的情况下策略评估失败，则会返回错误 invalid\_grant，这表明用户需要通过客户端重新进行身份验证。
配置联合身份验证服务以提供 Multi-Factor Authentication

对于联合租户，Multi-Factor Authentication (MFA) 可能通过 Azure Active Directory 或本地 AD FS 服务器执行。

默认情况下，MFA 将在由 Azure Active Directory 托管的页上进行。若要在本地配置 MFA，必须使用适用于 Windows PowerShell 的 Azure AD 模块，在 Azure Active Directory 中将 –SupportsMFA 属性设置为 **true**。

以下示例演示如何在 contoso.com 租户上使用 [Set-MsolDomainFederationSettings cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) 启用本地 MFA：

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

除了设置此标志之外，必须配置联合租户 AD FS 实例，以执行 Multi-Factor Authentication。请遵遵循部署本地 Azure Multi-Factor Authentication 的说明。

##相关文章

- [保护对 Office 365 和其他连接到 Azure Active Directory 的应用的访问](/documentation/articles/active-directory-conditional-access)
- [有关 Azure Active Directory 中应用程序管理的文章索引](/documentation/articles/active-directory-apps-index)

<!---HONumber=Mooncake_0711_2016-->