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
ms.date: 10/08/2019
ms.author: iainfou
ms.openlocfilehash: f239bab48e732755361fe734fdc24b37d3823c63
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481009"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Azure Active Directory 域服务中的用户帐户、密码和管理的管理概念

当你创建并运行 Azure Active Directory 域服务（AD DS）托管域时，与传统的本地 AD DS 环境相比，行为存在一些差异。 在 Azure AD DS 中使用的管理工具与自行托管域相同，但不能直接访问域控制器（DC）。 根据用户帐户创建的源，密码策略和密码哈希的行为也存在一些差异。

此概念文章详细介绍了如何管理 Azure AD DS 托管域和用户帐户的不同行为，具体取决于创建的方式。

## <a name="domain-management"></a>域管理

在 Azure AD DS 中，包含所有资源（如用户和组、凭据和策略）的域控制器（Dc）都是托管服务的一部分。 为实现冗余，会创建两个 Dc 作为 Azure AD DS 托管域的一部分。 你无法登录到这些 Dc 来执行管理任务。 相反，你创建一个加入到 Azure AD DS 托管域的管理 VM，然后安装常规 AD DS 管理工具。 例如，可以使用 Active Directory 管理中心或 Microsoft 管理控制台（MMC）管理单元（如 DNS 或组策略对象）。

## <a name="user-account-creation"></a>用户帐户创建

可以通过多种方式在 Azure AD DS 中创建用户帐户。 大多数用户帐户是在中从 Azure AD 同步的，也可能包括从本地 AD DS 环境同步的用户帐户。 你还可以在 Azure AD DS 中直接手动创建帐户。 某些功能（如初始密码同步或密码策略）的行为方式不同，具体取决于创建用户帐户的方式和位置。

* 可以从 Azure AD 同步用户帐户。 这包括直接在 Azure AD 中创建的仅限云的用户帐户，以及使用 Azure AD Connect 从本地 AD DS 环境同步的混合用户帐户。
    * Azure AD DS 中的大部分用户帐户是通过 Azure AD 中的同步过程创建的。
* 可以在 Azure AD DS 托管域中手动创建用户帐户，并且 Azure AD 中不存在该帐户。
    * 如果需要为仅在 Azure AD DS 中运行的应用程序创建服务帐户，你可以在托管域中手动创建它们。 由于同步是 Azure AD 单向的，因此在 Azure AD DS 中创建的用户帐户不会同步回 Azure AD。

## <a name="password-policy"></a>密码策略

Azure AD DS 包括一个默认密码策略，该策略定义帐户锁定、最长密码期限和密码复杂性等设置。 帐户锁定策略的设置适用于 Azure AD DS 中的所有用户，而不考虑上一节中所述的用户创建方式。 一些设置（如最小密码长度和密码复杂性）仅适用于直接在 Azure AD DS 中创建的用户。

你可以创建自己的自定义密码策略来覆盖 Azure AD DS 中的默认策略。 然后，可以根据需要将这些自定义策略应用到特定的用户组。

有关如何根据用户创建的源应用密码策略的详细信息，请参阅[托管域上的密码和帐户锁定策略][password-policy]。

## <a name="password-hashes"></a>密码哈希

若要对托管域上的用户进行身份验证，Azure AD DS 需要密码哈希，其格式适用于 NT LAN Manager (NTLM) 和 Kerberos 身份验证。 除非为租户启用了 Azure AD DS，否则 Azure AD 不会以 NTLM 或 Kerberos 身份验证所需的格式生成或存储密码哈希。 出于安全考虑，Azure AD 也不以明文形式存储任何密码凭据。 因此，Azure AD 无法基于用户的现有凭据自动生成这些 NTLM 或 Kerberos 密码哈希。

对于仅限云的用户帐户，用户必须更改其密码才能使用 Azure AD DS。 此密码更改过程会导致在 Azure AD 中生成并存储用于 Kerberos 和 NTLM 身份验证的密码哈希。

对于使用 Azure AD Connect 从本地 AD DS 环境同步的用户，[启用密码哈希同步][hybrid-phs]。

> [!IMPORTANT]
> 仅当你为 Azure AD 租户启用 Azure AD DS 时，Azure AD Connect 才同步旧密码哈希。 如果仅使用 Azure AD Connect 将本地 AD DS 环境与 Azure AD 同步，则不使用旧密码哈希。
>
> 如果旧版应用程序不使用 NTLM 身份验证或 LDAP 简单绑定，则建议你禁用 Azure AD DS 的 NTLM 密码哈希同步。 有关详细信息，请参阅[禁用弱密码套件和 NTLM 凭据哈希同步][secure-domain]。

经过适当的配置后，可用的密码哈希将存储在 Azure AD DS 托管域中。 删除 Azure AD DS 托管域也会删除其中存储的所有密码哈希。 如果之后创建 Azure AD DS 托管域，Azure AD 中的同步凭据信息将无法重复使用-你必须重新配置密码哈希同步，以再次存储密码哈希。 以前加入域的 VM 或用户无法立即进行身份验证 - Azure AD 需要在新的 Azure AD DS 托管域中生成并存储密码哈希。 有关详细信息，请参阅 [Azure AD DS 和 Azure AD Connect 的密码哈希同步过程][azure-ad-password-sync]。

> [!IMPORTANT]
> 只应安装并配置 Azure AD Connect 以便与本地 AD DS 环境同步。 不支持在 Azure AD DS 托管域中安装 Azure AD Connect，以将对象同步回 Azure AD。

## <a name="forests-and-trusts"></a>林和信任

*林*是 Active Directory 域服务（AD DS）用于对一个或多个*域*进行分组的逻辑构造。 然后，域存储用户或组的对象，并提供身份验证服务。

在 Azure AD DS 中，林只包含一个域。 本地 AD DS 林通常包含多个域。 在大型组织中，特别是合并和收购后，最终可能会得到多个本地林，每个林都包含多个域。

默认情况下，将 Azure AD DS 托管域创建为*用户*林。 这种类型的林将同步 Azure AD 中的所有对象，包括在本地 AD DS 环境中创建的任何用户帐户。 用户帐户可以直接针对 Azure AD DS 托管域进行身份验证，例如登录到已加入域的 VM。 用户林可用于同步密码哈希，并且用户不使用类似于智能卡身份验证的专用登录方法。

在 Azure AD DS*资源*林中，用户从本地 AD DS 对单向林*信任*进行身份验证。 采用此方法时，用户对象和密码哈希不会同步到 Azure AD DS。 仅本地 AD DS 中存在用户对象和凭据。 此方法允许企业在 Azure 中托管依赖于经典身份验证（例如 LDAPS、Kerberos 或 NTLM）的资源和应用程序平台，但会删除任何身份验证问题或问题。 Azure AD DS 资源林目前处于预览阶段。

有关 Azure AD DS 中的林类型的详细信息，请参阅[什么是资源林？][concepts-forest]和[林信任如何在 Azure AD DS 中工作？][concepts-trust]

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
