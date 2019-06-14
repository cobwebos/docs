---
title: 创建动态组并检查状态 - Azure Active Directory | Microsoft Docs
description: 如何在 Azure 门户中创建的组成员身份规则，请检查状态。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f828ff83e6b9c60eb08edef7f47e88185fb5aef8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60472104"
---
# <a name="create-a-dynamic-group-and-check-status"></a>创建动态组并检查状态

在 Azure Active Directory (Azure AD) 中可以使用规则来确定组成员身份基于用户或设备的属性。 本文介绍如何设置 Azure 门户中为动态组规则。
安全组或 Office 365 组支持动态成员身份。 应用的组成员身份规则时，将评估与成员身份规则匹配的用户和设备属性。 当属性更改为用户或设备时，组织中的所有动态组规则处理的成员身份更改。 添加或删除它们满足一组条件，如果用户和设备。

有关语法、 支持的属性、 运算符和值的成员身份规则的示例，请参阅[Azure Active Directory 中组的动态成员身份规则](groups-dynamic-membership.md)。

## <a name="to-create-a-group-membership-rule"></a>要创建组成员资格规则，请执行以下操作：

1. 登录到[Azure AD 管理中心](https://aad.portal.azure.com)处于全局管理员、 Intune 管理员或用户管理员角色的租户中的帐户。
2. 选择“组”。 
3. 选择“所有组”  ，然后选择“新组”  。

   ![选择要添加新组的命令](./media/groups-create-rule/new-group-creation.png)

4. 上**组**页上，输入名称和为新组的说明。 选择**成员身份类型**作为用户或设备，然后选择**添加动态查询**。 您可以使用规则生成器来生成一个简单的规则，或[自己编写的成员身份规则](groups-dynamic-membership.md)。

   ![添加动态组的成员身份规则](./media/groups-create-rule/add-dynamic-group-rule.png)

5. 若要查看可用于成员资格查询的自定义扩展属性
   1. 选择**获取自定义扩展属性**
   2. 输入应用程序 ID，然后选择**刷新属性**。 
6. 创建规则之后，选择边栏选项卡底部的“添加查询”  。
7. 在“组”边栏选项卡中，选择“创建”以创建该组。  

如果您输入的规则不是有效的原因无法处理此规则的说明将显示在门户的右上角。 仔细阅读，了解如何修复该规则。

## <a name="turn-on-or-off-welcome-email"></a>打开或关闭欢迎电子邮件

创建新的 Office 365 组后，欢迎使用的通知将发送的用户添加到组。 更高版本，如果用户或设备的任何属性发生更改，在组织中的所有动态组规则处理的成员身份更改。 添加的用户也会收到欢迎通知。 您可以关闭此行为[Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps)。 

## <a name="check-processing-status-for-a-rule"></a>检查规则的处理状态

可在组的“概述”页上查看成员资格处理状态和上次更新日期  。
  
  ![显示的动态组状态](./media/groups-create-rule/group-status.png)

“成员资格处理”状态会显示以下几种状态消息  ：

* **正在评估**：已收到组更改，正在评估更新。
* **正在处理**：正在处理更新。
* **更新完成**：处理已完成，且已完成所有适用更新。
* **处理错误**：由于评估成员身份规则出现错误，无法完成处理。
* **更新已暂停**：管理员暂停了动态成员资格规则更新。 MembershipRuleProcessingState 设置为“已暂停”。

“上次更新的成员资格”状态会显示以下几种状态消息  ：

* &lt;**日期和时间**&gt;：上次更新成员资格的时间。
* **正在进行**：目前正在进行更新。
* **未知**：无法检索上次更新时间。 此组可能是新的。

如果在处理特定组的成员资格规则时出错误，则该组的“概述”页顶部会显示警报  。 如果无法在 24 小时之后处理租户中所有组的挂起动态成员资格更新，则会在所有组的顶部显示警报  。

![处理错误的消息警报](./media/groups-create-rule/processing-error.png)

以下文章提供了有关 Azure Active Directory 中的组的更多信息。

* [查看现有组](../fundamentals/active-directory-groups-view-azure-portal.md)
* [创建新组并添加成员](../fundamentals/active-directory-groups-create-azure-portal.md)
* [管理组的设置](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [管理组的成员身份](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [管理组中用户的动态规则](groups-dynamic-membership.md)
