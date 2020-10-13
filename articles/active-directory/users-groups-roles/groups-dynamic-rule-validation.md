---
title: 验证动态组成员身份的规则 (预览) -Azure AD |Microsoft Docs
description: 如何针对 Azure Active Directory 中动态组的成员身份规则测试成员。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 04/10/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbbe37938249ee02c7deb4081a96aa1be2d8b7cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84728498"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a> (预览) 验证动态组成员身份规则 Azure Active Directory

Azure Active Directory (Azure AD) 现在提供了在公共预览版 (中验证动态组规则的方法。 在 " **验证规则** " 选项卡上，可以对照组成员验证动态规则，以确认规则按预期方式工作。 创建或更新动态组规则时，管理员希望知道用户或设备是否将成为该组的成员。 这有助于评估用户或设备是否满足规则条件，并有助于在不期望成员身份时进行故障排除。

## <a name="step-by-step-walk-through"></a>循序渐进的演练

若要开始，请转到**Azure Active Directory**  >  **组**。 选择现有动态组，或创建新的动态组，并单击 "动态成员身份规则"。 然后，你可以看到 " **验证规则** " 选项卡。

![找到 "验证规则" 选项卡，然后从现有规则开始](./media/groups-dynamic-rule-validation/validate-tab.png)

在 " **验证规则** " 选项卡上，可以选择用户来验证其成员身份。 可以一次选择20个用户或设备。

![添加用户以验证现有规则](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

从选取器选择用户或设备后， **选择**"验证" 将自动启动并显示验证结果。

![查看规则验证的结果](./media/groups-dynamic-rule-validation/validate-tab-results.png)

结果指示用户是否为组的成员。 如果规则无效或出现网络问题，结果将显示为 " **未知**"。 如果是 **未知**的，详细的错误消息将说明所需的问题和操作。

![查看规则验证结果的详细信息](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

您可以修改规则并将触发成员身份验证。 若要查看用户不是组成员的原因，请单击 "查看详细信息"，验证详细信息将显示构成规则的每个表达式的结果。 单击 **"确定"** 退出。

## <a name="next-steps"></a>后续步骤

- [组的动态成员身份规则](groups-dynamic-membership.md)
