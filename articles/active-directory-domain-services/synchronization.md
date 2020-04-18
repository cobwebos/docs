---
title: 同步在 Azure AD 域服务中的工作原理 |微软文档
description: 了解同步过程如何适用于从 Azure AD 租户或本地活动目录域服务环境到 Azure 活动目录域服务托管域的对象和凭据。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 38ed48df4d681543cc30daccf46b98635d973b89
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639913"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-ad-domain-services-managed-domain"></a>如何在 Azure AD 域服务托管域中同步对象和凭据

Azure 活动目录域服务 （AD DS） 托管域中的对象和凭据可以在域中本地创建，也可以从 Azure 活动目录 （Azure AD） 租户同步。 首次部署 Azure AD DS 时，将配置自动单向同步，并开始从 Azure AD 复制对象。 此单向同步将继续在后台运行，以保持 Azure AD 托管域与 Azure AD 的任何更改保持最新。 从 Azure AD DS 到 Azure AD 不执行任何同步。

在混合环境中，本地 AD DS 域中的对象和凭据可以使用 Azure AD 连接同步到 Azure AD。 成功将这些对象同步到 Azure AD 后，自动后台同步将使用这些对象和凭据提供给使用 Azure AD DS 托管域的应用程序。

下图说明了同步如何在 Azure AD DS、Azure AD 和可选的本地 AD DS 环境之间工作：

![Azure AD 域服务托管域的同步概述](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>从 Azure AD 到 Azure AD DS 的同步

用户帐户、组成员身份和凭据哈希以 Azure AD 到 Azure AD DS 的方式同步。 此同步过程是自动的。 您无需配置、监视或管理此同步过程。 初始同步可能需要几个小时到几天，具体取决于 Azure AD 目录中的对象数。 完成初始同步后，在 Azure AD 中所做的更改（如密码或属性更改）将自动同步到 Azure AD DS。

在 Azure AD 中创建用户时，在 Azure AD 中更改其密码之前，不会将其同步到 Azure AD DS。 此密码更改过程会导致在 Azure AD 中生成并存储用于 Kerberos 和 NTLM 身份验证的密码哈希。 需要密码哈希才能在 Azure AD DS 中成功验证用户。

同步过程是单向的/单向的。 从 Azure AD DS 到 Azure AD 的更改没有反向同步。 Azure AD DS 托管域大部分是只读的，但您可以创建的自定义 O 除外。 不能更改 Azure AD DS 托管域中的用户属性、用户密码或组成员身份。

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>属性同步和映射到 Azure AD DS

下表列出了一些常见属性以及它们如何同步到 Azure AD DS。

| Azure AD DS 中的属性 | 源 | 说明 |
|:--- |:--- |:--- |
| UPN | Azure AD 租户中的用户的*UPN*属性 | Azure AD 租户中的 UPN 属性与 Azure AD DS 同步。 登录到 Azure AD DS 托管域的最可靠方法是使用 UPN。 |
| SAMAccountName | Azure AD 租户或自动生成的用户*的邮件昵称*属性 | *SAMAccountName*属性来自 Azure AD 租户中的*邮件昵称*属性。 如果多个用户帐户具有相同的*邮件昵称*属性，则*SAMAccountName*将自动生成。 如果用户*的邮件昵称*或*UPN*前缀超过 20 个字符，则会自动生成*SAMAccountName*以满足*SAMAccountName*属性上的 20 个字符限制。 |
| 密码 | 来自 Azure AD 租户的用户密码 | NTLM 或 Kerberos 身份验证所需的旧密码哈希从 Azure AD 租户同步。 如果 Azure AD 租户配置为使用 Azure AD Connect 进行混合同步，则这些密码哈希值来自本地 AD DS 环境。 |
| 主用户/组 SID | 自动生成 | 用户/组帐户的主 SID 在 Azure AD DS 中自动生成。 此属性与本地 AD DS 环境中对象的主用户/组 SID 不匹配。 这种不匹配的原因是 Azure AD DS 托管域的 SID 命名空间与本地 AD DS 域不同。 |
| 用户和组的 SID 历史记录 | 本地主用户和组 SID | Azure AD DS 中用户和组的*Sid 历史*属性设置为与本地 AD DS 环境中的相应主用户或组 SID 匹配。 此功能有助于使本地应用程序升降和移位到 Azure AD DS 变得更加容易，因为您不需要重新使用 ACL 资源。 |

> [!TIP]
> **使用 UPN 格式登录到托管域***SAMAccountName*属性（如`AADDSCONTOSO\driley`）可能是为 Azure AD DS 托管域中的某些用户帐户自动生成的。 用户自动生成的*SAMAccountName*可能与其 UPN 前缀不同，因此并不总是一种可靠的登录方式。
>
> 例如，如果多个用户具有相同的*邮件昵称*属性或用户具有过长的 UPN 前缀，则这些用户的*SAMAccountName*可能是自动生成的。 使用 UPN 格式（如`driley@aaddscontoso.com`）可靠地登录到 Azure AD DS 托管域。

### <a name="attribute-mapping-for-user-accounts"></a>用户帐户的属性映射

下表说明了 Azure AD 中用户对象的特定属性如何与 Azure AD DS 中的相应属性同步。

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
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName（有时可能自动生成） |
| mobile |mobile |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl（设置或清除 DONT_EXPIRE_PASSWORD 位） |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>组的属性映射

下表说明了 Azure AD 中组对象的特定属性如何与 Azure AD DS 中的相应属性同步。

| Azure AD 中的组属性 | Azure AD DS 中的组属性 |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName（有时可能自动生成） |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>从本地 AD DS 到 Azure AD 和 Azure AD DS 的同步

Azure AD 连接用于将用户帐户、组成员身份和凭据哈希从本地 AD DS 环境同步到 Azure AD。 用户帐户的属性（如 UPN 和本地安全标识符 （SID） 是同步的。 要使用 Azure AD DS 登录，NTLM 和 Kerberos 身份验证所需的旧密码哈希也会同步到 Azure AD。

> [!IMPORTANT]
> 安装和配置的 Azure AD Connect 应仅用于与本地 AD DS 环境同步。 不支持在 Azure AD DS 托管域中安装 Azure AD Connect 以将对象同步回 Azure AD。

如果配置回写，Azure AD 的更改将同步回本地 AD DS 环境。 例如，如果用户使用 Azure AD 自助服务密码管理更改其密码，则密码将更新回本地 AD DS 环境中。

> [!NOTE]
> 请始终使用最新版本的 Azure AD Connect，确保获得所有已知 Bug 的修复程序。

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>从多林本地环境同步

许多组织具有相当复杂的本地 AD DS 环境，其中包括多个林。 Azure AD 连接支持将用户、组和凭据哈希从多林环境同步到 Azure AD。

Azure AD 具有更简单、更平坦的命名空间。 为了使用户能够可靠地访问 Azure AD 保护的应用程序，需要解决不同林中用户帐户的 UPN 冲突。 Azure AD DS 托管域使用平面 OU 结构，类似于 Azure AD。 所有用户帐户和组都存储在*AADDC 用户*容器中，尽管从不同的本地域或林同步，即使您在本地配置了分层 OU 结构也是如此。 Azure AD DS 托管域可平展任何分层 OU 结构。

如之前详述的，Azure AD DS 没有同步回 Azure AD。 您可以在 Azure AD DS 中[创建自定义组织单位 （OU），](create-ou.md)然后在这些自定义 OU 中创建用户、组或服务帐户。 在自定义 O 中创建的对象都不会同步回 Azure AD。 这些对象仅在 Azure AD DS 托管域中可用，并且使用 Azure AD PowerShell cmdlet、Microsoft 图形 API 或使用 Azure AD 管理 UI 不可见。

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>未同步到 Azure AD DS 的内容

以下对象或属性不会从本地 AD DS 环境同步到 Azure AD 或 Azure AD DS：

* **排除的属性：** 您可以选择使用 Azure AD 连接从本地 AD DS 环境中排除某些属性从同步到 Azure AD。 这些排除的属性在 Azure AD DS 中不可用。
* **组策略：** 在本地 AD DS 环境中配置的组策略不会同步到 Azure AD DS。
* **Sysvol 文件夹：** 本地 AD DS 环境中的*Sysvol*文件夹的内容未同步到 Azure AD DS。
* **计算机对象：** 连接到本地 AD DS 环境的计算机的计算机对象不会同步到 Azure AD DS。 这些计算机与 Azure AD DS 托管域没有信任关系，并且仅属于本地 AD DS 环境。 在 Azure AD DS 中，仅显示已显式加入托管域的计算机的计算机对象。
* **用户和组的 Sid 历史属性：** 本地 AD DS 环境中的主用户和主组 SID 同步到 Azure AD DS。 但是，用户和组的现有*SidHistory*属性不会从本地 AD DS 环境同步到 Azure AD DS。
* **组织单位 （OU） 结构：** 在本地 AD DS 环境中定义的组织单位不同步到 Azure AD DS。 Azure AD DS 中有两个内置的 O，一个用于用户，一个用于计算机。 Azure AD DS 托管域具有扁平的 OU 结构。 您可以选择[在托管域中创建自定义 OU。](create-ou.md)

## <a name="password-hash-synchronization-and-security-considerations"></a>密码哈希同步和安全注意事项

启用 Azure AD DS 时，需要 NTLM 和 Kerberos 身份验证的旧密码哈希。 Azure AD 不存储明文密码，因此无法自动为现有用户帐户生成这些哈希。 生成和存储后，NTLM 和 Kerberos 兼容的密码哈希始终以加密方式存储在 Azure AD 中。

加密密钥对于每个 Azure AD 租户是唯一的。 这些哈希是加密的，以便只有 Azure AD DS 才能访问解密密钥。 Azure AD 中没有其他服务或组件有权访问解密密钥。

然后，旧密码哈希从 Azure AD 同步到 Azure AD DS 托管域的域控制器中。 Azure AD DS 中这些托管域控制器的磁盘在静态时进行加密。 这些密码哈希存储在这些域控制器上并加以保护，类似于密码在本地 AD DS 环境中的存储和保护方式。

对于仅云 Azure AD 环境，[用户必须重置/更改其密码](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)，以便生成和存储在 Azure AD 中所需的密码哈希。 对于启用 Azure AD 域服务后在 Azure AD 中创建的任何云用户帐户，会生成密码哈希并采用与 NTLM 和 Kerberos 兼容的格式进行存储。 所有云用户帐户在同步到 Azure AD DS 之前都必须更改其密码。

对于使用 Azure AD Connect 从本地 AD DS 环境同步的混合用户帐户，必须[配置 Azure AD 连接以同步 NTLM 和 Kerberos 兼容格式的密码哈希](tutorial-configure-password-hash-sync.md)。

## <a name="next-steps"></a>后续步骤

有关密码同步细节的详细信息，请参阅[密码哈希同步如何与 Azure AD 连接配合](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context)使用。

要开始使用 Azure AD DS，[请创建一个托管域](tutorial-create-instance.md)。
