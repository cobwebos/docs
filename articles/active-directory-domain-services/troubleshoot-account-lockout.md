---
title: 在 Azure AD 域服务中排除帐户锁定故障 |微软文档
description: 了解如何解决导致用户帐户在 Azure 活动目录域服务中锁定的常见问题。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 2e274aa353f6c3e485ae10a6a67ee2940eb88b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246306"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>解决 Azure AD 域服务托管域的帐户锁定问题

为了防止重复的恶意登录尝试，Azure AD DS 会在定义的阈值后锁定帐户。 此帐户锁定也可能意外发生，而不会发生登录攻击事件。 例如，如果用户反复输入错误的密码或服务尝试使用旧密码，则帐户将锁定。

此故障排除文章概述了帐户锁定发生的原因以及如何配置行为，以及如何查看安全审核以排除锁定事件。

## <a name="what-is-an-account-lockout"></a>什么是帐户锁定？

当满足未成功登录尝试的已定义阈值时，Azure AD DS 中的用户帐户将锁定。 此帐户锁定行为旨在保护您免受可能指示自动数字攻击的重复暴力强制登录尝试的影响。

**默认情况下，如果在 2 分钟内尝试了 5 次错误密码，则帐户将锁定 30 分钟。**

使用细粒度密码策略配置默认帐户锁定阈值。 如果您有一组特定的要求，则可以覆盖这些默认帐户锁定阈值。 但是，不建议增加阈值限制以尝试减少帐户锁定的数量。 首先排除帐户锁定行为的来源。

### <a name="fine-grained-password-policy"></a>严格的密码策略

细粒度密码策略 （FGP） 允许您对域中的不同用户应用密码和帐户锁定策略的特定限制。 FGPP 仅影响在 Azure AD DS 中创建的用户。 从 Azure AD 同步到 Azure AD 托管域的云用户和域用户不受密码策略的影响。

策略通过 Azure AD DS 托管域中的组关联分发，所做的任何更改都应用于下一个用户登录。 更改策略不会解锁已锁定的用户帐户。

有关细粒度密码策略的详细信息，请参阅[配置密码和帐户锁定策略][configure-fgpp]。

## <a name="common-account-lockout-reasons"></a>常见帐户锁定原因

在没有任何恶意或因素的情况下，将帐户锁定的最常见原因包括以下方案：

* **用户把自己锁在门外。**
    * 最近更改密码后，用户继续使用以前的密码吗？ 默认帐户锁定策略在 2 分钟内尝试 5 次失败，可能是由于用户无意中重试旧密码造成的。
* **有一个应用程序或服务具有旧密码。**
    * 如果应用程序或服务使用帐户，这些资源可能会反复尝试使用旧密码登录。 此行为会导致帐户被锁定。
    * 尝试最小化跨多个不同应用程序或服务的帐户使用，并记录凭据的使用位置。 如果帐户密码已更改，请相应地更新关联的应用程序或服务。
* **密码已在不同的环境中更改，并且新密码尚未同步。**
    * 如果在 Azure AD DS 之外（如在 AD DS 环境中）更改了帐户密码，则密码更改可能需要几分钟才能通过 Azure AD 同步并同步到 Azure AD DS。
    * 尝试在密码同步过程完成之前通过 Azure AD DS 登录到资源的用户会导致其帐户被锁定。

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>通过安全审核解决帐户锁定问题

要在帐户锁定事件发生时和它们来自何处进行故障排除，[请为 Azure AD DS 启用安全审核][security-audit-events]。 审核事件仅从启用该功能时捕获。 理想情况下，您应该在出现要排除帐户锁定问题*之前*启用安全审核。 如果用户帐户反复出现锁定问题，则可以为下次发生这种情况启用安全审核。

启用安全审核后，以下示例查询将向您展示如何查看*帐户锁定事件*，代码*4740*。

查看过去七天的所有帐户锁定事件：

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

查看名为*driley*的帐户过去七天的所有帐户锁定事件。

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

查看 2019 年 6 月 26 日上午 9 点的所有帐户锁定事件。 和 2019 年 7 月 1 日午夜，按日期和时间排序：

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>后续步骤

有关调整帐户锁定阈值的细粒度密码策略的详细信息，请参阅[配置密码和帐户锁定策略][configure-fgpp]。

如果仍难以将 VM 加入 Azure AD DS 托管域，[请查找帮助并打开 Azure 活动目录的支持票证][azure-ad-support]。

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
