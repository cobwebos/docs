---
title: 验证动态组成员身份（预览）的规则 - Azure AD |微软文档
description: 如何根据 Azure 活动目录中的动态组的成员身份规则测试成员。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e081f62949f4cf3f7ae375b2c60ee5b032d974
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115516"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>在 Azure 活动目录中验证动态组成员身份规则（预览）

Azure 活动目录 （Azure AD） 现在提供了验证动态组规则的方法（在公共预览中）。 在"**验证规则"** 选项卡上，您可以针对示例组成员验证动态规则，以确认规则按预期工作。 创建或更新动态组规则时，管理员希望知道用户或设备是组的成员。 这有助于评估用户或设备是否符合规则条件，并有助于在不需要成员资格时进行故障排除。

## <a name="step-by-step-walk-through"></a>分步演练

要开始，请转到**Azure 活动目录** > **组**。 选择现有动态组或创建新的动态组，然后单击动态成员身份规则。 然后，您可以看到 **"验证规则"** 选项卡。

![查找"验证规则"选项卡，然后从现有规则开始](./media/groups-dynamic-rule-validation/validate-tab.png)

在 **"验证规则"** 选项卡上，您可以选择用户以验证其成员资格。 一次可以选择 20 个用户或设备。

![添加用户以验证现有规则](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

从选取器中选择用户或设备后，**选择**，验证将自动启动，并显示验证结果。

![查看规则验证结果](./media/groups-dynamic-rule-validation/validate-tab-results.png)

结果将判断用户是否是组的成员。 如果规则无效或存在网络问题，则结果将显示为 **"未知**"。 如果**未知**，详细的错误消息将描述问题和所需的操作。

![查看规则验证结果的详细信息](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

您可以修改规则，并触发成员资格的验证。 要查看用户为何不是组的成员，请单击"查看详细信息"，验证详细信息将显示组成规则的每个表达式的结果。 单击 **"确定"** 退出。

## <a name="next-steps"></a>后续步骤

- [组的动态成员资格规则](groups-dynamic-membership.md)
