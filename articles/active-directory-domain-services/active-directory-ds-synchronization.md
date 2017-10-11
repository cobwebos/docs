---
title: "Azure Active Directory 域服务： 在托管的域中同步 |Microsoft 文档"
description: "了解 Azure Active Directory 域服务托管域中的同步"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 0c9a9a56e1489ee91fcc332beeef36cdc9c93dc1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD 域服务托管域中的同步
下图说明了如何同步的工作原理在 Azure AD 域服务中托管的域。

![在 Azure AD 域服务中的同步拓扑](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>从你的本地目录同步到 Azure AD 租户
Azure AD Connect 同步可用于同步的用户帐户、 组成员身份，并对你的 Azure AD 租户凭据进行哈希处理。 用户的属性的帐户如 UPN 和本地同步 SID （安全标识符）。 如果你使用 Azure AD 域服务，NTLM 和 Kerberos 身份验证所需的旧凭据哈希也被同步到 Azure AD 租户。

如果你配置回写，发生在你的 Azure AD 目录中的更改将同步回本地 Active Directory。 例如，如果你更改你使用 Azure AD 的自助服务密码更改功能的密码，则该更改的密码更新在本地 AD 域。

> [!NOTE]
> 始终使用最新版本的 Azure AD Connect 以确保你具有的所有已知的 bug 修补程序。
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>从你的 Azure AD 租户同步到托管域
用户帐户、 组成员身份和凭据哈希从你的 Azure AD 租户同步到你的 Azure AD 域服务托管域。 此同步过程是自动的。 不需要配置、 监视或管理此同步过程。 你的目录的一次性初始同步完成后，通常要花费大约 20 分钟才能使更改在 Azure AD 中会反映在你托管的域。 此同步间隔适用于密码更改，或更改为在 Azure AD 中所做的属性。

同步过程也是一次性的单向/单向本质。 托管的域是很大程度上只读的你创建的任何自定义 Ou 除外。 因此，你无法更改用户属性、 用户密码或托管的域中的组成员身份。 因此，没有反向同步的更改从您的托管域回你的 Azure AD 租户。

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>从多林同步本地环境
许多组织具有相当复杂的在本地标识基础结构包含多个帐户林。 Azure AD Connect 支持同步的用户、 组和凭据的哈希从多林环境与 Azure AD 租户。

与此相反，你的 Azure AD 租户是一个多更简单且平面命名空间。 若要使用户能够可靠地访问由 Azure AD 保护的应用程序，解决跨不同林中的用户帐户的 UPN 冲突。 你的 Azure AD 域服务托管的域熊关闭与你的 Azure AD 租户。 因此，你可以在你托管的域中看到的平面 OU 结构中。 所有用户和组都存储在 AADDC 用户容器，而不考虑从该情况下，它们已同步的本地域或林。 你可能已配置分层 OU 结构在本地。 但是，你托管的域仍有一个简单的平面 OU 结构。

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>排除项的内容未同步到你的托管域
以下对象或属性不是同步到 Azure AD 租户或托管域：

* **排除特性：**你可以选择从你使用 Azure AD Connect 的本地域同步到 Azure AD 租户中排除某些属性。 在托管域中，这些排除的属性将不可用。
* **组策略：**你本地的域中配置的组策略不同步到你的托管域。
* **SYSVOL 共享：**同样，你在本地域上的 SYSVOL 共享的内容不同步到你的托管域。
* **计算机对象：**计算机对象的计算机加入到你的本地域不同步到你的托管域。 这些计算机不具有与托管域信任关系并属于仅你的本地域。 在托管的域中，您发现计算机对象，仅对于你具有显式域-加入到托管域的计算机。
* **用户和组的 sid 历史记录属性：**主要用户和主要组你本地的域中的 Sid 同步到托管域。 但是，用户和组的现有 SidHistory 属性不同步从本地域到你的托管域。
* **组织单位 (OU) 结构：**在你的本地域中定义的组织单位不会同步到你的托管域。 在托管域中有两个内置的 Ou。 默认情况下，你托管的域具有平面的 OU 结构。 但是，您可能选择到[在托管域中创建自定义 OU](active-directory-ds-admin-guide-create-ou.md)。

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>如何特定属性同步到托管域
下表列出了一些常见的属性，并描述到你托管的域中的同步方式。

| 属性在托管域 | 源 | 注意 |
|:--- |:--- |:--- |
| UPN |在你的 Azure AD 租户中的用户的 UPN 属性 |从你的 Azure AD 租户的 UPN 属性将同步到你的托管域所原样。 因此，若要登录到你的托管域的最可靠方法使用你的 UPN。 |
| sAMAccountName |用户的 mailNickname 属性中你的 Azure AD 租户或自动生成 |SAMAccountName 属性源自 Azure AD 租户中的 mailNickname 属性。 如果多个用户帐户具有相同的 mailNickname 属性，采用 SAMAccountName 是自动生成的。 如果用户的 mailNickname 或 UPN 前缀的长度超过 20 个字符，采用 SAMAccountName 是自动生成，以满足 SAMAccountName 属性 20 字符限制。 |
| 密码 |从你的 Azure AD 租户的用户的密码 |从你的 Azure AD 租户同步 （也称为补充凭据） 的 NTLM 或 Kerberos 身份验证所需的凭据哈希。 如果你的 Azure AD 租户是已同步的租户，这些凭据被来源于你在本地域。 |
| 主要用户/组 SID |自动生成 |用户/组帐户的主 SID 在托管域中是自动生成。 此属性与主用户/组中本地对象的 SID 不匹配 AD 域。 由于托管的域本地域不同具有不同的 SID 命名空间，这种不匹配。 |
| 用户和组的 SID 历史记录 |在本地的主要用户和组 SID |托管域中的用户和组的 SidHistory 特性设置为与相应的主要用户或组在本地域中的 SID 匹配。 此功能可帮助简化提起移动到托管域的本地应用程序，因为你不需要重新 ACL 资源。 |

> [!NOTE]
> **登录到托管域使用的 UPN 格式：** SAMAccountName 属性可能会自动生成的托管域一些用户帐户。 如果用户具有很长的 UPN 前缀或多个用户具有相同的 mailNickname 属性，这些用户 SAMAccountName 可能自动生成。 因此，SAMAccountName 格式 (例如，CONTOSO100\joeuser) 并不总是可靠的方式登录到域。 用户的自动生成 SAMAccountName 可能不同于其 UPN 前缀。 使用的 UPN 格式 (例如，joeuser@contoso100.com) 以可靠地登录到托管域。
>
>

### <a name="attribute-mapping-for-user-accounts"></a>用户帐户的属性映射
下表说明了你的 Azure AD 租户中的对象同步到你托管的域中的相应属性的用户的特定属性。

| 你的 Azure AD 租户中的用户属性 | 托管域中的用户属性 |
|:--- |:--- |
| accountEnabled |userAccountControl （设置或清除位 ACCOUNT_DISABLED） |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |标题 |
| mail |mail |
| mailNickname |msDS AzureADMailNickname |
| mailNickname |SAMAccountName （有时可能自动生成） |
| mobile |mobile |
| objectid |msDS AzureADObjectId |
| onPremiseSecurityIdentifier |sid 历史记录 |
| passwordPolicies |userAccountControl （设置或清除位 DONT_EXPIRE_PASSWORD） |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>组的属性映射
下表说明了如何特定属性为你的 Azure AD 租户中的对象同步到你托管的域中的相应属性的组。

| 你的 Azure AD 租户中的组属性 | 在托管域的组属性 |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName （有时可能自动生成） |
| mail |mail |
| mailNickname |msDS AzureADMailNickname |
| objectid |msDS AzureADObjectId |
| onPremiseSecurityIdentifier |sid 历史记录 |
| securityEnabled |group类型 |

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>从托管域未同步到 Azure AD 租户的对象
如本文前面部分所述，没有返回到你的 Azure AD 租户你托管的域中的同步。 你可以选择[创建自定义组织单位 (OU)](active-directory-ds-admin-guide-create-ou.md)你托管的域中。 此外，你可以创建其他 Ou、 用户、 组或这些自定义的 Ou 中的服务帐户。 在自定义 Ou 内创建的对象的任何同步回你的 Azure AD 租户。 这些对象是可用于仅在托管域内使用。 因此，这些对象不是使用 Azure AD PowerShell cmdlet，Azure AD Graph API 或使用 Azure AD 管理 UI 可见的。

## <a name="related-content"></a>相关的内容
* [功能-Azure AD 域服务](active-directory-ds-features.md)
* [部署方案的 Azure AD 域服务](active-directory-ds-scenarios.md)
* [Azure AD 域服务的网络注意事项](active-directory-ds-networking.md)
* [要开始使用 Azure AD 域服务](active-directory-ds-getting-started.md)
