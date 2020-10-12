---
title: Azure AD 域服务中的同步原理 | Microsoft Docs
description: 了解从 Azure AD 租户或本地 Active Directory 域服务环境到 Azure Active Directory 域服务托管域的对象和凭据的同步过程的工作原理。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 10eec1527fb0ac5109822da398642613219771f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86039834"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-active-directory-domain-services-managed-domain"></a>如何在 Azure Active Directory 域服务托管域中同步对象和凭据

可在域中本地创建 Azure Active Directory 域服务 (Azure AD DS) 托管域中的对象和凭据，或从 Azure Active Directory (Azure AD) 租户进行同步。 首次部署 Azure AD DS 时，将配置并启动自动单向同步，以从 Azure AD 中复制对象。 此单向同步将继续在后台运行，以使 Azure AD DS 托管域与 Azure AD 中的任何更改保持同步。 不会发生从 Azure AD DS 到 Azure AD 的逆向同步。

在混合环境中，可使用 Azure AD Connect 将本地 AD DS 域中的对象和凭据同步到 Azure AD。 一旦这些对象成功同步到 Azure AD，自动后台同步就会将这些对象和凭据提供给使用托管域的应用程序。

下图演示了 Azure AD DS、Azure AD 和可选本地 AD DS 环境之间的同步原理：

![在 Azure Active Directory 域服务托管域中的同步概述](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>从 Azure AD 到 Azure AD DS 的同步

用户帐户、组成员身份和凭据哈希从 Azure AD 单向同步到 Azure AD DS。 此同步过程是自动的。 不需要配置、监视或管理此同步过程。 初始同步可能需要几个小时到几天的时间，具体取决于 Azure AD 目录中的对象数。 初始同步完成后，在 Azure AD 中进行的更改（如密码或属性更改）将自动同步到 Azure AD DS。

在 Azure AD 中创建用户后，这些用户在 Azure AD 中更改其密码之前不会同步到 Azure AD DS。 此密码更改过程会导致在 Azure AD 中生成并存储用于 Kerberos 和 NTLM 身份验证的密码哈希。 需要使用密码哈希才能在 Azure AD DS 中成功进行用户身份验证。

同步过程设计为单向的。 Azure AD DS 中的更改不会逆向同步回 Azure AD。 托管域在很大程度上是只读的，但你可以创建的自定义 OU 除外。 无法对托管域中的用户属性、用户密码或组成员身份进行更改。

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>属性同步和映射到 Azure AD DS

下表列出了一些常见的属性，以及如何将它们同步到 Azure AD DS。

| Azure AD DS 中的属性 | Source | 注释 |
|:--- |:--- |:--- |
| UPN | Azure AD 租户中用户的“UPN”属性 | Azure AD 租户中的 UPN 属性是按原样同步到 Azure AD DS 的。 登录到托管域的最可靠方法是使用 UPN。 |
| SAMAccountName | Azure AD 租户中的或自动生成的用户的“mailNickname”属性 | “SAMAccountName”属性源自 Azure AD 租户中的“mailNickname”属性 。 如果多个用户帐户具有相同的“mailNickname”属性，会自动生成“SAMAccountName” 。 如果用户的“mailNickname”或“UPN”前缀长度超过 20 个字符，会自动生成“SAMAccountName”，以满足“SAMAccountName”属性不超过 20 个字符的限制   。 |
| 密码 | 从 Azure AD 租户同步用户的密码 | NTLM 或 Kerberos 身份验证所需的旧密码哈希将从 Azure AD 租户同步。 如果为使用 Azure AD Connect 混合同步配置了 Azure AD 租户，则这些密码哈希源自本地 AD DS 环境。 |
| 主用户/组 SID | 自动生成 | 用户/组帐户的主 SID 是在 Azure AD DS 中自动生成的。 此属性与本地 AD DS 环境中对象的主用户/组 SID 不匹配。 之所以不匹配，是因为托管域的 SID 命名空间不同于本地 AD DS 域。 |
| 用户和组的 SID 历史记录 | 本地主用户和组 SID | Azure AD DS 中用户和组的“SidHistory”属性已设置为与本地 AD DS 环境中相应的主用户或组 SID 相匹配。 借助此功能可以更方便地将本地应用程序直接迁移到 Azure AD DS，因为不需要重新 ACL 资源。 |

> [!TIP]
> “使用 UPN 格式登录到托管域”。可以针对托管域中的某些用户帐户自动生成“SAMAccountName”属性（例如 `AADDSCONTOSO\driley`）。 用户的自动生成的“SAMAccountName”可能不同于其 UPN 前缀，因此用它登录并不总是可靠的。
>
> 例如，如果多个用户具有相同的“mailNickname”属性或者用户具有很长的 UPN 前缀，可能会自动生成这些用户的“SAMAccountName” 。 使用 UPN 格式（例如 `driley@aaddscontoso.com`）可靠地登录到托管域。

### <a name="attribute-mapping-for-user-accounts"></a>用户帐户的属性映射

下表演示了 Azure AD 中用户对象的特定属性如何同步到 Azure AD DS 中的相应属性。

| Azure AD 中的用户属性 | Azure AD DS 中的用户属性 |
|:--- |:--- |
| accountEnabled |userAccountControl（设置或清除 ACCOUNT_DISABLED 位） |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| employeedId |employeeId |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |title |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName（有时可能自动生成） |
| manager |manager |
| mobile |mobile |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl（设置或清除 DONT_EXPIRE_PASSWORD 位） |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| ProxyAddresses | ProxyAddresses |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>组的属性映射

下表演示了 Azure AD 中组对象的特定属性如何同步到 Azure AD DS 中的相应属性。

| Azure AD 中的组属性 | Azure AD DS 中的组属性 |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName（有时可能自动生成） |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| ProxyAddresses | ProxyAddresses |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>从本地 AD DS 到 Azure AD 和 Azure AD DS 的同步

Azure AD Connect 用于将用户帐户、组成员身份和凭据哈希从本地 AD DS 环境同步到 Azure AD。 将同步用户帐户的属性，例如 UPN 和本地安全标识符 (SID)。 若要使用 Azure AD DS 登录,，则 NTLM 和 Kerberos 身份验证所需的旧凭据哈希也会同步到 Azure AD。

> [!IMPORTANT]
> 安装和配置的 Azure AD Connect 应仅用于与本地 AD DS 环境同步。 不支持在托管域中安装 Azure AD Connect 以将对象同步回 Azure AD。

如果配置了写回，则会将 Azure AD 中的更改同步回本地 AD DS 环境。 例如，如果用户使用 Azure AD 的自助密码管理更改了密码，则更改的密码会更新回本地 AD DS 环境。

> [!NOTE]
> 请始终使用最新版本的 Azure AD Connect，确保获得所有已知 Bug 的修复程序。

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>从多林本地环境同步

许多组织都拥有包含多个林的相当复杂的本地 AD DS 环境。 Azure AD Connect 支持将用户、组和凭据哈希从多林环境同步到 Azure AD。

Azure AD 具有更简单的平面命名空间。 为了使用户能够可靠地访问 Azure AD 保护的应用程序，需要解决不同林中用户帐户的 UPN 冲突。 和 Azure AD 类似，托管域使用平面 OU 结构。 即使你在本地配置了分层 OU 结构，所有用户帐户和组也都存储在“AADDC 用户”容器中，尽管它们从不同的本地域或林同步。 托管域会平展任何分层 OU 结构。

如前文所述，不会从 Azure AD DS 同步回 Azure AD。 可在 Azure AD DS 中[创建自定义组织单位 (OU)](create-ou.md)，然后在这些自定义 OU 中创建用户、组或服务帐户。 在自定义 OU 中创建的对象都不会同步回 Azure AD。 这些对象仅在托管域中可用，不能使用 Azure AD PowerShell cmdlet、Microsoft Graph API 或 Azure AD 管理 UI 来显示它们。

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>哪些信息不会同步到 Azure AD DS

下列对象或属性不会从本地 AD DS 环境同步到 Azure AD 或 Azure AD DS：

* **排除的属性：** 使用 Azure AD Connect 从本地 AD DS 环境同步到 Azure AD 时，可以选择排除某些属性。 这些排除的属性在 Azure AD DS 中不可用。
* **组策略：** 本地 AD DS 环境中配置的组策略不会同步到 Azure AD DS。
* **Sysvol 文件夹：** 本地 AD DS 环境中 Sysvol 文件夹的内容不会同步到 Azure AD DS。
* **计算机对象：** 加入本地 AD DS 环境中的计算机，其计算机对象不会同步到 Azure AD DS。 这些计算机未与托管域建立信任关系，它们仅属于本地 AD DS 环境。 在 Azure AD DS 中，仅显示已显式域加入该托管域的计算机的计算机对象。
* **用户和组的 SidHistory 属性：** 本地 AD DS 环境中的主用户和主组 SID 将同步到 Azure AD DS。 但是，用户和组的现有“SidHistory”属性不会从本地 AD DS 环境同步到 Azure AD DS。
* **组织单位 (OU) 结构：** 在本地 AD DS 环境中定义的组织单位不会同步到 Azure AD DS。 Azure AD DS 中有两个内置 OU - 一个用于用户，另一个用于计算机。 托管域具有平面 OU 结构。 可以选择[在托管域中创建自定义 OU](create-ou.md)。

## <a name="password-hash-synchronization-and-security-considerations"></a>密码哈希同步和安全注意事项

启用 Azure AD DS 时，需要使用 NTLM + Kerberos 身份验证的旧密码哈希。 Azure AD 不存储明文密码，因此不能为现有用户帐户自动生成这些哈希。 一旦生成和存储，与 NTLM 和 Kerberos 兼容的密码哈希始终以加密形式存储在 Azure AD 中。

每个 Azure AD 租户的加密密钥是唯一的。 这些哈希会进行加密，只有 Azure AD DS 才有权访问解密密钥。 Azure AD 中没有其他服务或组件有权访问解密密钥。

然后，旧密码哈希将从 Azure AD 同步到托管域的域控制器。 Azure AD DS 中这些托管域控制器的磁盘会进行静态加密。 会在这些域控制器上存储并保护这些密码哈希，其方式类似于在本地 AD DS 环境中存储并保护密码。

对于仅云 Azure AD 环境，[用户必须重置/更改其密码](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)以便生成所必需的密码哈希并将其存储在 Azure AD 中。 对于启用 Azure AD 域服务后在 Azure AD 中创建的任何云用户帐户，会生成密码哈希并采用与 NTLM 和 Kerberos 兼容的格式进行存储。 所有云用户帐户在同步到 Azure AD DS 之前都必须更改其密码。

对于使用 Azure AD Connect 从本地 AD DS 环境同步的混合用户帐户，必须[配置 Azure AD Connect 以同步采用与 NTLM 和 Kerberos 兼容的格式的密码哈希](tutorial-configure-password-hash-sync.md)。

## <a name="next-steps"></a>后续步骤

有关密码哈希同步具体细节的详细信息，请参阅[使用 Azure AD Connect 进行密码哈希同步的工作原理](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context)。

若要开始使用 Azure AD DS，请[创建托管域](tutorial-create-instance.md)。
