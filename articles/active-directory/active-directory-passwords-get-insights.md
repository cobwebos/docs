---
title: "深入分析：Azure AD 密码管理报告 | Microsoft 文档"
description: "本文介绍了如何使用报告来深入分析组织中的密码管理操作。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 1472b51d-53f4-4b0f-b1be-57f6fa88fa65
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 7375d2d3c237c3b1c2dcdab44b2fcb0000ff961c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/04/2017

---
# <a name="how-to-get-operational-insights-with-password-management-reports"></a>如何使用密码管理报告获取操作见解
> [!IMPORTANT]
> **你是否因登录时遇到问题而浏览至此？** 如果是这样， [可按以下方式更改和重置你的密码](active-directory-passwords-update-your-own-password.md#reset-or-unlock-my-password-for-a-work-or-school-account)。
>
>

本部分介绍了如何使用 Azure Active Directory 的密码管理报告来查看组织中用户对密码重置和更改的使用情况。

* [**密码管理报告概述**](#overview-of-password-management-reports)
* [**如何在新 Azure 门户中查看密码管理报告**](#how-to-view-password-management-reports)
 * [有权读取报告的目录角色](#directory-roles-allowed-to-read-reports)
* [**新 Azure 门户中的自助密码管理活动类型**](#self-service-password-management-activity-types)
 * [被自助密码重置功能阻止](#activity-type-blocked-from-self-service-password-reset)
 * [更改密码(自助服务)](#activity-type-change-password-self-service)
 * [重置密码(由管理员)](#activity-type-reset-password-by-admin)
 * [重置密码(自助服务)](#activity-type-reset-password-self-service)
 * [自助密码重置流活动进度](#activity-type-self-serve-password-reset-flow-activity-progress)
 * [解锁用户帐户(自助服务)](#activity-type-unlock-user-account-self-service)
 * [用户已注册自助密码重置](#activity-type-user-registered-for-self-service-password-reset)
* [**如何通过 Azure AD 报告和事件 API 检索密码管理事件**](#how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api)
 * [报告 API 数据检索限制](#reporting-api-data-retrieval-limitations)
* [**如何使用 PowerShell 快速下载密码重置注册事件**](#how-to-download-password-reset-registration-events-quickly-with-powershell)
* [**如何在经典门户中查看密码管理报告**](#how-to-view-password-management-reports-in-the-classic-portal)
* [**在经典门户中查看组织中的密码重置注册活动**](#view-password-reset-registration-activity-in-the-classic-portal)
* [**在经典门户中查看组织中的密码重置活动**](#view-password-reset-activity-in-the-classic-portal)


## <a name="overview-of-password-management-reports"></a>密码管理报告概述
一旦部署密码重置，最常见的后续步骤之一便是查看其在组织中的使用情况。  例如，你可能希望了解用户对密码重置的注册情况，或者在过去几天内进行了多少个密码重置。  使用 [Azure 管理门户](https://manage.windowsazure.com)中目前提供的密码管理报告可以解答下面这些常见问题：

* 有多少人已注册了密码重置？
* 谁已经注册了密码重置？
* 人们都注册哪些数据？
* 有多少人在过去 7 天内重置了他们的密码？
* 用户或管理员用于重置其密码的最常见方法是什么？
* 用户或管理员尝试使用密码重置时面临的常见问题是什么？
* 哪些管理员经常重置其自己的密码？
* 密码重置时是否有任何可疑的活动？

## <a name="how-to-view-password-management-reports"></a>如何查看密码管理报告
在新 [Azure 门户](https://portal.azure.com)体验中，我们提供一种改进的方式用于查看密码重置和密码重置注册活动。  请遵循以下步骤，在新 [Azure 门户](https://portal.azure.com)中查找密码重置和密码重置注册事件：

1. 导航到 [**portal.azure.com**](https://portal.azure.com)
2. 在主 Azure 门户的左侧导航栏中单击“更多服务”菜单
3. 在服务列表中搜索 **Azure Active Directory** 并选中它
4. 在 Azure Active Directory 导航菜单中单击“用户和组”
5. 在“用户和组”导航菜单中单击“审核日志”导航项。 此时将显示目录中所有用户发生的所有审核事件。 还可以筛选此视图，以查看所有与密码相关的事件。
6. 若要筛选此视图以便仅显示密码管理相关的事件，请单击边栏选项卡顶部的“筛选”按钮。
7. 在“筛选”菜单中选择“类别”下拉列表，然后将其更改为“自助密码管理”类别类型。
8. （可选）通过选择所需的特定“活动”进一步筛选该列表
### <a name="direct-link-to-user-audit-blade"></a>“用户审核”边栏选项卡的直接链接
如果已登录到门户，可通过用户审核边栏选项卡的以下直接链接查看这些事件：

* [直接转到用户管理审核视图](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Audit)

### <a name="directory-roles-allowed-to-read-reports"></a>有权读取报告的目录角色
目前，以下目录角色可在经典 Azure 门户中读取 Azure AD 密码管理报告：

* 全局管理员

在读取这些报告之前，公司的全局管理员必须至少访问报告选项卡或审核日志一次，选择代表组织检索这些数据。 只有在完成此过程后，才能为组织收集数据。

若要详细了解目录角色及其权限，请参阅[在 Azure Active Directory 中分配管理员角色](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-assign-admin-roles)。

## <a name="self-service-password-management-activity-types"></a>自助服务密码管理活动类型
“自助密码管理”审核事件类别中显示了以下活动类型。  下面提供了其中每个活动类型的说明。

* [**被自助密码重置功能阻止**](#activity-type-blocked-from-self-service-password-reset) - 表示用户在 24 小时内尝试重置密码、使用特定的门限或验证某个电话号码总共超过 5 次。
* [**更改密码(自助服务)**](#activity-type-change-password-self-service) - 表示用户执行了自愿性或强制性（由于密码过期）密码更改。
* [**重置密码(由管理员)**](#activity-type-reset-password-by-admin) - 表示管理员通过 Azure 门户代表用户执行了密码重置。
* [**重置密码(自助服务)**](#activity-type-reset-password-self-service) - 表示用户已成功通过 [Azure AD 密码重置门户](https://passwordreset.microsoftonline.com)重置其密码。
* [**自助密码重置流活动进度**](#activity-type-self-serve-password-reset-flow-activity-progress) - 表示密码重置过程中用户执行的每个特定步骤（例如，传递特定的密码重置身份验证门限）。
* [**解锁用户帐户(自助服务)**](#activity-type-unlock-user-account-self-service) - 表示用户在未通过 [Azure AD 密码重置门户](https://passwordreset.microsoftonline.com)重置其密码的情况下，使用[无需重置的 AD 帐户解锁](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-customize#allow-users-to-unlock-accounts-without-resetting-their-password)功能成功解锁了其 Active Directory 帐户。
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

* **活动说明** - 表示用户在未通过 [Azure AD 密码重置门户](https://passwordreset.microsoftonline.com)重置其密码的情况下，使用[无需重置的 AD 帐户解锁](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-customize#allow-users-to-unlock-accounts-without-resetting-their-password)功能成功解锁了其 Active Directory 帐户。
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

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>如何通过 Azure AD 报告和事件 API 检索密码管理事件
从 2015 年 8 月开始，Azure AD 报告和事件 API 支持检索密码重置和密码重置注册报告中包含的所有信息。 使用此 API 可以下载可与所选报告技术集成的各个密码重置和密码重置注册事件。

### <a name="how-to-get-started-with-the-reporting-api"></a>如何开始使用报告 API
若要访问此数据，你需要编写一个小型应用或脚本，以便从我们的服务器检索这些数据。 [了解如何开始使用 Azure AD 报告 API](active-directory-reporting-api-getting-started.md)。

编写有效的脚本后，接下来请根据你的方案，检查可以检索的密码重置和注册事件。

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent)：列出密码重置事件可用的列
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent)：列出密码重置注册事件可用的列

### <a name="reporting-api-data-retrieval-limitations"></a>报告 API 数据检索限制
目前，Azure AD 报告和事件 API 最多可以检索**过去 30 天内** [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) 和 [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent) 类型的 **75,000 个事件**。

如果需要检索或存储的数据超过此时间范围，我们建议将数据保存在外部数据库中，并使用该 API 来查询生成的差异数据。 最佳做法是在组织中启动密码重置注册过程时开始检索此数据并将其保存在外部，然后从此时间点开始继续跟踪差异数据。

## <a name="how-to-download-password-reset-registration-events-quickly-with-powershell"></a>如何使用 PowerShell 快速下载密码重置注册事件
除了直接使用 Azure AD 报告和事件 API 以外，还可以使用以下 PowerShell 脚本来查询目录中最新的注册事件。 如果想要查看最近谁已注册，或者想要确保密码重置的部署按预期进行，这种做法将非常有用。

* [Azure AD SSPR 注册活动 PowerShell 脚本](https://gallery.technet.microsoft.com/scriptcenter/azure-ad-self-service-e31b8aee)

## <a name="how-to-view-password-management-reports-in-the-classic-portal"></a>如何在经典门户中查看密码管理报告
若要查找密码管理报告，请按照以下步骤操作：

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中单击“Active Directory”扩展。
2. 从门户显示的列表中选择你的目录。
3. 单击“报告”选项卡。
4. 查看“活动日志”部分的下面。
5. 选择“密码重置活动报告”或“密码重置注册活动”报告。

## <a name="view-password-reset-registration-activity-in-the-classic-portal"></a>在经典门户中查看密码重置注册活动
密码重置注册活动报告显示你的组织中已发生的所有密码重置注册。  对于已在密码重置注册门户 ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) 成功注册身份验证信息的所有用户，密码重置注册都显示在此报告中。

* **最大时间范围**：30 天
* **最大行数**：75,000
* **可下载**：是，通过 CSV 文件

### <a name="description-of-report-columns"></a>报告列的说明
以下列表详细说明每个报告列：

* **用户** – 尝试了密码重置注册操作的用户。
* **角色** – 该用户在目录中的角色。
* **日期和时间** – 尝试日期和时间。
* **已注册数据** – 用户在密码重置注册期间提供的身份验证数据。

### <a name="description-of-report-values"></a>报告值的说明
下表描述了每个列的不同允许值：

| 列 | 允许值及其含义 |
| --- | --- |
| 已注册数据 |**备用电子邮件** – 用户使用了备用电子邮件或身份验证电子邮件进行身份验证<p><p>**办公电话** – 用户使用了办公电话进行身份验证<p>**移动电话** – 用户使用了移动电话或身份验证电话进行身份验证<p>**安全问题** – 用户使用了安全问题进行身份验证<p>**上述任一组合（例如，备用电子邮件 + 移动电话）**– 指定两项策略时发生，并显示用户使用哪两种方法对其密码重置请求进行身份验证。 |

## <a name="view-password-reset-activity-in-the-classic-portal"></a>在经典门户中查看密码重置活动
此报告显示你的组织中发生的所有密码重置尝试。

* **最大时间范围**：30 天
* **最大行数**：75,000
* **可下载**：是，通过 CSV 文件

### <a name="description-of-report-columns"></a>报告列的说明
以下列表详细说明每个报告列：

1. **用户** – 尝试了密码重置操作的用户（基于用户重置密码时提供的“用户 ID”字段）。
2. **角色** – 该用户在目录中的角色。
3. **日期和时间** – 尝试日期和时间。
4. **所用方法** – 用户针对此重置操作使用的身份验证方法。
5. **结果** – 密码重置操作的最终结果。
6. **详细信息** – 密码重置为什么导致这样值的详细信息。  此外，还包括你为解决意外错误而可能采取的任何缓解步骤。

### <a name="description-of-report-values"></a>报告值的说明
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

## <a name="next-steps"></a>后续步骤
以下是所有 Azure AD 密码重置文档页面的链接：

* **你是否因登录时遇到问题而浏览至此？** 如果是这样， [可按以下方式更改和重置你的密码](active-directory-passwords-update-your-own-password.md#reset-or-unlock-my-password-for-a-work-or-school-account)。
* [**工作原理**](active-directory-passwords-how-it-works.md) - 了解六个不同的服务组件及其功能
* [**入门**](active-directory-passwords-getting-started.md) - 了解如何让用户重置和更改云密码或本地密码
* [**自定义**](active-directory-passwords-customize.md) - 了解如何根据组织的需求自定义服务的外观和行为
* [**最佳做法**](active-directory-passwords-best-practices.md) - 了解如何快速部署且有效管理组织的密码
* [**常见问题**](active-directory-passwords-faq.md) - 获取常见问题的解答
* [**故障排除**](active-directory-passwords-troubleshoot.md) - 了解如何快速排查服务的问题
* [**了解详细信息**](active-directory-passwords-learn-more.md) - 深入探索服务工作原理的技术详细信息

