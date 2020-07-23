---
title: Azure AD 域服务的管理概念 |Microsoft Docs
description: 了解如何管理 Azure Active Directory 域服务托管域以及用户帐户和密码的行为
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: iainfou
ms.openlocfilehash: 4f1f6c60ef2e0ccdd3e166e2272fe917ead3ed2e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84735041"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Azure Active Directory 域服务中的用户帐户、密码和管理的管理概念

当你创建并运行 Azure Active Directory 域服务（AD DS）托管域时，与传统的本地 AD DS 环境相比，行为存在一些差异。 在 Azure AD DS 中使用的管理工具与自行托管域相同，但不能直接访问域控制器（DC）。 根据用户帐户创建的源，密码策略和密码哈希的行为也存在一些差异。

此概念文章详细介绍了如何管理托管域和用户帐户的不同行为，具体取决于创建的方式。

## <a name="domain-management"></a>域管理

托管域是一个 DNS 命名空间和匹配目录。 在托管域中，包含所有资源（如用户和组、凭据和策略）的域控制器（Dc）都是托管服务的一部分。 为实现冗余，将创建两个 Dc 作为托管域的一部分。 你无法登录到这些 Dc 来执行管理任务。 相反，你将创建一个已加入托管域的管理 VM，然后安装你的常规 AD DS 管理工具。 例如，可以使用 Active Directory 管理中心或 Microsoft 管理控制台（MMC）管理单元（如 DNS 或组策略对象）。

## <a name="user-account-creation"></a>用户帐户创建

可以通过多种方式在托管域中创建用户帐户。 大多数用户帐户是在中从 Azure AD 同步的，也可能包括从本地 AD DS 环境同步的用户帐户。 你还可以直接在托管域中手动创建帐户。 某些功能（如初始密码同步或密码策略）的行为方式不同，具体取决于创建用户帐户的方式和位置。

* 可以从 Azure AD 同步用户帐户。 这包括直接在 Azure AD 中创建的仅限云的用户帐户，以及使用 Azure AD Connect 从本地 AD DS 环境同步的混合用户帐户。
    * 托管域中的大部分用户帐户是通过 Azure AD 中的同步过程创建的。
* 可以在托管域中手动创建用户帐户，Azure AD 中不存在该帐户。
    * 如果需要为仅在托管域中运行的应用程序创建服务帐户，你可以在托管域中手动创建它们。 由于同步是 Azure AD 的一种方式，因此在托管域中创建的用户帐户不会同步回 Azure AD。

## <a name="password-policy"></a>密码策略

Azure AD DS 包括一个默认密码策略，该策略定义帐户锁定、最长密码期限和密码复杂性等设置。 帐户锁定策略的设置应用于托管域中的所有用户，而不考虑上一节中所述的用户创建方式。 一些设置（如最小密码长度和密码复杂性）仅适用于直接在托管域中创建的用户。

你可以创建自己的自定义密码策略来替代托管域中的默认策略。 然后，可以根据需要将这些自定义策略应用到特定的用户组。

有关如何根据用户创建的源应用密码策略的详细信息，请参阅[托管域上的密码和帐户锁定策略][password-policy]。

## <a name="password-hashes"></a>密码哈希

若要对托管域上的用户进行身份验证，Azure AD DS 需要密码哈希，其格式适用于 NT LAN Manager (NTLM) 和 Kerberos 身份验证。 除非为租户启用了 Azure AD DS，否则 Azure AD 不会以 NTLM 或 Kerberos 身份验证所需的格式生成或存储密码哈希。 出于安全考虑，Azure AD 也不以明文形式存储任何密码凭据。 因此，Azure AD 无法基于用户的现有凭据自动生成这些 NTLM 或 Kerberos 密码哈希。

对于仅限云的用户帐户，用户必须更改其密码，然后才能使用托管域。 此密码更改过程会导致在 Azure AD 中生成并存储用于 Kerberos 和 NTLM 身份验证的密码哈希。 在更改密码之前，帐户不会从 Azure AD 同步到 Azure AD DS。

对于使用 Azure AD Connect 从本地 AD DS 环境同步的用户，[启用密码哈希同步][hybrid-phs]。

> [!IMPORTANT]
> 仅当你为 Azure AD 租户启用 Azure AD DS 时，Azure AD Connect 才同步旧密码哈希。 如果仅使用 Azure AD Connect 将本地 AD DS 环境与 Azure AD 同步，则不使用旧密码哈希。
>
> 如果旧版应用程序不使用 NTLM 身份验证或 LDAP 简单绑定，则建议你禁用 Azure AD DS 的 NTLM 密码哈希同步。 有关详细信息，请参阅[禁用弱密码套件和 NTLM 凭据哈希同步][secure-domain]。

经过适当的配置后，可用的密码哈希将存储在托管域中。 删除托管域也会删除其中存储的所有密码哈希。 如果以后创建另一个托管域，则不能重复使用 Azure AD 中的同步凭据信息，你必须重新配置密码哈希同步以再次存储密码哈希。 以前加入域的 VM 或用户无法立即进行身份验证 - Azure AD 需要在新的托管域中生成并存储密码哈希。 有关详细信息，请参阅 [Azure AD DS 和 Azure AD Connect 的密码哈希同步过程][azure-ad-password-sync]。

> [!IMPORTANT]
> 安装和配置的 Azure AD Connect 应仅用于与本地 AD DS 环境同步。 不支持在托管域中安装 Azure AD Connect 以将对象同步回 Azure AD。

## <a name="forests-and-trusts"></a>林和信任

林是 Active Directory 域服务 (AD DS) 用来对一个或多个域进行分组的逻辑构造 。 域随后会存储用户或组的对象，并提供身份验证服务。

在 Azure AD DS 中，林只包含一个域。 本地 AD DS 林通常包含许多域。 在大型组织中，特别是在合并和收购之后，最终可能会得到多个本地林，每个林又包含多个域。

默认情况下，托管域作为*用户*林创建。 此类林可同步 Azure AD 中的所有对象，包括在本地 AD DS 环境中创建的所有用户帐户。 用户帐户可以直接针对托管域进行身份验证，例如登录到已加入域的 VM。 当可以同步密码哈希，并且用户不使用独占登录方法（如智能卡身份验证）时，用户林可发挥作用。

在 Azure AD DS 资源林中，用户从本地 AD DS 通过单向林信任进行身份验证 。 采用此方法时，用户对象和密码哈希不会同步到 Azure AD DS。 用户对象和凭据仅存在于本地 AD DS 中。 此方法使企业可以在 Azure 中托管依赖于经典身份验证（如 LDAPS、Kerberos 或 NTLM）的资源和应用程序平台，不过可消除任何身份验证问题或疑虑。 Azure AD DS 资源林目前为预览版。

有关 Azure AD DS 中的林类型的详细信息，请参阅[什么是资源林？][concepts-forest]和[林信任如何在 Azure AD DS 中工作？][concepts-trust]

## <a name="azure-ad-ds-skus"></a>Azure AD DS Sku

在 Azure AD DS 中，可用的性能和功能基于 SKU。 当你创建托管域时，你可以选择 SKU，你可以在部署托管域后，根据业务需求的变化来切换 Sku。 下表概述了可用的 Sku 以及它们之间的差异：

| SKU 名称   | 最大对象计数 | 备份频率 | 最大出站林信任数量 |
|------------|----------------------|------------------|----|
| 标准   | 无限制            | 每7天     | 0  |
| Enterprise | 无限制            | 每3天     | 5  |
| 高级    | 无限制            | 每天            | 10 |

在这些 Azure AD DS Sku 之前，使用托管域中的对象（用户和计算机帐户）数量的计费模型。 对于托管域中的对象数量，不再提供可变定价。

有关详细信息，请参阅[AZURE AD DS 定价页][pricing]。

### <a name="managed-domain-performance"></a>托管域性能

域性能因应用程序的身份验证的实现方式而异。 额外的计算资源可能有助于改善查询响应时间，并缩短同步操作所用的时间。 当 SKU 级别增加时，可用于托管域的计算资源将增加。 监视应用程序的性能并规划所需的资源。

如果业务或应用程序要求发生更改，并且你需要托管域的额外计算能力，则可以切换到其他 SKU。

### <a name="backup-frequency"></a>备份频率

备份频率决定了托管域快照的拍摄频率。 备份是 Azure 平台管理的自动化过程。 当你的托管域出现问题时，Azure 支持人员可以帮助你从备份还原。 由于同步仅*从*Azure AD 的一种方式进行，因此，托管域中的任何问题都不会影响 Azure AD 或本地 AD DS 环境和功能。

随着 SKU 级别的增加，这些备份快照的频率会增加。 查看您的业务需求和恢复点目标（RPO），以确定托管域所需的备份频率。 如果业务或应用程序要求发生更改，并且需要更频繁的备份，则可以切换到其他 SKU。

### <a name="outbound-forest-trusts"></a>出站林信任

上一节详细介绍了从托管域到本地 AD DS 环境（当前为预览版）的单向出站林信任。 SKU 决定了可以为托管域创建的林信任的最大数目。 查看你的业务和应用程序要求，确定你实际需要的信任数量，并选择适当的 Azure AD DS SKU。 同样，如果你的业务要求发生了变化，并且你需要创建其他林信任，则可以切换到其他 SKU。

## <a name="next-steps"></a>后续步骤

若要开始，请[创建 AZURE AD DS 托管域][create-instance]。

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
