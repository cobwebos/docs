---
title: 免除 Azure 安全中心安全建议和安全分数的资源
description: 了解如何根据安全建议和安全分数免除资源
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 87c16207f312479dcfe083ad9494d75b3538e18c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91532544"
---
# <a name="exempt-a-resource-from-recommendations-and-secure-score"></a>从建议和安全分数中免除资源

每个安全团队的核心优先级是尝试确保分析人员可以专注于涉及组织的任务和事件。 安全中心提供许多功能，可用于自定义你确定优先级最高的信息并确保安全分数是组织安全决策的有效反射。 豁免资源是一项功能。

调查 Azure 安全中心的安全建议时，所查看的第一条信息是受影响的资源的列表。

有时，将列出不应包含的资源。 它可能已被安全中心未跟踪的进程修正。 或者，你的组织可能已决定接受该特定资源的风险。 

在这种情况下，你可以创建例外规则，并确保将来不会列出资源和不正常的资源，并且不会影响安全分数。 

资源将列出为 "不适用"，原因将显示为 "已免除"，并显示所选的理由。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|预览|
|定价：|这是一种高级 Azure 策略功能，适用于无需额外费用的 Azure Defender 客户。 对于其他用户，将来可能会收取费用。|
|所需角色和权限：|用于创建例外的**订阅所有者**或**策略参与者**<br>若要创建规则，需要在 Azure 策略中编辑策略的权限。<br>有关详细信息，请参阅 azure [策略中的 AZURE RBAC 权限](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)。|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov）|
|||


## <a name="create-an-exemption-rule"></a>创建例外规则

1. 在不正常的资源列表中，选择要免除的资源的省略号菜单 ( "..." ) 。

    :::image type="content" source="./media/exempt-resource/create-exemption.png" alt-text="上下文菜单中的 Create 豁免选项&quot;:::

    此时将打开 &quot;创建例外" 窗格。

    :::image type="content" source="./media/exempt-resource/exemption-rule-options.png" alt-text="上下文菜单中的 Create 豁免选项&quot;:::

    此时将打开 &quot;创建例外" 页顶部的信息栏中列出了免除的资源的数目：
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="上下文菜单中的 Create 豁免选项&quot;:::

    此时将打开 &quot;创建例外" 选项卡。

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="上下文菜单中的 Create 豁免选项&quot;:::

    此时将打开 &quot;创建例外" ) 如 (2) 所示。


## <a name="review-all-of-the-exemption-rules-on-your-subscription"></a>查看订阅中的所有免除规则

例外规则使用 Azure 策略为策略分配上的资源创建例外。

可以使用 Azure 策略在 **豁免** 页中跟踪所有例外：

:::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="上下文菜单中的 Create 豁免选项&quot;:::

    此时将打开 &quot;创建例外":::



## <a name="next-steps"></a>后续步骤

本文介绍了如何从建议中免除资源，使其不会影响安全分数。 有关安全分数的详细信息，请参阅：

- [Azure 安全中心的安全评分](secure-score-security-controls.md)