---
title: 更新和管理动态组规则并排除成员身份 Azure Active Directory |Microsoft Docs
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
ms.openlocfilehash: ce3bce5e2656efea0a4fc3d7aa6be46f1e6926ec
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657341"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>更新动态组以管理中的成员身份 Azure Active Directory

在 Azure Active Directory (Azure AD) 中, 可以使用规则根据用户或设备属性来确定组成员身份。 本文介绍如何在 Azure 门户中为动态组设置规则。
安全组或 Office 365 组支持动态成员资格。 应用组成员身份规则时, 将评估用户和设备属性与成员身份规则的匹配项。 当用户或设备的属性发生更改时, 将为组织中的所有动态组规则处理成员身份更改。 如果用户和设备满足组的条件, 则将其添加或删除。

有关语法、支持的属性、运算符以及成员身份规则的值的示例, 请参阅[Azure Active Directory 中组的动态成员身份规则](groups-dynamic-membership.md)。

## <a name="to-update-a-group-membership-rule"></a>更新组成员身份规则

1. 使用租户中 "全局管理员"、"Intune 管理员" 或 "用户管理员" 角色中的帐户登录到[Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择 "**组** > " "**所有组**"。
1. 选择组以打开其配置文件。
1. 在该组的 "配置文件" 页上, 选择 "**动态成员身份规则**"。 规则生成器最多支持五个表达式。 若要添加第六个或任何后续表达式, 必须使用文本框。

   ![添加动态组的成员身份规则](./media/groups-update-rule/update-dynamic-group-rule.png)

1. 查看适用于你的成员身份规则的自定义扩展属性:
   1. 选择**获取自定义扩展属性**
   2. 输入应用程序 ID, 然后选择 "**刷新属性**"。
1. 更新规则后, 请选择 "**保存**"。

如果你输入的规则无效, 则会在门户中的 Azure 通知中显示无法处理规则的原因说明。 仔细阅读, 了解如何修复规则。

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

* **日期和时间**:上次更新成员资格的时间。
* **正在进行**：目前正在进行更新。
* **未知**：无法检索上次更新时间。 该组可能是新组。

如果在处理特定组的成员资格规则时出错误，则该组的“概述”页顶部会显示警报。 如果无法在 24 小时之后处理租户中所有组的挂起动态成员资格更新，则会在所有组的顶部显示警报。

![处理错误消息警报](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>后续步骤

这些文章提供了有关在 Azure AD 中使用动态组的其他信息。

* 有关动态规则结构的完整引用, 请参阅[动态成员身份规则语法](groups-dynamic-membership.md)。
* [创建静态成员身份组并添加成员](../fundamentals/active-directory-groups-create-azure-portal.md)。
