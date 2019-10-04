---
title: 排查 Azure AD 域服务中的登录问题 |Microsoft Docs
description: 了解如何排查 Azure Active Directory 域服务中的常见用户登录问题和错误。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: aa03e388019bf696324ea7af6062ec98386df5fa
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827053"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>排查 Azure AD 域服务托管域中的帐户登录问题

无法登录到 Azure AD DS 托管域的用户帐户最常见的原因包括以下方案：

* [帐户尚未同步到 Azure AD DS。](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS 没有允许帐户登录的密码哈希。](#azure-ad-ds-doesnt-have-the-password-hashes)
* [帐户已锁定。](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS 无法在 Azure AD 租户外部的帐户的凭据中同步。 外部用户无法登录到 Azure AD DS 托管域。

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>帐户尚未同步到 Azure AD DS

根据目录的大小，可能需要一段时间，用户帐户和凭据哈希在 Azure AD DS 中可用。 对于大型目录，此初始单向 Azure AD 同步可能需要几个小时，并且最多可能需要一天或两天的时间。 请确保等待足够长的时间，然后重试身份验证。

对于用户 Azure AD Connect 将本地目录数据同步到 Azure AD 的混合环境，请确保运行最新版本的 Azure AD Connect，并已将[Azure AD Connect 配置为在启用 Azure 后执行完全同步AD DS][azure-ad-connect-phs]。 如果禁用 Azure AD DS 然后重新启用，则必须再次执行这些步骤。

如果通过 Azure AD Connect 仍无法同步帐户的问题，请重新启动 Azure AD Sync 服务。 在安装了 Azure AD Connect 的计算机上，打开命令提示符窗口，并运行以下命令：

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS 没有密码哈希

除非为租户启用了 Azure AD DS，否则 Azure AD 不会以 NTLM 或 Kerberos 身份验证所需的格式生成或存储密码哈希。 出于安全考虑，Azure AD 也不以明文形式存储任何密码凭据。 因此，Azure AD 无法基于用户的现有凭据自动生成这些 NTLM 或 Kerberos 密码哈希。

### <a name="hybrid-environments-with-on-premises-synchronization"></a>本地同步的混合环境

对于使用 Azure AD Connect 从本地 AD DS 环境同步的混合环境，你可以在本地生成所需的 NTLM 或 Kerberos 密码哈希，并将其同步到 Azure AD 中。 创建 Azure AD DS 托管域后，[启用 Azure Active Directory 域服务的密码哈希同步][azure-ad-connect-phs]。 如果没有完成此密码哈希同步步骤，则无法使用 Azure AD DS 登录到帐户。 如果禁用 Azure AD DS 然后重新启用，则必须再次执行这些步骤。

有关详细信息，请参阅[AZURE AD DS 的密码哈希同步的工作原理][phs-process]。

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>仅限云的环境，无本地同步

Azure AD DS 托管域，无需本地同步，只有 Azure AD 中的帐户也需要生成所需的 NTLM 或 Kerberos 密码哈希。 如果仅限云的帐户无法登录，则在启用 Azure AD DS 后，是否已成功完成该帐户的密码更改过程？

* **不，密码尚未更改。**
    * [更改帐户的密码][enable-user-accounts]以生成所需的密码哈希，并等待15分钟，然后再尝试再次登录。
    * 如果禁用 Azure AD DS 然后重新启用，则每个帐户必须再次执行这些步骤以更改其密码并生成所需的密码哈希。
* **是，密码已更改。**
    * 尝试使用*UPN*格式（例如 `driley@contoso.com`，而不是*SAMAccountName*格式，如 `CONTOSO\deeriley`）登录。
    * 对于 UPN 前缀过长或与托管域上的其他用户相同的用户，可能会自动生成*SAMAccountName* 。 *UPN*格式保证在 Azure AD 租户中是唯一的。

## <a name="the-account-is-locked-out"></a>帐户已锁定

如果满足未成功登录尝试的规定阈值，则会锁定 Azure AD DS 中的用户帐户。 此帐户锁定行为旨在防止你重复多次尝试登录，这可能表示自动数字攻击。

默认情况下，如果2分钟内有5个错误的密码尝试，则该帐户将被锁定30分钟。

有关详细信息以及如何解决帐户锁定问题，请参阅[排查 AZURE AD DS 中的帐户锁定问题][troubleshoot-account-lockout]。

## <a name="next-steps"></a>后续步骤

如果在将 VM 加入到 Azure AD DS 托管域时仍出现问题，请[找到 "帮助" 并为 Azure Active Directory 打开支持票证][azure-ad-support]。

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
