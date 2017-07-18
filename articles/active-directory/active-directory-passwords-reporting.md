---
title: "报告：Azure AD SSPR | Microsoft Docs"
description: "报告 Azure AD 自助密码重置事件"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 4d7f05d626e6f718ca3597dff00715af4055f046
ms.contentlocale: zh-cn
ms.lasthandoff: 05/03/2017

---
# <a name="reporting-options-for-azure-ad-password-management"></a>用于 Azure AD 密码管理的报告选项

部署后，许多组织想要知道如何使用或者是否已真正使用 SSPR。 Azure AD 提供报告功能，可帮助你使用集成的报告解答问题；如果你已获得适当的授权，则还可以创建自定义查询。

[Azure 门户] (https://portal.azure.com/) 中提供的报告可解答以下问题。

> [!NOTE]
> 你必须是[全局管理员](active-directory-assign-admin-roles.md#assign-or-remove-administrator-roles)，并且必须通过访问报告选项卡或审核日志至少一次，选择代表组织收集这些数据。 只有在完成此过程后，才能为组织收集数据。

* 有多少人已注册了密码重置？
* 谁已经注册了密码重置？
* 人们都注册哪些数据？
* 有多少人在过去 7 天内重置了他们的密码？
* 用户或管理员用于重置其密码的最常见方法是什么？
* 用户或管理员尝试使用密码重置时面临的常见问题是什么？
* 哪些管理员经常重置其自己的密码？
* 密码重置时是否有任何可疑的活动？

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>如何在 Azure 门户中查看密码管理报告

在 Azure 门户体验中，我们提供一种改进的方式用于查看密码重置和密码重置注册活动。  请遵循以下步骤，查找密码重置和密码重置注册事件：

1. 导航到 [**portal.azure.com**](https://portal.azure.com)
2. 在主 Azure 门户的左侧导航栏中单击“更多服务”菜单
3. 在服务列表中搜索 **Azure Active Directory** 并选中它
4. 在 Azure Active Directory 导航菜单中单击“用户和组”
5. 在“用户和组”导航菜单中单击“审核日志”导航项。 此时将显示目录中所有用户发生的所有审核事件。 还可以筛选此视图，以查看所有与密码相关的事件。
6. 若要筛选此视图以便仅显示密码重置相关的事件，请单击边栏选项卡顶部的“筛选”按钮。
7. 在“筛选”菜单中选择“类别”下拉列表，然后将其更改为“自助密码管理”类别类型。
8. （可选）通过选择所需的特定“活动”进一步筛选该列表

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>如何通过 Azure AD 报告和事件 API 检索密码管理事件

Azure AD 报告和事件 API 支持检索密码重置和密码重置注册报告中包含的所有信息。 使用此 API 可以下载可与所选报告技术集成的各个密码重置和密码重置注册事件。

### <a name="how-to-get-started-with-the-reporting-api"></a>如何开始使用报告 API

若要访问此数据，你需要编写一个小型应用或脚本，以便从我们的服务器检索这些数据。 [了解如何开始使用 Azure AD 报告 API](active-directory-reporting-api-getting-started.md)。

编写有效的脚本后，接下来请根据你的方案，检查可以检索的密码重置和注册事件。

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent)：列出密码重置事件可用的列
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent)：列出密码重置注册事件可用的列

### <a name="reporting-api-data-retrieval-limitations"></a>报告 API 数据检索限制

目前，Azure AD 报告和事件 API 最多可以检索**过去 30 天内** [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) 和 [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent) 类型的 **75,000 个事件**。

如果需要检索或存储的数据超过此时间范围，我们建议将数据保存在外部数据库中，并使用该 API 来查询生成的差异数据。 我们建议在组织中开始使用 SSPR 时开始检索此数据并将其保存在外部，然后从此时间点开始继续跟踪差异数据。

## <a name="how-to-download-password-reset-registration-events-quickly-with-powershell"></a>如何使用 PowerShell 快速下载密码重置注册事件

除了直接使用 Azure AD 报告和事件 API 以外，还可以使用以下 PowerShell 脚本来查询目录中最新的注册事件。 如果想要查看最近谁已注册，或者想要确保密码重置的部署按预期进行，这种做法将非常有用。

* [Azure AD SSPR 注册活动 PowerShell 脚本](https://gallery.technet.microsoft.com/scriptcenter/azure-ad-self-service-e31b8aee)

### <a name="description-of-report-columns-in-azure-portal"></a>Azure 门户中报告列的说明

以下列表详细说明每个报告列：

* **用户** – 尝试了密码重置注册操作的用户。
* **角色** – 该用户在目录中的角色。
* **日期和时间** – 尝试日期和时间。
* **已注册数据** – 用户在密码重置注册期间提供的身份验证数据。

### <a name="description-of-report-values-in-azure-portal"></a>Azure 门户中报告值的说明

下表描述了每个列的不同允许值：

| 列 | 允许值及其含义 |
| --- | --- |
| 已注册数据 |**备用电子邮件** – 用户使用了备用电子邮件或身份验证电子邮件进行身份验证<p><p>**办公电话** – 用户使用了办公电话进行身份验证<p>**移动电话** – 用户使用了移动电话或身份验证电话进行身份验证<p>**安全问题** – 用户使用了安全问题进行身份验证<p>**上述任一组合（例如，备用电子邮件 + 移动电话）**– 指定两项策略时发生，并显示用户使用哪两种方法对其密码重置请求进行身份验证。 |

## <a name="view-password-reset-activity-in-the-classic-portal"></a>在经典门户中查看密码重置活动

此报告显示你的组织中发生的所有密码重置尝试。

* **最大时间范围**：30 天
* **最大行数**：75,000
* **可下载**：是，通过 CSV 文件

### <a name="description-of-report-columns-in-azure-classic-portal"></a>Azure 经典门户中报告列的说明

以下列表详细说明每个报告列：

1. **用户** – 尝试了密码重置操作的用户（基于用户重置密码时提供的“用户 ID”字段）。
2. **角色** – 该用户在目录中的角色。
3. **日期和时间** – 尝试日期和时间。
4. **所用方法** – 用户针对此重置操作使用的身份验证方法。
5. **结果** – 密码重置操作的结果。
6. **详细信息** – 密码重置为什么导致这样值的详细信息。  此外，还包括你为解决意外错误而可能采取的任何缓解步骤。

### <a name="description-of-report-values-in-azure-classic-portal"></a>Azure 经典门户中报告值的说明

下表描述了每个列的不同允许值：

| 列 | 允许值及其含义 |
| --- | --- |
| 所用方法 |**备用电子邮件** – 用户使用了备用电子邮件或身份验证电子邮件进行身份验证<p>**办公电话** – 用户使用了办公电话进行身份验证<p>**移动电话** – 用户使用了移动电话或身份验证电话进行身份验证<p>**安全问题** – 用户使用了安全问题进行身份验证<p>**上述任一组合（例如，备用电子邮件 + 移动电话）**– 指定两项策略时发生，并显示用户使用哪两种方法对其密码重置请求进行身份验证。 |
| 结果 |**已放弃** – 用户启动了密码重置，但尚未完成便中途停止<p>**已阻止** – 用户帐户因在 24 小时内尝试使用密码重置页面或单个密码重置关口的次数过多而被禁止使用密码重置<p>**已取消** – 用户启动了密码重置，但却在中途单击取消按钮取消了会话 <p>**已联系管理员** – 用户在会话期间遇到无法解决的问题，因此用户单击了“联系管理员”链接而不是完成密码重置流程<p>**失败** – 用户无法重置密码，可能原因是用户未被配置为使用该功能（例如，无许可证、缺少身份验证信息、密码在本地管理但写回处于关闭状态）。<p>**成功** – 密码重置成功。 |
| 详细信息 |请参阅下表 |

### <a name="allowed-values-for-details-column"></a>详细信息列的允许值

下面是你在使用密码重置活动报告时可能会遇到的结果类型列表：

| 详细信息 | 结果类型 |
| --- | --- |
| 用户在完成电子邮件验证选项后放弃 |已放弃 |
| 用户在完成手机短信验证选项后放弃 |已放弃 |
| 用户在完成移动语音呼叫验证选项后放弃 |已放弃 |
| 用户在完成办公室语音呼叫验证选项后放弃 |已放弃 |
| 用户在完成安全问题选项后放弃 |已放弃 |
| 用户在输入其用户 ID 后放弃 |已放弃 |
| 用户在启动电子邮件验证选项后放弃 |已放弃 |
| 用户在启动手机短信验证选项后放弃 |已放弃 |
| 用户在启动移动语音呼叫验证选项后放弃 |已放弃 |
| 用户在启动办公室语音呼叫验证选项后放弃 |已放弃 |
| 用户在启动安全问题选项后放弃 |已放弃 |
| 用户在选择新密码之前放弃 |已放弃 |
| 用户在选择新密码时放弃 |已放弃 |
| 用户输入的无效短信验证代码过多，被阻止 24 小时 |已阻止 |
| 用户尝试移动电话语音验证的次数过多，被阻止 24 小时 |已阻止 |
| 用户尝试办公室电话语音验证的次数过多，被阻止 24 小时 |已阻止 |
| 用户尝试回答安全问题的次数过多，被阻止 24 小时 |已阻止 |
| 用户尝试电话号码验证的次数过多，被阻止 24 小时 |已阻止 |
| 用户在传递必需的身份验证方法之前取消 |已取消 |
| 用户在提交新密码之前取消 |已取消 |
| 用户在尝试电子邮件验证选项后联系了管理员 |已联系管理员 |
| 用户在尝试手机短信验证选项后联系了管理员 |已联系管理员 |
| 用户在尝试移动语言呼叫验证选项后联系了管理员 |已联系管理员 |
| 用户在尝试办公室语言呼叫验证选项后联系了管理员 |已联系管理员 |
| 用户在尝试安全问题验证选项后联系了管理员 |已联系管理员 |
| 未对此用户启用密码重置。 启用配置选项卡下的密码重置以解决此问题 |已失败 |
| 用户没有许可证。 你可以将许可证添加到用户以解决此问题 |已失败 |
| 用户试图从设备重置而不启用 Cookie |已失败 |
| 用户帐户已定义足够的身份验证方法。 添加身份验证信息以解决此问题 |已失败 |
| 用户的密码在本地管理。 你可以启用密码写回以解决此问题 |已失败 |
| 我们无法访问你的本地密码重置服务。 检查同步计算机的事件日志 |已失败 |
| 我们在重置用户的本地密码时遇到问题。 检查同步计算机的事件日志 |已失败 |
| 此用户不是密码重置用户组的成员。 将此用户添加到该组以解决此问题。 |已失败 |
| 已对此租户完全禁用密码重置。 若要解决此问题，请参阅[此文](http://aka.ms/ssprtroubleshoot)。 |已失败 |
| 用户已成功重置密码 |已成功 |

## <a name="self-service-password-management-activity-types"></a>自助服务密码管理活动类型

“自助密码管理”审核事件类别中显示了以下活动类型。  下面提供了其中每个活动类型的说明。

* [**被自助密码重置功能阻止**](#activity-type-blocked-from-self-service-password-reset) - 表示用户在 24 小时内尝试重置密码、使用特定的门限或验证某个电话号码总共超过 5 次。
* [**更改密码(自助服务)**](#activity-type-change-password-self-service) - 表示用户执行了自愿性或强制性（由于密码过期）密码更改。
* [**重置密码(由管理员)**](#activity-type-reset-password-by-admin) - 表示管理员通过 Azure 门户代表用户执行了密码重置。
* [**重置密码(自助服务)**](#activity-type-reset-password-self-service) - 表示用户已成功通过 [Azure AD 密码重置门户](https://passwordreset.microsoftonline.com)重置其密码。
* [**自助密码重置流活动进度**](#activity-type-self-serve-password-reset-flow-activity-progress) - 表示密码重置过程中用户执行的每个特定步骤（例如，传递特定的密码重置身份验证门限）。
* [**解锁用户帐户(自助服务)**](#activity-type-unlock-user-account-self-service) - 表示用户在未通过 [Azure AD 密码重置门户](https://passwordreset.microsoftonline.com)重置其密码的情况下，使用无需重置的 AD 帐户解锁功能成功解锁了其 Active Directory 帐户。
* [**用户已注册自助密码重置**](#activity-type-user-registered-for-self-service-password-reset) - 表示用户已注册全部所需的信息，可以根据当前指定的租户密码重置策略重置其密码。

### <a name="activity-type-blocked-from-self-service-password-reset"></a>活动类型：被自助密码重置功能阻止

以下列表详细说明了此活动：

* **活动说明** - 表示用户在 24 小时内尝试重置密码、使用特定的门限或验证某个电话号码总共超过 5 次。
* **活动参与者** - 被限制执行其他重置操作的用户。 可能是最终用户，也可能是管理员。
* **活动目标** - 被限制执行其他重置操作的用户。 可能是最终用户，也可能是管理员。
* **允许的活动状态**
  * _成功_ - 表示某个用户在未来的 24 小时内被限制执行其他任何重置操作、尝试使用其他任何身份验证方法，或验证其他任何电话号码。
* **活动状态失败原因** - 不适用

### <a name="activity-type-change-password-self-service"></a>活动类型：更改密码(自助服务)

以下列表详细说明了此活动：

* **活动说明** - 表示用户执行了自愿性或强制性（由于密码过期）密码更改。
* **活动参与者** - 更改其密码的用户。 可能是最终用户，也可能是管理员。
* **活动目标** - 更改其密码的用户。 可能是最终用户，也可能是管理员。
* **允许的活动状态**
  * _成功_ - 表示用户已成功更改其密码
  * _失败_ - 表示用户未能更改其密码。 单击相应的行可查看“活动状态原因”类别，详细了解发生失败的原因。
* **活动状态失败原因** - 
  * _FuzzyPolicyViolationInvalidPassword_ - 由于 Microsoft 的“受禁密码检测”功能发现用户选择的某个密码过于常见或者太弱，因此已自动阻止此密码。

### <a name="activity-type-reset-password-by-admin"></a>活动类型：重置密码(由管理员)

以下列表详细说明了此活动：

* **活动说明** - 表示管理员通过 Azure 门户代表用户执行了密码重置。
* **活动参与者** - 代表其他最终用户或管理员执行密码重置的管理员。 必须是全局管理员、密码管理员、用户管理员或支持管理员。
* **活动目标** - 重置其密码的用户。 可能是最终用户，也可能是其他管理员。
* **允许的活动状态**
  * _成功_ - 表示管理员已成功重置用户的密码
  * _失败_ - 表示管理员未能更改用户的密码。 单击相应的行可查看“活动状态原因”类别，详细了解发生失败的原因。

### <a name="activity-type-reset-password-self-service"></a>活动类型：重置密码(自助服务)

以下列表详细说明了此活动：

* **活动说明** - 表示用户已成功通过 [Azure AD 密码重置门户](https://passwordreset.microsoftonline.com)重置其密码。
* **活动参与者** - 重置其密码的用户。 可能是最终用户，也可能是管理员。
* **活动目标** - 重置其密码的用户。 可能是最终用户，也可能是管理员。
* **允许的活动状态**
  * _成功_ - 表示用户已成功重置其自己的密码
  * _失败_ - 表示用户未能重置其自己的密码。 单击相应的行可查看“活动状态原因”类别，详细了解发生失败的原因。
* **活动状态失败原因** -
  * _FuzzyPolicyViolationInvalidPassword_ - 由于 Microsoft 的“受禁密码检测”功能发现管理员选择的某个密码过于常见或者太弱，因此已自动阻止此密码。

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>活动类型：自助密码重置流活动进度

以下列表详细说明了此活动：

* **活动说明** - 表示密码重置过程中用户执行的每个特定步骤（例如，传递特定的密码重置身份验证门限）。
* **活动参与者** - 执行了密码重置流的一部分步骤的用户。 可能是最终用户，也可能是管理员。
* **活动目标** - 执行了密码重置流的一部分步骤的用户。 可能是最终用户，也可能是管理员。
* **允许的活动状态**
  * _成功_ - 表示用户已成功完成密码重置流的特定步骤。
  * _失败_ - 表示未能执行密码重置流的特定步骤。 单击相应的行可查看“活动状态原因”类别，详细了解发生失败的原因。
* **允许的活动状态原因**
  * 请参阅下表中列出的[所有允许的重置活动状态原因](#allowed-values-for-details-column)

### <a name="activity-type-unlock-user-account-self-service"></a>活动类型：解锁用户帐户(自助服务)

以下列表详细说明了此活动：

* **活动说明** - 表示用户在未通过 [Azure AD 密码重置门户](https://passwordreset.microsoftonline.com)重置其密码的情况下，使用无需重置的 AD 帐户解锁功能成功解锁了其 Active Directory 帐户。
* **活动参与者** - 在未重置其密码的情况下解锁了其帐户的用户。 可能是最终用户，也可能是管理员。
* **活动目标** - 在未重置其密码的情况下解锁了其帐户的用户。 可能是最终用户，也可能是管理员。
* **允许的活动状态**
  * _成功_ - 表示用户已成功解锁其自己的帐户
  * _失败_ - 表示用户未能解锁其自己的帐户。 单击相应的行可查看“活动状态原因”类别，详细了解发生失败的原因。

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>活动类型：用户已注册自助密码重置

以下列表详细说明了此活动：

* **活动说明** - 表示用户已注册全部所需的信息，可以根据当前指定的租户密码重置策略重置其密码。 
* **活动参与者** - 注册了密码重置的用户。 可能是最终用户，也可能是管理员。
* **活动目标** - 注册了密码重置的用户。 可能是最终用户，也可能是管理员。
* **允许的活动状态**
  * _成功_ - 表示用户已根据当前策略成功注册了密码重置。 
  * _失败_ - 表示用户未能注册密码重置。 单击相应的行可查看“活动状态原因”类别，详细了解发生失败的原因。 注意 - 这并不表示用户无法重置其自己的密码，而只是表示该用户未完成注册过程。 如果用户的帐户中未验证的数据是正确的（例如，未验证的电话号码），则即使他们未验证此电话号码，也仍可以使用该电话号码来重置其密码。 有关详细信息，请参阅[用户注册时会发生什么情况？](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers)

## <a name="next-steps"></a>后续步骤

以下链接提供有关使用 Azure AD 进行密码重置的其他信息

* [用户管理审核日志的快捷方式](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Audit) - 直接转到租户的用户管理审核日志
* [**快速入门**](active-directory-passwords-getting-started.md) - 启动并运行 Azure AD 自助密码管理 
* [**授权**](active-directory-passwords-licensing.md) - 配置 Azure AD 授权
* [**数据**](active-directory-passwords-data.md) - 了解所需的数据以及如何使用它进行密码管理
* [**推出**](active-directory-passwords-best-practices.md) - 使用此处提供的指南计划 SSPR 并将其部署到用户
* [**自定义**](active-directory-passwords-customize.md) - 自定义公司的 SSPR 体验的外观。
* [**深入技术探究**](active-directory-passwords-how-it-works.md) - 了解幕后的工作原理
* [**常见问题**](active-directory-passwords-faq.md) - 如何？ 为什么？ 什么？ 何处？ 谁？ 何时？ - 始终要问的问题的答案
* [**故障排除**](active-directory-passwords-troubleshoot.md) - 了解如何解决使用 SSPR 时遇到的常见问题
* [**策略**](active-directory-passwords-policy.md) - 了解并设置 Azure AD 密码策略

