---
title: Azure AD 域服务的管理概念 |微软文档
description: 了解如何管理 Azure 活动目录域服务托管域以及用户帐户和密码的行为
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: ba281ffb30801e0ae10cab10ceb95c0a3bffde2d
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640027"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Azure 活动目录域服务中用户帐户、密码和管理的管理概念

创建和运行 Azure 活动目录域服务 （AD DS） 托管域时，与传统本地 AD DS 环境相比，其行为存在一些差异。 在 Azure AD DS 中使用与自托管域相同的管理工具，但不能直接访问域控制器 （DC）。 密码策略和密码哈希的行为也存在一些差异，具体取决于用户帐户创建的来源。

此概念文章详细介绍了如何管理 Azure AD DS 托管域以及用户帐户的不同行为，具体取决于其创建方式。

## <a name="domain-management"></a>域管理

在 Azure AD DS 中，包含所有资源（如用户和组、凭据和策略）的域控制器 （DC） 是托管服务的一部分。 对于冗余，将创建两个 DC 作为 Azure AD DS 托管域的一部分。 您无法登录到这些 DC 以执行管理任务。 相反，您可以创建一个已加入 Azure AD DS 托管域的管理 VM，然后安装常规的 AD DS 管理工具。 例如，您可以使用活动目录管理中心或 Microsoft 管理控制台 （MMC） 管理控制台 （MMC） 管理单元（如 DNS 或组策略对象）。

## <a name="user-account-creation"></a>用户帐户创建

可以在 Azure AD DS 中以多种方式创建用户帐户。 大多数用户帐户从 Azure AD 同步，Azure AD 还可以包括从本地 AD DS 环境同步的用户帐户。 您还可以直接在 Azure AD DS 中手动创建帐户。 某些功能（如初始密码同步或密码策略）的行为方式和位置不同。

* 可以从 Azure AD 同步用户帐户。 这包括直接在 Azure AD 中创建的仅云用户帐户，以及使用 Azure AD Connect 从本地 AD DS 环境同步的混合用户帐户。
    * Azure AD DS 中的大多数用户帐户都是通过 Azure AD 的同步过程创建的。
* 用户帐户可以在 Azure AD DS 托管域中手动创建，并且在 Azure AD 中不存在。
    * 如果需要为仅在 Azure AD DS 中运行的应用程序创建服务帐户，则可以在托管域中手动创建它们。 由于同步是 Azure AD 的一种方式，因此在 Azure AD DS 中创建的用户帐户不会同步回 Azure AD。

## <a name="password-policy"></a>密码策略

Azure AD DS 包括默认密码策略，用于定义帐户锁定、最大密码期限和密码复杂性等设置。 帐户锁定策略等设置适用于 Azure AD DS 中的所有用户，而不考虑上一节中概述的用户创建方式。 少数设置（如最小密码长度和密码复杂性）仅适用于直接在 Azure AD DS 中创建的用户。

您可以创建自己的自定义密码策略来覆盖 Azure AD DS 中的默认策略。 然后，这些自定义策略可以根据需要应用于特定的用户组。

有关密码策略的应用方式因用户创建来源而异的详细信息，请参阅[托管域上的密码和帐户锁定策略][password-policy]。

## <a name="password-hashes"></a>密码哈希

若要对托管域上的用户进行身份验证，Azure AD DS 需要密码哈希，其格式适用于 NT LAN Manager (NTLM) 和 Kerberos 身份验证。 除非为租户启用了 Azure AD DS，否则 Azure AD 不会以 NTLM 或 Kerberos 身份验证所需的格式生成或存储密码哈希。 出于安全考虑，Azure AD 也不以明文形式存储任何密码凭据。 因此，Azure AD 无法基于用户的现有凭据自动生成这些 NTLM 或 Kerberos 密码哈希。

对于仅限云的用户帐户，用户必须更改其密码才能使用 Azure AD DS。 此密码更改过程会导致在 Azure AD 中生成并存储用于 Kerberos 和 NTLM 身份验证的密码哈希。 在更改密码之前，帐户不会从 Azure AD 同步到 Azure AD DS。

对于使用 Azure AD 连接从本地 AD DS 环境同步的用户，[启用密码哈希的同步][hybrid-phs]。

> [!IMPORTANT]
> Azure AD 连接仅在为 Azure AD 租户启用 Azure AD DS 时同步旧密码哈希。 如果仅使用 Azure AD 连接将本地 AD DS 环境与 Azure AD 同步，则不使用旧密码哈希。
>
> 如果旧应用程序不使用 NTLM 身份验证或 LDAP 简单绑定，我们建议您禁用 Azure AD DS 的 NTLM 密码哈希同步。 有关详细信息，请参阅[禁用弱密码套件和 NTLM 凭据哈希同步][secure-domain]。

经过适当的配置后，可用的密码哈希将存储在 Azure AD DS 托管域中。 删除 Azure AD DS 托管域也会删除其中存储的所有密码哈希。 如果以后创建 Azure AD DS 托管域，则无法重复使用 Azure AD 中的同步凭据信息 - 必须重新配置密码哈希同步以再次存储密码哈希。 以前加入域的 VM 或用户无法立即进行身份验证 - Azure AD 需要在新的 Azure AD DS 托管域中生成并存储密码哈希。 有关详细信息，请参阅 [Azure AD DS 和 Azure AD Connect 的密码哈希同步过程][azure-ad-password-sync]。

> [!IMPORTANT]
> 安装和配置的 Azure AD Connect 应仅用于与本地 AD DS 环境同步。 不支持在 Azure AD DS 托管域中安装 Azure AD Connect 以将对象同步回 Azure AD。

## <a name="forests-and-trusts"></a>林和信任

*林*是活动目录域服务 （AD DS） 用于对一个或多个*域*进行分组的逻辑构造。 然后，域为用户或组存储对象，并提供身份验证服务。

在 Azure AD DS 中，林只包含一个域。 本地 AD DS 林通常包含许多域。 在大型组织中，尤其是在合并和收购之后，您最终可能会拥有多个本地林，每个林都包含多个域。

默认情况下，Azure AD DS 托管域将作为*用户*林创建。 此类林可同步 Azure AD 中的所有对象，包括在本地 AD DS 环境中创建的所有用户帐户。 用户帐户可以直接针对 Azure AD DS 托管域进行身份验证，例如登录到加入域的 VM。 当用户林可以同步密码哈希，并且用户不使用专用登录方法（如智能卡身份验证）时，用户林将起作用。

在 Azure AD DS*资源*林中，用户通过本地 AD DS 的单向林*信任*进行身份验证。 使用此方法，用户对象和密码哈希不会同步到 Azure AD DS。 用户对象和凭据仅存在于本地 AD DS 中。 此方法允许企业在 Azure 中托管依赖于经典身份验证（如 LDAPS、Kerberos 或 NTLM）的资源和应用程序平台，但删除了任何身份验证问题或疑虑。 Azure AD DS 资源林目前为预览版。

有关 Azure AD DS 中的林类型的详细信息，请参阅[什么是资源林？][concepts-forest]和[在 Azure AD DS 中，林信任如何工作？][concepts-trust]

## <a name="azure-ad-ds-skus"></a>Azure AD DS SKU

在 Azure AD DS 中，可用的性能和功能基于 SKU。 创建托管域时选择 SKU，并且可以在部署托管域后业务需求发生变化时切换 SKU。 下表概述了可用的 SKU 及其之间的差异：

| SKU 名称   | 最大对象计数 | 备份频率 | 出站林信任的最大数量 |
|------------|----------------------|------------------|----|
| Standard   | 无限制            | 每7天     | 0  |
| Enterprise | 无限制            | 每3天     | 5  |
| Premium    | 无限制            | 每日            | 10 |

在这些 Azure AD DS SKU 之前，使用了基于 Azure AD DS 托管域中对象数（用户和计算机帐户）的计费模型。 不再根据托管域中的对象数进行可变定价。

有关详细信息，请参阅 Azure [AD DS 定价页][pricing]。

### <a name="managed-domain-performance"></a>托管域性能

域性能因应用程序实现身份验证的方式而异。 其他计算资源可能有助于缩短查询响应时间并减少同步操作所花费的时间。 随着 SKU 级别的增加，托管域可用的计算资源也会增加。 监视应用程序的性能并规划所需的资源。

如果业务或应用程序需要更改，并且 Azure AD DS 托管域需要额外的计算能力，则可以切换到其他 SKU。

### <a name="backup-frequency"></a>备份频率

备份频率确定获取托管域快照的频率。 备份是由 Azure 平台管理的自动化过程。 如果托管域出现问题，Azure 支持可以帮助您从备份中还原。 由于同步仅*从*Azure AD 单向发生，因此 Azure AD DS 托管域中的任何问题都不会影响 Azure AD 或本地 AD DS 环境和功能。

随着 SKU 级别的增加，这些备份快照的频率也会增加。 查看您的业务要求和恢复点目标 （RPO），以确定托管域所需的备份频率。 如果业务或应用程序要求发生变化，并且需要更频繁的备份，则可以切换到其他 SKU。

### <a name="outbound-forests"></a>出站林

上一节详细介绍了从 Azure AD DS 托管域到本地 AD DS 环境（当前处于预览状态）的单向出站林信任。 SKU 确定可以为 Azure AD DS 托管域创建的最大林信任数。 查看业务和应用程序要求以确定实际需要的信任数，并选择相应的 Azure AD DS SKU。 同样，如果您的业务需求发生变化，并且需要创建其他林信任，则可以切换到其他 SKU。

## <a name="next-steps"></a>后续步骤

要开始，[请创建 Azure AD DS 托管域][create-instance]。

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
