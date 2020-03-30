---
title: Azure AD 域服务中的疑难解答登录问题 |微软文档
description: 了解如何在 Azure 活动目录域服务中解决常见用户登录问题和错误。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 0585ced3bc53f216ab203b4686b5800b5e14bbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612742"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>解决 Azure AD 域服务托管域的帐户登录问题

用户帐户无法登录到 Azure AD DS 托管域的最常见原因包括以下方案：

* [该帐户尚未同步到 Azure AD DS。](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS 没有密码哈希，无法让帐户登录。](#azure-ad-ds-doesnt-have-the-password-hashes)
* [帐户已锁定。](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS 无法在 Azure AD 租户外部的帐户的凭据中同步。 外部用户无法登录到 Azure AD DS 托管域。

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>帐户尚未同步到 Azure AD DS

根据目录的大小，用户帐户和凭据哈希可能需要一段时间才能在 Azure AD DS 中可用。 对于大型目录，来自 Azure AD 的初始单向同步可能需要几个小时，最多需要一两天。 在重试身份验证之前，请确保等待足够长的时间。

对于用户 Azure AD Connect 将本地目录数据同步到 Azure AD 的混合环境，请确保运行最新版本的 Azure AD 连接，并[配置 Azure AD 连接以在启用 Azure AD DS 后执行完全同步][azure-ad-connect-phs]。 如果禁用 Azure AD DS，然后重新启用，则必须再次执行这些步骤。

如果帐户继续出现问题，无法通过 Azure AD 连接同步，请重新启动 Azure AD 同步服务。 从安装了 Azure AD 连接的计算机中打开命令提示窗口并运行以下命令：

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS 没有密码哈希

除非为租户启用了 Azure AD DS，否则 Azure AD 不会以 NTLM 或 Kerberos 身份验证所需的格式生成或存储密码哈希。 出于安全考虑，Azure AD 也不以明文形式存储任何密码凭据。 因此，Azure AD 无法基于用户的现有凭据自动生成这些 NTLM 或 Kerberos 密码哈希。

### <a name="hybrid-environments-with-on-premises-synchronization"></a>具有本地同步的混合环境

对于使用 Azure AD Connect 从本地 AD DS 环境进行同步的混合环境，可以在本地生成所需的 NTLM 或 Kerberos 密码哈希到 Azure AD 中。 创建 Azure AD DS 托管域后，[启用对 Azure 活动目录域服务的密码哈希同步][azure-ad-connect-phs]。 如果不完成此密码哈希同步步骤，就无法使用 Azure AD DS 登录到帐户。 如果禁用 Azure AD DS，然后重新启用，则必须再次执行这些步骤。

有关详细信息，请参阅[密码哈希同步如何适用于 Azure AD DS][phs-process]。

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>无本地同步的仅云环境

Azure AD DS 托管域没有本地同步（仅 Azure AD 中的帐户）还需要生成所需的 NTLM 或 Kerberos 密码哈希。 如果仅云帐户无法登录，则在启用 Azure AD DS 后，是否成功完成了帐户的密码更改过程？

* **否，密码未更改。**
    * [更改帐户的密码][enable-user-accounts]以生成所需的密码哈希，然后等待 15 分钟，然后再次尝试登录。
    * 如果禁用 Azure AD DS 然后重新启用，则每个帐户必须再次按照这些步骤更改其密码并生成所需的密码哈希。
* **是，密码已更改。**
    * 尝试使用*UPN*格式（如`driley@aaddscontoso.com`）而不是*SAMAccountName*格式（如`AADDSCONTOSO\deeriley`）登录。
    * 对于 UPN 前缀过长或与托管域上的其他用户相同的用户，可以自动生成*SAMAccountName。* *UPN*格式保证在 Azure AD 租户中是唯一的。

## <a name="the-account-is-locked-out"></a>帐户被锁定

当满足未成功登录尝试的已定义阈值时，Azure AD DS 中的用户帐户将锁定。 此帐户锁定行为旨在保护您免受可能指示自动数字攻击的重复暴力强制登录尝试的影响。

默认情况下，如果在 2 分钟内尝试了 5 次错误密码，则帐户将锁定 30 分钟。

有关详细信息以及如何解决帐户锁定问题，请参阅 Azure AD [DS 中的帐户锁定问题故障排除][troubleshoot-account-lockout]。

## <a name="next-steps"></a>后续步骤

如果仍难以将 VM 加入 Azure AD DS 托管域，[请查找帮助并打开 Azure 活动目录的支持票证][azure-ad-support]。

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
