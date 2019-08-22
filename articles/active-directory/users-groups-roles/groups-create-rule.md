---
title: 创建动态组并检查状态 - Azure Active Directory | Microsoft Docs
description: 如何在 Azure 门户中创建组成员身份规则, 请检查状态。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/12/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb4f9d2f78857231d0ecd81a2538a75b4b8a2f74
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650304"
---
# <a name="create-a-dynamic-group-and-check-status"></a>创建动态组并检查状态

在 Azure Active Directory (Azure AD) 中, 可以使用规则根据用户或设备属性来确定组成员身份。 本文介绍如何在 Azure 门户中为动态组设置规则。
安全组或 Office 365 组支持动态成员资格。 应用组成员身份规则时, 将评估用户和设备属性与成员身份规则的匹配项。 当用户或设备的属性发生更改时, 将为组织中的所有动态组规则处理成员身份更改。 如果用户和设备满足组的条件, 则将其添加或删除。 安全组可用于设备或用户, 但 Office 365 组只能是用户组。

有关语法、支持的属性、运算符以及成员身份规则的值的示例, 请参阅[Azure Active Directory 中组的动态成员身份规则](groups-dynamic-membership.md)。

## <a name="to-create-a-group-membership-rule"></a>要创建组成员资格规则，请执行以下操作：

1. 使用租户中 "全局管理员"、"Intune 管理员" 或 "用户管理员" 角色中的帐户登录到[Azure AD 管理中心](https://aad.portal.azure.com)。
2. 选择“组”。
3. 选择“所有组”，然后选择“新组”。

   ![选择要添加新组的命令](./media/groups-create-rule/new-group-creation.png)

4. 在 "**组**" 页上, 输入新组的名称和描述。 选择 "用户" 或 "设备" 的**成员身份类型**, 然后选择 "**添加动态查询**"。 规则生成器最多支持五个表达式。 若要添加第六个或任何后续表达式, 必须使用文本框。

   ![添加动态组的成员身份规则](./media/groups-create-rule/add-dynamic-group-rule.png)

5. 查看可用于成员资格查询的自定义扩展属性
   1. 选择**获取自定义扩展属性**
   2. 输入应用程序 ID, 然后选择 "**刷新属性**"。
6. 创建规则后, 选择 "**保存**"。
7. 在 "**新建组**" 页上选择 "**创建**" 来创建组。

如果你输入的规则无效, 则会在门户中的 Azure 通知中显示无法处理规则的原因说明。 仔细阅读, 了解如何修复规则。

## <a name="turn-on-or-off-welcome-email"></a>打开或关闭欢迎电子邮件

创建新的 Office 365 组后, 会向添加到该组的用户发送欢迎电子邮件通知。 稍后, 如果用户或设备的任何属性发生更改, 则会为组织中的所有动态组规则处理成员身份更改。 添加的用户也会收到欢迎通知。 你可以在[Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps)中禁用此行为。

## <a name="check-processing-status-for-a-rule"></a>检查规则的处理状态

可在组的“概述”页上查看成员资格处理状态和上次更新日期。
  
  ![显示动态组状态](./media/groups-create-rule/group-status.png)

“成员资格处理”状态会显示以下几种状态消息：

* **正在评估**：已收到组更改，正在评估更新。
* **正在处理**：正在处理更新。
* **更新完成**：处理已完成，且已完成所有适用更新。
* **处理错误**：无法完成处理, 因为评估成员身份规则时出错。
* **更新已暂停**：管理员暂停了动态成员资格规则更新。 MembershipRuleProcessingState 设置为“已暂停”。

“上次更新的成员资格”状态会显示以下几种状态消息：

* &lt;**日期和时间**&gt;：上次更新成员资格的时间。
* **正在进行**：目前正在进行更新。
* **未知**：无法检索上次更新时间。 该组可能是新组。

如果在处理特定组的成员资格规则时出错误，则该组的“概述”页顶部会显示警报。 如果无法在 24 小时之后处理租户中所有组的挂起动态成员资格更新，则会在所有组的顶部显示警报。

![处理错误消息警报](./media/groups-create-rule/processing-error.png)

以下文章提供了有关 Azure Active Directory 中的组的更多信息。

* [查看现有组](../fundamentals/active-directory-groups-view-azure-portal.md)
* [创建新组并添加成员](../fundamentals/active-directory-groups-create-azure-portal.md)
* [管理组的设置](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [管理组的成员身份](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [管理组中用户的动态规则](groups-dynamic-membership.md)
