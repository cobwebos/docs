---
title: "适用于 SaaS 应用的 Azure 条件性访问 | Microsoft Docs"
description: "使用 Azure AD 中的条件性访问功能可以配置基于应用程序的多重身份验证，以及阻止不在受信任网络中的用户的访问。 "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 4720d168f5ecd3a5e39cdcad812efb52755fb59a
ms.contentlocale: zh-cn
ms.lasthandoff: 12/29/2016


---
# <a name="getting-started-with-azure-active-directory-conditional-access"></a>Azure Active Directory 条件性访问入门
借助用于 [SaaS](https://azure.microsoft.com/overview/what-is-saas/) 应用的 Azure Active Directory 条件性访问和与 Azure AD 连接的应用，可以基于组、位置和应用程序敏感性配置条件性访问。 

使用基于应用程序敏感性的条件性访问，可以设置每个应用程序的多重身份验证 (MFA) 访问规则。 每个应用程序的 MFA 功能能够阻止位于不受信任的网络上的用户的访问权限。 可以将 MFA 规则应用于已分配给应用程序的所有用户，或者仅应用于指定安全组中的用户。  如果用户是从组织网络内部的 IP 地址访问应用程序，则可能不需要进行 MFA。

这些功能可供已购买 Azure Active Directory Premium 许可证的客户使用。

## <a name="scenario-prerequisites"></a>方案先决条件
* Azure Active Directory Premium 许可证
* 联合的或托管的 Azure Active Directory 租户
* 联合租户要求启用多重身份验证。

## <a name="configure-per-application-access-rules"></a>配置基于应用程序的访问规则
本部分介绍如何配置基于应用程序的访问规则。

1. 使用身为 Azure AD 全局管理员的帐户登录到 Azure 经典门户。
2. 在左窗格中选择“Active Directory”。
3. 在“目录”选项卡中选择你的目录。
4. 选择“应用程序”选项卡。
5. 选择要为其配置规则的应用程序。
6. 选择“配置”  选项卡。
7. 向下滚动到访问规则部分。 选择所需的访问规则。
8. 指定要向其应用规则的用户。
9. 选择将“启用”设置为“打开”以启用策略。

## <a name="understanding-access-rules"></a>了解访问规则
本部分详细说明在 Azure 条件性应用程序访问中支持的访问规则。

### <a name="specifying-the-users-the-access-rules-apply-to"></a>指定要向其应用访问规则的用户
默认情况下，该策略将应用于拥有该应用程序访问权限的所有用户。 但是，也可以将该策略的应用范围限制为指定安全组成员的用户。 可以使用“添加组”按钮，从组选择对话框中选择一个或多个要向其应用访问规则的组。 此对话框还可以用于删除所选组。 当选择规则应用于组时，仅会对属于指定安全组之一的用户强制执行访问规则。

也可以通过选择“排除”选项并指定一个或多个安全组，从策略中显式排除安全组。 属于“排除”列表中某个组的成员的用户不受多重身份验证要求的约束，即使这些用户是访问规则要应用到的组的成员。
下面所示的访问规则要求“管理员”组中的所有用户在访问应用程序时使用多重身份验证。

![设置使用 MFA 的条件性访问规则](./media/active-directory-conditional-access-azuread-connected-apps/conditionalaccess-saas-apps.png)

## <a name="conditional-access-rules-with-mfa"></a>使用 MFA 的条件性访问规则
如果使用针对用户的多重身份验证功能对某个用户进行了配置，则针对该用户的这个设置将与应用的多重身份验证规则合并。 这意味着，如果将某个用户配置为进行针对用户的多重身份验证，则该用户必须执行多重身份验证，即使该用户已被排除在应用程序多重身份验证规则之外。 了解有关多重身份验证和每个用户设置的详细信息。

### <a name="access-rule-options"></a>访问规则选项
可以使用以下选项：

* **需要多重身份验证**：要求要向其应用访问规则的用户先完成多重身份验证，然后才能访问策略所应用到的应用程序。
* **不在工作网络时要求多重身份验证**：来自受信任 IP 地址的用户不需要执行多重身份验证。 可以在 Multi-Factor Authentication 设置页上配置受信任的 IP 地址范围。
* **不在工作网络时阻止访问**：阻止不是来自受信任 IP 地址的用户。 可以在 Multi-Factor Authentication 设置页上配置受信任的 IP 地址范围。

### <a name="setting-rule-status"></a>设置规则状态
使用访问规则状态可以启用或禁用规则。 当访问规则处于关闭状态时，不会强制实施多重身份验证要求。

### <a name="access-rule-evaluation"></a>访问规则的评估
当用户访问使用 OAuth 2.0、OpenID Connect、SAML 或 WS-Federation 的联合应用程序时，则会计算访问规则。 此外，当 OAuth 2.0 和 OpenID Connect 使用刷新令牌获取访问令牌时，对访问规则进行了计算。 如果在使用刷新令牌的情况下策略计算失败，则返回错误 **invalid_grant**，这表明用户需要通过客户端重新进行身份验证。

### <a name="configure-federation-services-to-provide-multi-factor-authentication"></a>配置联合身份验证服务以提供多重身份验证
对于联合租户，MFA 可能通过 Azure Active Directory 或本地 AD FS 服务器执行。

默认情况下，MFA 将在由 Azure Active Directory 托管的页上进行。 若要在本地配置 MFA，必须使用适用于 Windows PowerShell 的 Azure AD 模块，在 Azure Active Directory 中将 **–SupportsMFA** 属性设置为 **true**。

以下示例演示如何在 contoso.com 租户上使用 [Set-MsolDomainFederationSettings cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) 启用本地 MFA：

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

除了设置此标志之外，必须配置联合租户 AD FS 实例，以执行 Multi-Factor Authentication。 请遵循[部署本地 Azure 多重身份验证](../multi-factor-authentication/multi-factor-authentication-get-started-server.md)的说明。

## <a name="related-articles"></a>相关文章
* [保护对 Office 365 和其他连接到 Azure Active Directory 的应用的访问](active-directory-conditional-access.md)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)


