---
title: "Azure Active Directory 域服务：托管域中的同步 | Microsoft 文档"
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
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD 域服务托管域中的同步
下图演示了 Azure AD 域服务托管域中的同步工作原理。

![Azure AD 域服务中的同步拓扑](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>从本地目录同步到 Azure AD 租户
Azure AD Connect 同步用于将用户帐户、组成员身份和凭据哈希同步到 Azure AD 租户。 用户帐户的属性，例如 UPN 和本地 SID（安全标识符），会同步。 如果使用 Azure AD 域服务，则 NTLM 和 Kerberos 身份验证所需的旧凭据哈希也会同步到 Azure AD 租户。

如果配置了写回，则 Azure AD 目录中发生的更改将同步回到本地 Active Directory。 例如，如果使用 Azure AD 的自助密码更改功能更改了密码，则更改的密码会在本地 AD 域中更新。

> [!NOTE]
> 请始终使用最新版本的 Azure AD Connect，确保获得所有已知 Bug 的修复程序。
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>从 Azure AD 租户同步到托管域
用户帐户、组成员身份和凭据哈希将从 Azure AD 租户同步到 Azure AD 域服务托管域。 此同步过程是自动的。 不需要配置、监视或管理此同步过程。 目录的一次性初始同步完成后，Azure AD 中的更改通常需要约 20 分钟才会在托管域中反映。 此同步时间间隔适用于密码更改或者 Azure AD 中的属性更改。

此外，同步过程在性质上是单向的。 托管域基本上是只读的，但创建的任何自定义 OU 除外。 因此，无法对托管域中的用户属性、用户密码或组成员身份进行更改。 无法将更改从托管域反向同步到 Azure AD 租户。

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>从多林本地环境同步
许多组织使用相当复杂的本地标识基础结构，其中包含多个帐户林。 Azure AD Connect 支持将用户、组和凭据哈希从多林环境同步到 Azure AD 租户。

相比之下，Azure AD 租户是一个简单得多的扁平命名空间。 为了使用户能够可靠地访问 Azure AD 保护的应用程序，需要解决不同林中用户帐户的 UPN 冲突。 Azure AD 域服务托管域与 Azure AD 租户非常类似。 因此，在托管域中会看到一个扁平的 OU 结构。 所有用户和组都存储在“AADDC 用户”容器中，不管它们是从哪个本地域或林同步的。 可能在本地配置了分层 OU 结构。 但是，托管域仍采用简单的扁平 OU 结构。

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>排除项 - 哪些内容不会同步到托管域
以下对象或属性不会同步到 Azure AD 租户或托管域：

* **排除的属性：**可以选择排除使用 Azure AD Connect 将某些属性从本地域同步到 Azure AD 租户。 这些排除的属性将不会出现在托管域中。
* **组策略：**本地域中配置的组策略不会同步到托管域。
* **SYSVOL 共享：**同样，本地域中 SYSVOL 共享的内容不会同步到托管域。
* **计算机对象：**已加入本地域的计算机的计算机对象不会同步到托管域。 这些计算机未与托管域建立信任关系，它们仅属于本地域。 在托管域中，只能看到已显式加入该托管域的计算机的计算机对象。
* **用户和组的 SidHistory 属性：**本地域中的主用户和主组 SID 将同步到托管域。 但是，用户和组的现有 SidHistory 属性不会从本地域同步到托管域。
* **组织单位 (OU) 结构：**在本地域中定义的组织单位不会同步到托管域。 托管域中有两个内置的 OU。 默认情况下，托管域采用扁平的 OU 结构。 但是，可以选择[在托管域中创建自定义 OU](active-directory-ds-admin-guide-create-ou.md)。

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>特定的属性如何同步到托管域
下表列出一些常用属性，并说明它们如何同步到托管域。

| 托管域中的属性 | 源 | 说明 |
|:--- |:--- |:--- |
| UPN |用户在 Azure AD 租户中的 UPN 属性 |Azure AD 租户中的 UPN 属性将按原样同步到托管域。 因此，登录到托管域的最可靠方法是使用 UPN。 |
| SAMAccountName |用户在 Azure AD 租户中的或自动生成的 mailNickname 属性 |SAMAccountName 属性源自 Azure AD 租户中的 mailNickname 属性。 如果多个用户帐户具有相同的 mailNickname 属性，会自动生成 SAMAccountName。 如果用户的 mailNickname 或 UPN 前缀长度超过 20 个字符，会自动生成 SAMAccountName，以满足 SAMAccountName 属性不超过 20 个字符的限制。 |
| 密码 |用户在 Azure AD 租户中的密码 |NTLM 或 Kerberos 身份验证所需的凭据哈希（也称为补充凭据）将从 Azure AD 租户同步。 如果 Azure AD 租户是已同步的租户，这些凭据源自本地域。 |
| 主用户/组 SID |自动生成 |用户/组帐户的主 SID 在托管域中自动生成。 此属性与本地 AD 域中对象的主用户/组 SID 不匹配。 之所以不匹配，是因为托管域的 SID 命名空间不同于本地域。 |
| 用户和组的 SID 历史记录 |本地主用户和组 SID |托管域中用户和组的 SidHistory 属性已设置为与本地域中相应的主用户或组 SID 相匹配。 借助此功能可以更方便地将本地应用程序即时转移到托管域，因为不需要重新将资源加入 ACL。 |

> [!NOTE]
> **使用 UPN 格式登录到托管域：**可以针对托管域中的某些用户帐户自动生成 SAMAccountName 属性。 如果多个用户具有相同的 mailNickname 属性或者用户具有很长的 UPN 前缀，可能会自动生成这些用户的 SAMAccountName。 因此，使用 SAMAccountName 格式（例如“CONTOSO100\joeuser”）不一定能够可靠地登录到域。 为用户自动生成的 SAMAccountName 可能不同于其 UPN 前缀。 请使用 UPN 格式（例如“joeuser@contoso100.com”）可靠地登录到托管域。
>
>

### <a name="attribute-mapping-for-user-accounts"></a>用户帐户的属性映射
下表演示了 Azure AD 租户中用户对象的特定属性如何同步到托管域中的相应属性。

| Azure AD 租户中的用户属性 | 托管域中的用户属性 |
|:--- |:--- |
| accountEnabled |userAccountControl（设置或清除 ACCOUNT_DISABLED 位） |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |title |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName（有时可能会自动生成） |
| mobile |mobile |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl（设置或清除 DONT_EXPIRE_PASSWORD 位） |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |号 |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>组的属性映射
下表演示了 Azure AD 租户中组对象的特定属性如何同步到托管域中的相应属性。

| Azure AD 租户中的组属性 | 托管域中的组属性 |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName（有时可能会自动生成） |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>不会从托管域同步到 Azure AD 租户的对象
如本文前面部分中所述，不存在从托管域到 Azure AD 租户的反向同步。 可以选择在托管域中[创建自定义组织单位 (OU)](active-directory-ds-admin-guide-create-ou.md)。 此外，可以在这些自定义 OU 中创建其他 OU、用户、组或服务帐户。 在自定义 OU 中创建的对象都不会同步回到 Azure AD 租户。 这些对象只能在托管域中使用。 因此，无法使用 Azure AD PowerShell cmdlet、Azure AD 图形 API 或 Azure AD 管理 UI 查看这些对象。

## <a name="related-content"></a>相关内容
* [功能 - Azure AD 域服务](active-directory-ds-features.md)
* [部署方案 - Azure AD 域服务](active-directory-ds-scenarios.md)
* [有关 Azure AD 域服务的网络注意事项](active-directory-ds-networking.md)
* [Azure AD 域服务入门](active-directory-ds-getting-started.md)
