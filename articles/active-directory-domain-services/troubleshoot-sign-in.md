---
title: 排查 Azure AD 域服务中的登录问题 | Microsoft Docs
description: 了解如何排查 Azure Active Directory 域服务中的常见用户登录问题和错误。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 91c42e391d1a23e9ac494af9271c92e3e9b5d6c4
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722647"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>排查 Azure Active Directory 域服务托管域的帐户登录问题

用户帐户无法登录到 Azure Active Directory 域服务 (Azure AD DS) 托管域的最常见原因包括以下情形：

* [该帐户尚未同步到 Azure AD DS 中。](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS 不具有允许该帐户登录的密码哈希。](#azure-ad-ds-doesnt-have-the-password-hashes)
* [该帐户已被锁定。](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS 无法使用 Azure AD 租户之外帐户的凭据进行同步。 外部用户无法登录到 Azure AD DS 托管域。

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>帐户尚未同步到 Azure AD DS 中

根据目录的大小，可能需要一段时间之后才可在托管域中使用用户帐户和凭据哈希。 对于大型目录，从 Azure AD 进行这样一次初始单向同步可能需要几个小时，最多可能会需要一两天时间。 请确保重试身份验证之前等待足够长的时间。

对于使用 Azure AD Connect 将本地目录数据同步到 Azure AD 中的混合环境，请确保运行最新版本的 Azure AD Connect 并且已[将 Azure AD Connect 配置为在启用 Azure AD DS 之后执行完全同步][azure-ad-connect-phs]。 如果禁用 Azure AD DS 后再重新启用，则必须再次执行这些步骤。

如果未通过 Azure AD Connect 同步的帐户仍然存在问题，请重启 Azure AD Sync 服务。 在安装了 Azure AD Connect 的计算机上，打开命令提示符窗口，然后运行以下命令：

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS 没有密码哈希

除非为租户启用了 Azure AD DS，否则 Azure AD 不会以 NTLM 或 Kerberos 身份验证所需的格式生成或存储密码哈希。 出于安全考虑，Azure AD 也不以明文形式存储任何密码凭据。 因此，Azure AD 无法基于用户的现有凭据自动生成这些 NTLM 或 Kerberos 密码哈希。

### <a name="hybrid-environments-with-on-premises-synchronization"></a>使用本地同步的混合环境

对于使用 Azure AD Connect 从本地 AD DS 环境同步的混合环境，可以在本地生成所需 NTLM 或 Kerberos 密码哈希并将其同步到 Azure AD 中。 在创建托管域后，[针对 Azure Active Directory 域服务启用密码哈希同步][azure-ad-connect-phs]。 如果没有完成此密码哈希同步步骤，则无法使用托管域登录到帐户。 如果禁用 Azure AD DS 后再重新启用，必须再次执行那些步骤。

有关详细信息，请参阅[如何针对 Azure AD DS 进行密码哈希同步][phs-process]。

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>不使用本地同步的“仅限云”环境

不使用本地同步的托管域（仅限 Azure AD 中的帐户）也需要生成所需的 NTLM 或 Kerberos 密码哈希。 如果“仅限云”帐户无法登录，是否在启用 Azure AD DS 后成功完成了该帐户的密码更改过程？

* **否，尚未更改密码。**
    * 请[更改该帐户的密码][enable-user-accounts]以生成所需密码哈希，然后等待 15 分钟后再次尝试登录。
    * 如果禁用 Azure AD DS 后重新启用，每个帐户都必须再次执行这些步骤，以更改其密码并生成所需密码哈希。
* **是，已更改了密码。**
    * 请尝试使用 `driley@aaddscontoso.com` 等 UPN 格式（而不是 `AADDSCONTOSO\deeriley` 等 SAMAccountName 格式 ）登录。
    * 对于其 UPN 前缀过长或与托管域上另一用户相同的用户，系统可能会自动生成 SAMAccountName。 UPN 格式在 Azure AD 租户中保证是唯一的。

## <a name="the-account-is-locked-out"></a>该帐户已被锁定

达到为登录尝试失败定义的阈值时，会锁定托管域中的用户帐户。 此帐户锁定行为的设计意图是为了防止反复尝试暴力登录，而反复尝试暴力登录可能表示存在自动化数字攻击。

默认情况下，如果 2 分钟内有 5 次错误的密码尝试，该帐户就会被锁定 30 分钟。

有关详细信息以及如何解决帐户锁定问题，请参阅[排查 Azure AD DS 中的帐户锁定问题][troubleshoot-account-lockout]。

## <a name="next-steps"></a>后续步骤

如果在将 VM 加入托管域时仍有问题，请[查找帮助，并为 Azure Active Directory 创建支持票证][azure-ad-support]。

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: ./tutorial-configure-password-hash-sync.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md