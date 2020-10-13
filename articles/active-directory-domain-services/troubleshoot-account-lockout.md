---
title: 对 Azure AD 域服务中的帐户锁定进行故障排除 | Microsoft Docs
description: 了解如何对导致用户帐户在 Azure Active Directory 域服务中被锁定的常见问题进行故障排除。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 4a5ae321a4a97df5b5fa91bb239589c76c6601fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86039749"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>使用 Azure Active Directory 域服务托管域对帐户锁定问题进行故障排除

为防止重复的恶意登录尝试，Azure Active Directory 域服务 (Azure AD DS) 托管域会在定义的阈值后锁定帐户。 在没有登录攻击事件的情况下，也可能意外发生这种帐户锁定。 例如，如果用户重复输入错误的密码或服务尝试使用旧密码，则帐户将被锁定。

本文概述了为何会发生帐户锁定、如何配置行为，以及如何查看安全审核以对锁定事件进行故障排除。

## <a name="what-is-an-account-lockout"></a>什么是帐户锁定？

达到为登录尝试失败定义的阈值时，会锁定 Azure AD DS 托管域中的用户帐户。 这种帐户锁定行为旨在防止重复的暴力登录尝试（这种尝试可能指示了自动化数字攻击）。

**默认情况下，如果 2 分钟内有 5 次失败的密码尝试，将锁定帐户 30 分钟。**

使用细化密码策略配置默认的帐户锁定阈值。 如果你有一组特定的要求，可以替代这些默认的帐户锁定阈值。 但是，建议不要放松阈值限制来尝试减少帐户锁定次数。 首先对帐户锁定行为的来源进行故障排除。

### <a name="fine-grained-password-policy"></a>细化密码策略

通过细化密码策略 (FGPP)，可以对域中的不同用户应用特定的密码和帐户锁定策略限制。 FGPP 仅影响托管域中的用户。 从 Azure AD 同步到托管域的云用户和域用户仅受托管域内的密码策略影响。 其在 Azure AD 或本地目录中的帐户不受影响。

通过托管域中的组关联来分发策略，并在下次用户登录时应用你的任何更改。 更改策略不会解除锁定已锁定的用户帐户。

有关细化密码策略的详细信息，以及直接在 Azure AD DS 中创建的用户与从 Azure AD 中同步的用户之间的差异，请参阅[配置密码和帐户锁定策略][configure-fgpp]。

## <a name="common-account-lockout-reasons"></a>常见帐户锁定原因

在没有任何恶意意图或因素的情况下，锁定帐户的最常见原因包括以下情况：

* **用户使自己被锁定。**
    * 最近密码更改后，用户是否继续使用以前的密码？ 用户无意中重试旧密码，可能会触发默认帐户锁定策略：2 分钟内有 5 次失败的尝试即会锁定帐户。
* **存在具有旧密码的应用程序或服务。**
    * 如果应用程序或服务使用某个帐户，这些资源可能会使用旧密码重复尝试登录。 此行为将导致帐户被锁定。
    * 尝试最大程度地减少跨多个不同应用程序或服务使用同一帐户，并记录使用凭据的位置。 如果更改了帐户密码，请相应地更新关联的应用程序或服务。
* **在其他环境中更改了密码，而新密码尚未同步。**
    * 如果在托管域之外（如本地 AD DS 环境中）更改了帐户密码，密码更改可能需要几分钟时间才能同步到 Azure AD 和托管域。
    * 如果有用户在密码同步过程完成之前尝试在托管域登录到资源，其帐户将被锁定。

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>通过安全审核对帐户锁定进行故障排除

若要对帐户锁定事件发生的时间以及这些事件的来源进行故障排除，请[为 Azure AD DS 启用安全审核][security-audit-events]。 仅从启用该功能时开始捕获审核事件。 理想情况下，应在有帐户锁定问题需要进行故障排除之前启用安全审核。 如果用户帐户重复出现锁定问题，可启用安全审核以防下次出现这种情况。

启用安全审核后，可查看以下示例查询，了解如何查看“帐户锁定事件”，代码“4740” 。

查看过去 7 天的所有帐户锁定事件：

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

查看名为“driley”的帐户在过去 7 天的所有帐户锁定事件。

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

查看发生在 2020 年 6 月 26 日上午 9 点和 2020 年 7 月 1 日午夜 期间的所有帐户锁定事件，按日期和时间升序排序：

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-26 09:00) and TimeGenerated <= datetime(2020-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>后续步骤

有关细化密码策略以调整帐户锁定阈值的详细信息，请参阅[配置密码和帐户锁定策略][configure-fgpp]。

如果在将 VM 加入托管域时仍有问题，请[查找帮助并创建 Azure Active Directory 的支持票证][azure-ad-support]。

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
