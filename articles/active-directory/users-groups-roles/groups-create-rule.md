---
title: 在 Azure Active Directory 中创建动态组成员资格并检查状态 | Microsoft Docs
description: 如何在 Azure 门户中创建组成员资格规则并检查状态。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/02/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 9a2eb8ab4e3ee65e97de578c825bf106aee1b829
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480334"
---
# <a name="create-a-dynamic-group-and-check-status"></a>创建动态组并检查状态

在 Azure Active Directory (Azure AD) 中，可以通过应用规则来创建组，以根据用户或设备属性确定成员资格。 当用户或设备属性发生更改时，Azure AD 将评估 Azure AD 租户中的所有动态组规则，并执行任何添加或删除操作。 如果用户或设备满足组的规则，则会将其添加为成员，如果不再满足规则，则会将其删除。

本文详细介绍了如何在 Azure 门户中为安全组或 Office 365 组的动态成员资格设置规则。 有关规则语法的示例以及成员资格规则支持的属性、运算符和值的完整列表，请参阅 [Azure Active Directory 中的组的动态成员资格规则](groups-dynamic-membership.md)。

## <a name="to-create-a-group-membership-rule"></a>要创建组成员资格规则，请执行以下操作：

1. 使用全局管理员或用户帐户管理员的帐户登录到 [Azure AD 管理中心](https://aad.portal.azure.com)。
2. 选择“组”。
3. 选择“所有组”，然后选择“新组”。

   ![添加新组](./media/groups-create-rule/new-group-creation.png)

4. 在“组”边栏选项卡上，输入新组的名称和说明。 根据是要为用户还是设备创建规则，在“成员身份类型”中选择“动态用户”或“动态设备”，并选择“添加动态查询”。 可使用规则生成器生成一个简单的规则，或自己编写一个成员资格身份规则。 本文包含有关可用的用户和设备属性，以及成员资格规则示例的详细信息。

   ![添加动态成员身份规则](./media/groups-create-rule/add-dynamic-group-rule.png)

5. 创建规则之后，选择边栏选项卡底部的“添加查询”。
6. 在“组”边栏选项卡中，选择“创建”以创建该组。

> [!TIP]
> 如果输入的规则格式不正确或无效，则无法创建组。 门户右上角将显示一个通知，解释为何系统无法处理规则。 请仔细阅读，了解如何调整规则以使其生效。

## <a name="check-processing-status-for-a-membership-rule"></a>检查成员资格规则的处理状态

可在组的“概述”页上查看成员资格处理状态和上次更新日期。
  
  ![动态组状态显示](./media/groups-create-rule/group-status.png)

“成员资格处理”状态会显示以下几种状态消息：

* **正在评估**：已收到组更改，正在评估更新。
* **正在处理**：正在进行更新。
* **更新完成**：处理已完成，且已完成所有适用更新。
* **处理错误**：评估成员资格规则时遇到错误，无法完成处理。
* **更新已暂停**：管理员暂停了动态成员资格规则更新。 MembershipRuleProcessingState 设置为“已暂停”。

“上次更新的成员资格”状态会显示以下几种状态消息：

* &lt;**日期和时间**&gt;：上次更新成员资格的时间。
* **正在进行**：目前正在进行更新。
* **未知**：无法检索上次更新时间。 这可能是由于该组是新创建的组。

如果在处理特定组的成员资格规则时出错误，则该组的“概述”页顶部会显示警报。 如果无法在 24 小时之后处理租户中所有组的挂起动态成员资格更新，则会在所有组的顶部显示警报。

![正在处理错误消息](./media/groups-create-rule/processing-error.png)

以下文章提供了有关 Azure Active Directory 中的组的更多信息。

* [查看现有组](../fundamentals/active-directory-groups-view-azure-portal.md)
* [创建新组并添加成员](../fundamentals/active-directory-groups-create-azure-portal.md)
* [管理组的设置](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [管理组的成员身份](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [管理组中用户的动态规则](groups-dynamic-membership.md)
