---
title: 更新和管理动态组规则并排除成员身份 Azure Active Directory |Microsoft Docs
description: 如何在 Azure 门户中创建组成员资格规则并检查状态。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/30/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c387e2d78adcaebc430073a2a45818c4a0928b9f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422350"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>更新动态组以管理中的成员身份 Azure Active Directory

在 Azure Active Directory (Azure AD) 中，可以使用规则根据用户或设备属性确定组成员资格。 本文介绍如何为 Azure 门户中的动态组设置一项规则。
支持为安全组或 Office 365 组启用动态成员身份。 应用组成员身份规则时，将会对用户和设备属性进行评估，确定其是否与成员身份规则匹配。 当用户或设备的任何属性发生更改时，将处理组织中的所有动态组规则以进行成员身份更改。 如果用户和设备符合组的条件，则会对其执行添加或删除操作。

## <a name="rule-builder-in-the-azure-portal"></a>Azure 门户中的规则生成器

Azure AD 提供了一个规则生成器，以便更快地创建和更新重要规则。 规则生成器支持的构造最多为五个表达式。 使用规则生成器可以通过一些简单的表达式轻松形成规则，但是，它不能用于重现每个规则。 如果规则生成器不支持要创建的规则，则可以使用文本框。

下面是一些高级规则或语法的示例，我们建议你使用文本框构造这些规则或语法：

- 包含五个以上表达式的规则
- 直接下属规则
- 设置[运算符优先顺序](groups-dynamic-membership.md#operator-precedence)
- [包含复杂表达式的规则](groups-dynamic-membership.md#rules-with-complex-expressions)；例如 `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> 规则生成器可能无法显示在文本框中构造的某些规则。 当规则生成器无法显示规则时，可能会显示一条消息。 规则生成器不会以任何方式更改动态组规则支持的语法、验证或处理。

![为动态组添加成员身份规则](./media/groups-update-rule/update-dynamic-group-rule.png)

如需成员身份规则的语法、支持的属性、运算符和值的示例，请参阅 [Azure Active Directory 中的组的动态成员资格规则](groups-dynamic-membership.md)。

## <a name="to-update-a-group-membership-rule"></a>更新组成员身份规则

1. 使用租户中 "全局管理员"、"组管理员"、"Intune 管理员" 或 "用户管理员" 角色中的帐户登录到[Azure AD 管理中心](https://aad.portal.azure.com)。
1. 选择**组** > "**所有组**"。
1. 选择组以打开其配置文件。
1. 在该组的 "配置文件" 页上，选择 "**动态成员身份规则**"。 规则生成器支持最多五个表达式。 若要添加五个以上的表达式，必须使用文本框。

   ![为动态组添加成员身份规则](./media/groups-update-rule/update-dynamic-group-rule.png)

1. 查看适用于你的成员身份规则的自定义扩展属性：
   1. 选择“获取自定义扩展属性”
   1. 输入应用程序 ID，然后选择“刷新属性”。
1. 更新规则后，请选择 "**保存**"。

如果输入的规则无效，则会在门户的 Azure 通知中显示一个说明，指出为何系统无法处理规则。 请仔细阅读，了解如何修复规则。

## <a name="check-processing-status-for-a-rule"></a>检查规则的处理状态

可在组的“概述”页上查看成员资格处理状态和上次更新日期。
  
  ![显示动态组状态](./media/groups-create-rule/group-status.png)

“成员资格处理”状态会显示以下几种状态消息：

- **正在评估**：已收到组更改，正在评估更新。
- **正在处理**：正在进行更新。
- **更新完成**：处理已完成，且已完成所有适用更新。
- **处理错误**：无法完成处理，因为评估成员身份规则时出错。
- **更新已暂停**：管理员暂停了动态成员资格规则更新。 MembershipRuleProcessingState 设置为“已暂停”。

“上次更新的成员资格”状态会显示以下几种状态消息：

- **日期和时间**：上次更新成员身份的时间。
- **正在进行**：目前正在进行更新。
- **未知**：无法检索上次更新时间。 该组可能是新的。

如果在处理特定组的成员资格规则时出错误，则该组的“概述”页顶部会显示警报。 如果无法在 24 小时之后处理租户中所有组的挂起动态成员资格更新，则会在所有组的顶部显示警报。

![正在处理错误消息警报](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>后续步骤

这些文章提供了有关在 Azure AD 中使用动态组的其他信息。

- 有关动态规则结构的完整引用，请参阅[动态成员身份规则语法](groups-dynamic-membership.md)。
- [创建静态成员身份组并添加成员](../fundamentals/active-directory-groups-create-azure-portal.md)。
