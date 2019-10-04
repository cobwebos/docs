---
title: 排查 Azure AD 域服务中的帐户锁定问题 |Microsoft Docs
description: 了解如何对导致用户帐户在 Azure Active Directory 域服务中被锁定的常见问题进行故障排除。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 29789f299f266c86d719d56cfbf8e262907f7264
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827075"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>排查 Azure AD 域服务托管域的帐户锁定问题

为防止重复的恶意登录尝试，Azure AD DS 在定义的阈值后锁定帐户。 此帐户锁定也可能在没有登录攻击事件的情况下发生。 例如，如果用户重复输入错误的密码或服务尝试使用旧密码，则该帐户将被锁定。

此故障排除一文概述了发生帐户锁定的原因，以及如何配置行为，以及如何查看安全审核来排查锁定事件。

## <a name="what-is-an-account-lockout"></a>什么是帐户锁定？

如果满足未成功登录尝试的规定阈值，则会锁定 Azure AD DS 中的用户帐户。 此帐户锁定行为旨在防止你重复多次尝试登录，这可能表示自动数字攻击。

**默认情况下，如果2分钟内有5个错误的密码尝试，则该帐户将被锁定30分钟。**

使用细化密码策略配置默认的帐户锁定阈值。 如果有一组特定的要求，则可以重写这些默认的帐户锁定阈值。 但是，不建议增加阈值限制来尝试减少帐户锁定次数。 首先排查帐户锁定行为的问题。

### <a name="fine-grained-password-policy"></a>细化密码策略

细化密码策略（Fgpp）允许您将对密码和帐户锁定策略的特定限制应用于域中的不同用户。 FGPP 仅影响在 Azure AD DS 中创建的用户。 从 Azure AD 同步到 Azure AD DS 托管域的云用户和域用户不受密码策略影响。

策略通过 Azure AD DS 托管域中的组关联进行分发，你所做的任何更改将在下次用户登录时应用。 更改策略不会解除对已锁定的用户帐户的锁定。

有关细化密码策略的详细信息，请参阅[配置密码和帐户锁定策略][configure-fgpp]。

## <a name="common-account-lockout-reasons"></a>常见帐户锁定原因

帐户被锁定的最常见原因是没有任何恶意意向或因素，包括以下方案：

* **用户已自行锁定。**
    * 最近密码更改后，用户是否继续使用以前的密码？ 默认的帐户锁定策略在2分钟内尝试失败的次数为5次，因为用户意外重试旧密码。
* **存在具有旧密码的应用程序或服务。**
    * 如果应用程序或服务使用某个帐户，这些资源可能会使用旧密码重复尝试登录。 此行为将导致帐户被锁定。
    * 尝试最大程度地减少跨多个不同应用程序或服务使用的帐户，并记录使用凭据的位置。 如果更改了帐户密码，请相应地更新关联的应用程序或服务。
* **密码在不同的环境中已更改，新密码尚未同步。**
    * 如果帐户密码在 Azure AD DS 之外（如本地 AD DS 环境中）发生更改，则密码更改可能需要几分钟时间才能通过 Azure AD 和 Azure AD DS 同步。
    * 在完成密码同步过程之前，尝试通过 Azure AD DS 登录到资源的用户会导致其帐户被锁定。

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>排除安全审核帐户锁定问题

若要解决帐户锁定事件发生的时间以及这些事件的来源，请[为 AZURE AD DS 启用安全审核（当前为预览版）][security-audit-events]。 仅在启用该功能时捕获审核事件。 理想情况下，应该先启用安全审核，*然后再*进行故障排除。 如果用户帐户重复出现锁定问题，你可以在下一次出现此情况时启用安全审核。

启用安全审核后，以下示例查询将演示如何查看*帐户锁定事件*，代码*4740*。

查看过去7天的所有帐户锁定事件：

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

查看最近7天内名为*driley*的帐户的所有帐户锁定事件。

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

查看2019年6月26日之间的所有帐户锁定事件。 和2019年7月1日午夜按日期和时间升序排序：

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>后续步骤

有关细化密码策略以调整帐户锁定阈值的详细信息，请参阅[配置密码和帐户锁定策略][configure-fgpp]。

如果在将 VM 加入到 Azure AD DS 托管域时仍出现问题，请[找到 "帮助" 并为 Azure Active Directory 打开支持票证][azure-ad-support]。

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
