---
title: 管理对资源和数据的威胁
titleSuffix: Azure AD B2C
description: 了解 Azure Active Directory B2C 中用于拒绝服务攻击和密码攻击的检测和缓解技术。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b236dba9b682bc4d62ef8cfc8b95e67370b3e0ea
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947734"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中管理对资源和数据的威胁

Azure Active Directory B2C （Azure AD B2C）提供内置功能，可帮助防止对资源和数据的威胁。 这些威胁包括拒绝服务攻击和密码攻击。 拒绝服务攻击可能会使目标用户无法使用资源。 密码攻击会导致未经授权的资源访问。

## <a name="denial-of-service-attacks"></a>拒绝服务攻击

Azure AD B2C 使用 SYN cookie 防御 SYN 洪流攻击。 Azure AD B2C 还使用速率和连接限制防止拒绝服务攻击。

## <a name="password-attacks"></a>密码攻击

要求用户所设密码的复杂性合理。 Azure AD B2C 针对密码攻击实施了缓解技术。 缓解措施包括检测暴力破解密码攻击和字典密码攻击。 Azure AD B2C 使用各种信号分析请求的完整性。 Azure AD B2C 旨在智能地将目标用户与黑客和僵尸网络区分开来。

Azure AD B2C 使用复杂策略来锁定帐户。 将根据请求的 IP 和输入的密码锁定帐户。 锁定的持续时间也会根据存在攻击的可能性而延长。 密码尝试成功10次（默认尝试阈值）后，会发生一分钟的锁定。 下一次登录失败后，当帐户解锁后（即，在锁定期到期后，服务已自动解锁该帐户之后），会发生另一分钟的锁定，并为每个失败的登录继续进行。 重复输入相同的密码不会计为多次不成功登录。

前 10 个锁定期限的长度为一分钟。 接下来的 10 个锁定期限时间稍长，并且每 10 个锁定期限后都会增加持续时间。 当帐户未锁定时，锁定计数器在成功登录后重置为零。 锁定期限可以持续长达五个小时。

## <a name="manage-password-protection-settings"></a>管理密码保护设置

管理密码保护设置，包括锁定阈值：

1. 登录到 [Azure 门户](https://portal.azure.com)
1. 使用顶部菜单中的 "**目录 + 订阅**" 筛选器选择包含 Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 在 "**安全性**" 下，选择 "**身份验证方法（预览版）** "，然后选择 "**密码保护**"。
1. 输入所需的密码保护设置，然后选择 "**保存**"。

    Azure AD 设置](media/active-directory-b2c-reference-threat-management/portal-02-password-protection.png)
   中 ![Azure 门户密码保护 "页  <br />*在**密码保护**设置中将锁定阈值设置为 5*。

## <a name="view-locked-out-accounts"></a>查看锁定的帐户

若要获取有关锁定的帐户的信息，可以查看 Active Directory[登录活动报告](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)。 在 "**状态**" 下，选择 "**失败**"。 登录尝试失败，**登录错误代码**`50053` 表示锁定的帐户：

![显示锁定帐户的 Azure AD 登录报表部分](media/active-directory-b2c-reference-threat-management/portal-01-locked-account.png)

若要了解如何在 Azure Active Directory 中查看登录活动报表，请参阅[登录活动报告错误代码](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)。
