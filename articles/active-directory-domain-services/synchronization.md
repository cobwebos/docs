---
title: 同步在 Azure AD 域服务中的工作方式 |Microsoft Docs
description: 了解同步过程如何处理 Azure AD 租户或本地 Active Directory 域服务环境中的对象和凭据到 Azure Active Directory 的域服务托管域。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: iainfou
ms.openlocfilehash: 9a7baa6385e0130b784b264a4c53c232ae8a1b50
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980463"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-ad-domain-services-managed-domain"></a>如何在 Azure AD 域服务托管域中同步对象和凭据

可在域中本地创建 Azure Active Directory 域服务 (AD DS) 托管域中的对象和凭据, 或从 Azure Active Directory (AD) 租户进行同步。 首次部署 Azure AD DS 时, 将配置并启动自动单向同步, 以将对象从 Azure AD 中复制。 此单向同步将继续在后台运行, 以使 Azure AD DS 托管域与 Azure AD 中的任何更改保持同步。

在混合环境中, 可使用 Azure AD Connect 将本地 AD DS 域中的对象和凭据同步到 Azure AD。 一旦这些对象成功同步到 Azure AD 后, 自动后台同步就会使用 Azure AD DS 托管域将这些对象和凭据提供给应用程序使用。

下图说明了 Azure AD DS、Azure AD 和可选本地 AD DS 环境之间的同步工作方式:

![Azure AD 域服务托管域的同步概述](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>从 Azure AD 到 Azure AD DS 的同步

用户帐户、组成员身份和凭据哈希从 Azure AD 同步到 Azure AD DS。 此同步过程是自动的。 不需要配置、监视或管理此同步过程。 初始同步可能需要几个小时到几天的时间, 具体取决于 Azure AD 目录中的对象数。 初始同步完成后, 在 Azure AD 中进行的更改 (如密码或属性更改) 大约需要20-30 分钟才能在 Azure AD DS 中更新。

按照设计, 同步过程是一种方法。 Azure AD DS 中的更改不会反向同步回 Azure AD。 除了可以创建的自定义 Ou 外, Azure AD DS 托管域在大部分情况上都是只读的。 不能更改 Azure AD DS 托管域中的用户属性、用户密码或组成员身份。

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>属性同步和到 Azure AD DS 的映射

下表列出了一些常见的属性, 以及如何将它们同步到 Azure AD DS。

| Azure AD DS 中的属性 | Source | 说明 |
|:--- |:--- |:--- |
| UPN | Azure AD 租户中的用户的*UPN*属性 | Azure AD 租户中的 UPN 属性按原样同步到 Azure AD DS。 登录到 Azure AD DS 托管域的最可靠方法是使用 UPN。 |
| SAMAccountName | 用户在 Azure AD 租户中的*mailNickname*属性, 或自动生成 | *SAMAccountName*属性源于 Azure AD 租户中的*mailNickname*属性。 如果多个用户帐户具有相同的*mailNickname*属性, 则将自动生成*SAMAccountName* 。 如果用户的*mailNickname*或*UPN*前缀的长度超过20个字符, 则将自动生成*samaccountname*以满足*SAMAccountName*属性的20个字符的限制。 |
| 密码 | Azure AD 租户中的用户密码 | NTLM 或 Kerberos 身份验证所需的旧密码哈希将从 Azure AD 租户同步。 如果 Azure AD 租户配置为使用 Azure AD Connect 进行混合同步, 则这些密码哈希源自本地 AD DS 环境。 |
| 主用户/组 SID | 生成 | 用户/组帐户的主 SID 在 Azure AD DS 中自动生成。 此属性与本地 AD DS 环境中对象的主用户/组 SID 不匹配。 这种不匹配是因为 Azure AD DS 托管域的 SID 命名空间与本地 AD DS 域不同。 |
| 用户和组的 SID 历史记录 | 本地主用户和组 SID | Azure AD DS 中的用户和组的*SidHistory*属性设置为与本地 AD DS 环境中相应的主用户或组 SID 相匹配。 此功能可让本地应用程序的直接迁移和移动 Azure AD DS, 因为无需重新创建 ACL 资源。 |

> [!TIP]
> **使用 UPN 格式登录到托管域**可以为 Azure AD DS 托管域`CONTOSO\driley`中的某些用户帐户自动生成 SAMAccountName 属性 (如)。 用户自动生成的*SAMAccountName*可能不同于其 UPN 前缀, 因此不一定是一种可靠的登录方式。 例如, 如果多个用户具有相同的*mailNickname*属性, 或者用户的 UPN 前缀过长, 则可能会自动生成这些用户的*SAMAccountName* 。 使用 UPN 格式 (例如`driley@contoso.com`) 来可靠地登录到 Azure AD DS 托管域。

### <a name="attribute-mapping-for-user-accounts"></a>用户帐户的属性映射

下表说明了 Azure AD 中的用户对象的特定属性如何同步到 Azure AD DS 中的相应属性。

| Azure AD 中的用户属性 | Azure AD DS 中的用户属性 |
|:--- |:--- |
| accountEnabled |userAccountControl（设置或清除 ACCOUNT_DISABLED 位） |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |title |
| 邮件 |邮件 |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (有时可能会自动生成) |
| mobile |mobile |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl（设置或清除 DONT_EXPIRE_PASSWORD 位） |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| 省/自治区/直辖市 |号 |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>组的属性映射

下表说明了 Azure AD 中组对象的特定属性如何同步到 Azure AD DS 中的相应属性。

| Azure AD 中的组属性 | Azure AD DS 中的组属性 |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (有时可能会自动生成) |
| 邮件 |邮件 |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>从本地 AD DS 同步到 Azure AD 和 Azure AD DS

Azure AD Connect 用于将用户帐户、组成员身份和凭据哈希从本地 AD DS 环境同步到 Azure AD。 用户帐户的属性 (如 UPN 和本地安全标识符 (SID)) 是同步的。 若要使用 Azure AD 域服务登录, NTLM 和 Kerberos 身份验证所需的旧密码哈希也会同步到 Azure AD。

如果配置了写回, 则会将 Azure AD 中的更改同步回到本地 AD DS 环境。 例如, 如果用户使用 Azure AD 自助密码管理更改其密码, 则会在本地 AD DS 环境中更新密码。

> [!NOTE]
> 请始终使用最新版本的 Azure AD Connect，确保获得所有已知 Bug 的修复程序。

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>从多林本地环境同步

许多组织都拥有一个相当复杂的本地 AD DS 环境, 其中包括多个林。 Azure AD Connect 支持将用户、组和凭据哈希从多林环境同步到 Azure AD。

Azure AD 具有更简单、更简单的命名空间。 为了使用户能够可靠地访问 Azure AD 保护的应用程序，需要解决不同林中用户帐户的 UPN 冲突。 Azure AD DS 托管域使用类似于 Azure AD 的平面 OU 结构。 尽管已从不同的本地域或林同步, 但即使已在本地配置了分层 OU 结构, 所有用户帐户和组都存储在*AADDC Users*容器中。 Azure AD DS 托管域平展任何分层 OU 结构。

如前文所述, 没有从 Azure AD DS 到 Azure AD 的同步。 你可以在 Azure AD DS 中[创建自定义组织单位 (OU)](create-ou.md) , 然后在这些自定义 ou 中创建用户、组或服务帐户。 在自定义 Ou 中创建的任何对象都不会同步回 Azure AD。 这些对象仅在 Azure AD DS 托管域中可用, 不能使用 Azure AD PowerShell cmdlet、Azure AD 图形 API 或使用 Azure AD 管理 UI 来显示。

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>什么不同步到 Azure AD DS

下列对象或属性不会同步到 Azure AD 或 Azure AD DS:

* **排除的属性：** 您可以使用 Azure AD Connect 选择将某些属性从本地 AD DS 环境中排除同步到 Azure AD。 这些排除的属性在 Azure AD DS 中不可用。
* **组策略：** 本地 AD DS 环境中配置的组策略不会同步到 Azure AD DS。
* **Sysvol 文件夹:** 本地 AD DS 环境中*Sysvol*文件夹的内容不会同步到 Azure AD DS。
* **计算机对象：** 加入本地 AD DS 环境的计算机的计算机对象不会同步到 Azure AD DS。 这些计算机与 Azure AD DS 托管域没有信任关系, 且仅属于本地 AD DS 环境。 在 Azure AD DS 中, 只显示已显式加入域的计算机的计算机对象。
* **用户和组的 SidHistory 属性：** 本地 AD DS 环境中的主要用户和主要组 Sid 将同步到 Azure AD DS。 但是, 用户和组的现有*SidHistory*属性不会从本地 AD DS 环境同步到 Azure AD DS。
* **组织单位 (OU) 结构：** 在本地 AD DS 环境中定义的组织单位不会同步到 Azure AD DS。 Azure AD DS 中有两个内置 Ou-一个用于用户, 另一个用于计算机。 Azure AD DS 托管域具有平面 OU 结构。 你可以选择[在托管域中创建自定义 OU](create-ou.md)。

## <a name="password-hash-synchronization-and-security-considerations"></a>密码哈希同步和安全注意事项

启用 Azure AD DS 时, 需要使用 NTLM + Kerberos 身份验证的旧密码哈希。 Azure AD 不存储明文密码, 因此不能为现有用户帐户自动生成这些哈希。 生成并存储后, NTLM 和 Kerberos 兼容密码哈希将始终以加密方式存储在 Azure AD 中。 加密密钥对于每个 Azure AD 租户都是唯一的。 这些哈希已加密, 因此只有 Azure AD DS 有权访问解密密钥。 Azure AD 中没有其他服务或组件有权访问解密密钥。 然后, 旧密码哈希将从 Azure AD 同步到 Azure AD DS 托管域的域控制器。 Azure AD DS 中的这些托管域控制器的磁盘将静态加密。 这些密码哈希在这些域控制器上进行存储和保护, 这类似于在本地 AD DS 环境中存储和保护密码的方式。

对于仅限云的 Azure AD 环境,[用户必须重置/更改其密码](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds), 才能生成所需的密码哈希, 并将其存储到 Azure AD 中。 对于启用 Azure AD 域服务后在 Azure AD 中创建的任何云用户帐户，会生成密码哈希并采用与 NTLM 和 Kerberos 兼容的格式进行存储。 这些新帐户无需重置/更改其密码即可生成旧密码哈希。

对于使用 Azure AD Connect 从本地 AD DS 环境同步的混合用户帐户, 你必须[将 Azure AD Connect 配置为以 NTLM 和 Kerberos 兼容格式同步密码哈希](active-directory-ds-getting-started-password-sync-synced-tenant.md)。

## <a name="next-steps"></a>后续步骤

有关密码同步细节的详细信息, 请参阅[密码哈希同步如何与 Azure AD Connect 一起工作](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context)。

若要开始使用 Azure AD DS, 请[创建托管域](tutorial-create-instance.md)。
