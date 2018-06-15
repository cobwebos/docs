---
title: Azure Active Directory 报告通知
description: 如何将 Azure Active Directory 报告通知用于可疑登录。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ae6d4b0e-5931-4cb3-98bf-9be91b381c92
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: compliance-reports
ms.date: 01/03/2018
ms.author: dhanyahk;rolyon
ms.custom: oldportal
ms.reviewer: dhanyahk
ms.openlocfilehash: 8f4beece9d1d351c91f2bb055eedc0ae9851f532
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588182"
---
# <a name="azure-active-directory-reporting-notifications"></a>Azure Active Directory 报告通知
## <a name="what-reports-generate-email-notifications"></a>哪些报告可生成电子邮件通知
此时，仅异常登录活动报告可触发电子邮件通知。

## <a name="what-is-an-irregular-sign-in"></a>什么是“异常登录”？
异常登录是指根据“不可能前往”条件与异常登录位置和设备，由机器学习算法标识的登录。 这可能指示黑客已在尝试使用此帐户登录。

## <a name="who-receives-the-email-notifications"></a>谁会收到电子邮件通知？
电子邮件将发送给已分配 Active Directory Premium 许可证的所有全局管理员。 要确保电子邮件送达，我们还将其发送到管理员的备用电子邮件地址。 管理员应将 aad-alerts-noreply@mail.windowsazure.com 包含在其安全发件人列表中，以便他们不会错过该电子邮件。

## <a name="how-often-are-these-emails-sent"></a>这些电子邮件的发送频率如何？
如果在过去 30 天内或自上一封电子邮件发送后发生 10 次新的异常登录活动（以时间较短者为准），将发送电子邮件。

## <a name="how-do-i-access-the-report-mentioned-in-the-email"></a>如何访问电子邮件中提到的报告？
单击该链接时，会重定向到 Azure 门户中的报告页。 要访问该报告，需要具备以下身份：

* Azure 订阅的管理员或共同管理员
* 目录中的全局管理员，并且已分配 Active Directory Premium 许可证。 有关详细信息，请参阅 [Azure Active Directory 版本](active-directory-whatis.md)。

## <a name="can-i-turn-off-these-emails"></a>是否可以关闭这些电子邮件？
是，要在 Azure 门户中关闭与异常登录相关的通知，请单击“配置”，并在“通知”部分下选择“已禁用”。

## <a name="whats-next"></a>后续步骤
* 想要知道可以使用哪些安全、审核和活动报告吗？ 请查看 [Azure AD 安全、审核和活动报告](active-directory-view-access-usage-reports.md)
* [Azure Active Directory 高级版入门](active-directory-get-started-premium.md)
* [向“登录”和“访问面板”页添加公司品牌](customize-branding.md)

