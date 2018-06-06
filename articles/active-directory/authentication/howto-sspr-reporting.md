---
title: 自助密码重置报告 - Azure Active Directory
description: 报告 Azure AD 自助密码重置事件
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 45183e51e9071647e07cfd0738522f0dfa28bd7b
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "33868996"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>用于 Azure AD 密码管理的报告选项

部署后，许多组织想要知道如何使用或者是否已真正使用自助密码重置 (SSPR)。 Azure Active Directory (Azure AD) 提供的报表功能可帮助使用预生成的报表来回答问题。 如果有相应的授权，还可以创建自定义查询。

![报告][Reporting]

[Azure 门户] (https://portal.azure.com/): 中提供的报表可解答以下问题：

> [!NOTE]
> 必须是[全局管理员](../active-directory-assign-admin-roles-azure-portal.md)，并且必须选择代表组织收集这些数据。 要做出此选择，必须至少访问一次“报告”选项卡或审核日志。 在此之前，不会为组织收集数据。
>

* 有多少人已注册了密码重置？
* 谁已经注册了密码重置？
* 人们都注册哪些数据？
* 有多少人在过去 7 天内重置了他们的密码？
* 用户或管理员用于重置其密码的最常见方法是什么？
* 用户或管理员尝试使用密码重置时面临的常见问题是什么？
* 哪些管理员经常重置其自己的密码？
* 密码重置时是否有任何可疑的活动？

## <a name="power-bi-content-pack"></a>Power BI 内容包

如果是 Power BI 用户，可以使用 Azure AD 的内容包，其中包含易用的 SSPR 报告。 有关如何使用和部署内容包的详细信息，请参阅[如何使用 Azure Active Directory Power BI 内容包](../active-directory-reporting-power-bi-content-pack-how-to.md)。 使用内容包，可以创建自己的仪表板并与组织中的其他人共享。

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>如何在 Azure 门户中查看密码管理报告

在 Azure 门户体验中，我们提供一种改进的方式用于查看密码重置和密码重置注册活动。 请使用以下步骤，查找密码重置和密码重置注册事件：

1. 浏览到 [Azure 门户](https://portal.azure.com)。
2. 在左侧窗格中，选择“所有服务”。
3. 在服务列表中搜索“Azure Active Directory”并选中它。
4. 选择“用户和组”。
5. 从“用户和组”菜单中选择“审核日志”。 此时会显示目录中所有用户发生的所有审核事件。 可以筛选此视图，查看所有与密码相关的事件。
6. 要筛选此视图以便仅查看密码重置的相关事件，请选择窗格顶部的“筛选”按钮。
7. 在“筛选”菜单中选择“类别”下拉列表，然后将其更改为“自助密码管理”类别类型。
8. （可选）通过选择所需的特定“活动”进一步筛选该列表。

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>如何通过 Azure AD 报告和事件 API 检索密码管理事件

Azure AD 报表和事件 API 支持检索密码重置和密码重置注册报表中包含的所有信息。 使用此 API，可以下载单个密码重置和密码重置注册事件，并将其与所选报表技术集成。

> [!IMPORTANT]
> 目前，Azure AD 报表和事件 API 最多可以检索 [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) 和 [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent) 类型的 75,000 个事件。 该 API 的检索范围为过去 30 天。
> 
> 如果需要检索或存储的数据超过此时间范围，建议使用该 API 来查询生成的差异数据，以便将数据保存在外部数据库中。 建议当开始在组织中使用 SSPR 时即开始检索此数据。 将其保留在外部，然后继续跟踪之后的差异数据。
>

### <a name="how-to-get-started-with-the-reporting-api"></a>如何开始使用报告 API

要访问此数据，需要编写一个小型应用程序或脚本，以便从我们的服务器检索这些数据。 有关详细信息，请参阅 [Azure AD 报表 API 入门](../active-directory-reporting-api-getting-started-azure-portal.md)。

编写有效的脚本后，请根据方案，检查可以检索的密码重置和注册事件：

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent)：列出密码重置事件可用的列。
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent)：列出密码重置注册事件可用的列。

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Azure 门户中报表列的说明

以下列表详细说明了 Azure 门户中的每个报表列：

* **用户**：尝试了密码重置注册操作的用户。
* **角色**：该用户在目录中的角色。
* **日期和时间**：尝试日期和时间。
* **已注册数据**：用户在密码重置注册期间提供的身份验证数据。

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Azure 门户中报表值的说明

下表说明了可为 Azure 门户的每一列设置的不同值：

| 列 | 允许值及其含义 |
| --- | --- |
| 已注册数据 |**备用电子邮件**：用户使用了备用电子邮件或身份验证电子邮件进行身份验证。<p><p>**办公电话**：用户使用了办公电话进行身份验证。<p>**移动电话**：用户使用了移动电话或身份验证电话进行身份验证。<p>**安全性问题**：用户使用了安全性问题进行身份验证。<p>**上述方法的任一组合（例如，备用电子邮件 + 移动电话）**：指定两项策略时发生，并显示用户使用哪两种方法对其密码重置请求进行身份验证。 |

## <a name="self-service-password-management-activity-types"></a>自助密码管理活动类型

“自助密码管理”审核事件类别中显示了以下活动类型：

* [被自助密码重置功能阻止](#activity-type-blocked-from-self-service-password-reset)：表示用户在 24 小时内尝试重置密码、使用特定的门限或验证某个电话号码总共超过 5 次。
* [更改密码(自助服务)](#activity-type-change-password-self-service)：表示用户执行了自愿性或强制性（由于密码过期）密码更改。
* [重置密码(由管理员)](#activity-type-reset-password-by-admin)：表示管理员通过 Azure 门户代表用户执行了密码重置。
* [重置密码(自助服务)](#activity-type-reset-password-self-service)：表示用户已成功通过 [Azure AD 密码重置门户](https://passwordreset.microsoftonline.com)重置其密码。
* [自助密码重置流活动进度](#activity-type-self-serve-password-reset-flow-activity-progress)：表示密码重置过程中用户执行的每个特定步骤（例如，传递特定的密码重置身份验证门限）。
* [解锁用户帐户(自助服务)](#activity-type-unlock-user-account-self-service)：表示用户在未通过 [Azure AD 密码重置门户](https://passwordreset.microsoftonline.com)重置其密码的情况下，使用无需重置的 Active Directory 帐户解锁功能成功解锁了其 Active Directory 帐户。
* [用户已注册自助密码重置](#activity-type-user-registered-for-self-service-password-reset)：表示用户已注册全部所需的信息，可以根据当前指定的租户密码重置策略重置其密码。

### <a name="activity-type-blocked-from-self-service-password-reset"></a>活动类型：被自助密码重置功能阻止

以下列表详细说明了此活动：

* **活动说明**：表示用户在 24 小时内尝试重置密码、使用特定的门限或验证某个电话号码总共超过 5 次。
* **活动参与者**：被限制执行其他重置操作的用户。 此用户可能是最终用户，也可能是管理员。
* **活动目标**：被限制执行其他重置操作的用户。 此用户可能是最终用户，也可能是管理员。
* **活动状态**：
  * 成功：表示某个用户在未来的 24 小时内被限制执行其他任何重置操作、尝试使用其他任何身份验证方法，或验证其他任何电话号码。
* **活动状态失败原因**：不适用。

### <a name="activity-type-change-password-self-service"></a>活动类型：更改密码(自助服务)

以下列表详细说明了此活动：

* **活动说明**：表示用户执行了自愿性或强制性（由于密码过期）密码更改。
* **活动参与者**：更改了其密码的用户。 此用户可能是最终用户，也可能是管理员。
* **活动目标**：更改了其密码的用户。 此用户可能是最终用户，也可能是管理员。
* **活动状态**：
  * 成功：表示用户已成功更改其密码。
  * 失败：表示用户未能更改其密码。 选择相应的行可查看“活动状态原因”类别，详细了解发生失败的原因。
* **活动状态失败原因**： 
  * FuzzyPolicyViolationInvalidPassword：由于 Microsoft 的“受禁密码检测”功能发现用户选择的某个密码过于常见或者太弱，因此已自动阻止该密码。

### <a name="activity-type-reset-password-by-admin"></a>活动类型：重置密码(由管理员)

以下列表详细说明了此活动：

* **活动说明**：表示管理员通过 Azure 门户代表用户执行了密码重置。
* **活动参与者**：代表其他最终用户或管理员执行了密码重置的管理员。 必须是全局管理员、密码管理员、用户管理员或支持管理员。
* **活动目标**：其密码被重置的用户。 此用户可能是最终用户，也可能是其他管理员。
* **活动状态**：
  * 成功：表示管理员已成功重置用户的密码。
  * 失败：表示管理员未能更改用户的密码。 选择相应的行可查看“活动状态原因”类别，详细了解发生失败的原因。

### <a name="activity-type-reset-password-self-service"></a>活动类型：重置密码(自助服务)

以下列表详细说明了此活动：

* **活动说明**：表示用户已成功通过 [Azure AD 密码重置门户](https://passwordreset.microsoftonline.com)重置其密码。
* **活动参与者**：重置了其密码的用户。 此用户可能是最终用户，也可能是管理员。
* **活动目标**：重置了其密码的用户。 此用户可能是最终用户，也可能是管理员。
* **活动状态**：
  * 成功：表示用户已成功重置其自己的密码。
  * 失败：表示用户未能重置其自己的密码。 选择相应的行可查看“活动状态原因”类别，详细了解发生失败的原因。
* **活动状态失败原因**： 
  * FuzzyPolicyViolationInvalidPassword：由于 Microsoft 的“受禁密码检测”功能发现管理员选择的某个密码过于常见或者太弱，因此已自动阻止该密码。

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>活动类型：自助密码重置流活动进度

以下列表详细说明了此活动：

* **活动说明**：表示密码重置过程中用户执行的每个特定步骤（例如，传递特定的密码重置身份验证门限）。
* **活动参与者**：执行了密码重置流的一部分步骤的用户。 此用户可能是最终用户，也可能是管理员。
* **活动目标**：执行了密码重置流的一部分步骤的用户。 此用户可能是最终用户，也可能是管理员。
* **活动状态**：
  * 成功：表示用户已成功完成密码重置流的特定步骤。
  * 失败：表示未能执行密码重置流的特定步骤。 选择相应的行可查看“活动状态原因”类别，详细了解发生失败的原因。
* **活动状态原因**：请查看下表，了解[所有允许的重置活动状态原因](#allowed-values-for-details-column)。

### <a name="activity-type-unlock-a-user-account-self-service"></a>活动类型：解锁用户帐户(自助服务)

以下列表详细说明了此活动：

* **活动说明**：表示用户在未通过 [Azure AD 密码重置门户](https://passwordreset.microsoftonline.com)重置其密码的情况下，使用无需重置的 Active Directory 帐户解锁功能成功解锁了其 Active Directory 帐户。
* **活动参与者**：在未重置其密码的情况下解锁了其帐户的用户。 此用户可能是最终用户，也可能是管理员。
* **活动目标**：在未重置其密码的情况下解锁了其帐户的用户。 此用户可能是最终用户，也可能是管理员。
* **允许的活动状态**：
  * 成功：表示用户已成功解锁其自己的帐户。
  * 失败：表示用户未能解锁其自己的帐户。 选择相应的行可查看“活动状态原因”类别，详细了解发生失败的原因。

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>活动类型：用户已注册自助密码重置

以下列表详细说明了此活动：

* **活动说明**：表示用户已注册全部所需的信息，可以根据当前指定的租户密码重置策略重置其密码。 
* **活动参与者**：注册了密码重置的用户。 此用户可能是最终用户，也可能是管理员。
* **活动目标**：注册了密码重置的用户。 此用户可能是最终用户，也可能是管理员。
* **允许的活动状态**：
  * 成功：表示用户已根据当前策略成功注册了密码重置。 
  * 失败：表示用户未能注册密码重置。 选择相应的行可查看“活动状态原因”类别，详细了解发生失败的原因。 

     >[!NOTE]
     >“失败”并不意味着用户无法重置其自己的密码。 而是意味着他们未完成注册过程。 如果用户的帐户中未验证的数据是正确的（例如，未验证的电话号码），则即使他们未验证此电话号码，也仍可以使用该电话号码来重置其密码。 有关详细信息，请参阅[用户注册时会发生什么情况？](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers)。
     >

## <a name="next-steps"></a>后续步骤

* [如何成功推出 SSPR？](howto-sspr-deployment.md)
* [重置或更改密码](../active-directory-passwords-update-your-own-password.md)。
* [注册自助服务密码重置](../active-directory-passwords-reset-register.md)。
* [是否有许可问题？](concept-sspr-licensing.md)
* [SSPR 使用哪些数据？应为用户填充哪些数据？](howto-sspr-authenticationdata.md)
* [哪些身份验证方法可供用户使用？](concept-sspr-howitworks.md#authentication-methods)
* [SSPR 有哪些策略选项？](concept-sspr-policy.md)
* [什么是密码写回？我为什么关心它？](howto-sspr-writeback.md)
* [SSPR 中的所有选项是什么？它们有哪些含义？](concept-sspr-howitworks.md)
* [我认为有些功能被破坏。如何对 SSPR 进行故障排除？](active-directory-passwords-troubleshoot.md)
* [我有在别处未涵盖的问题](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Azure AD 中的 SSPR 活动审核日志示例"
