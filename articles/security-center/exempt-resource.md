---
title: 免除 Azure 安全中心安全建议和安全分数的资源
description: 了解如何根据安全建议和安全分数免除资源
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: f3ac2f82882dfd7eb1e15e9a5287cf1d6f5b32d2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301527"
---
# <a name="exempt-a-resource-from-recommendations-and-secure-score"></a>从建议和安全分数中免除资源

每个安全团队的核心优先级是尝试确保分析人员可以专注于涉及组织的任务和事件。 安全中心提供许多功能，可用于自定义你确定优先级最高的信息并确保安全分数是组织安全决策的有效反射。 豁免资源是一项功能。

调查 Azure 安全中心的安全建议时，所查看的第一条信息是受影响的资源的列表。

有时，将列出不应包含的资源。 它可能已被安全中心未跟踪的进程修正。 或者，您的组织可能决定接受该特定资源的风险。 

在这种情况下，你可以创建例外规则，并确保将来不会列出资源和不正常的资源，并且不会影响安全分数。 

资源将列出为 "不适用"，原因将显示为 "已免除"，并显示所选的理由。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|预览|
|定价：|这是一种高级 Azure 策略功能，适用于无需额外费用的 Azure Defender 客户。 对于其他用户，将来可能会收取费用。|
|所需角色和权限：|用于创建例外的**订阅所有者**或**策略参与者**<br>若要创建规则，需要在 Azure 策略中编辑策略的权限。<br>有关详细信息，请参阅 [Azure 策略中的 RBAC 权限](../governance/policy/overview.md#rbac-permissions-in-azure-policy)。|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||


## <a name="create-an-exemption-rule"></a>创建例外规则

1. 在不正常的资源列表中，选择要免除的资源的省略号菜单 ( "..." ) 。

    :::image type="content" source="./media/exempt-resource/create-exemption.png" alt-text="上下文菜单中的 Create 豁免选项":::

    此时将打开 "创建例外" 窗格。

    :::image type="content" source="./media/exempt-resource/exemption-rule-options.png" alt-text="创建例外窗格":::

1. 输入条件，并选择有关为何应免除此资源的条件：
    - 已**缓解**-此问题与资源无关，因为它已由不同于所建议的工具或进程处理
    - **弃权** -接受此资源的风险
1. 选择“保存”。
1. 一段时间后 (可能需要长达24小时) ：
    - 该资源不会影响安全分数。
    - "建议详细信息" 页的 " **不适用** " 选项卡中列出了该资源
    - "建议详细信息" 页顶部的信息栏中列出了免除的资源的数目：
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="免除的资源数":::

1. 若要查看免除的资源，请打开 " **不适用** " 选项卡。

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="修改例外":::

    每个例外的原因包括在表 (1) 中。

    若要修改或删除例外，请选择省略号菜单 ( "..." ) 如 (2) 所示。


## <a name="review-all-of-the-exemption-rules-on-your-subscription"></a>查看订阅中的所有免除规则

例外规则使用 Azure 策略为策略分配上的资源创建例外。

可以使用 Azure 策略在 **豁免** 页中跟踪所有例外：

:::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure 策略的豁免页":::



## <a name="next-steps"></a>后续步骤

本文介绍了如何从建议中免除资源，使其不会影响安全分数。 有关安全分数的详细信息，请参阅：

- [Azure 安全中心的安全评分](secure-score-security-controls.md)