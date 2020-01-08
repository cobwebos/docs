---
title: 在 Azure 安全中心创建自定义安全策略 |Microsoft Docs
description: Azure 安全中心监视的 azure 自定义策略定义。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 34dbace304ccf70891ef53dd768de60d87e26967
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666629"
---
# <a name="using-custom-security-policies-preview"></a>使用自定义安全策略（预览）

为了帮助保护你的系统和环境，Azure 安全中心会生成安全建议。 这些建议基于行业最佳实践，该方案合并到为所有客户提供的通用默认安全策略中。 它们还可以来自安全中心的行业和法规标准知识。

通过此预览功能，你可以添加自己的*自定义*计划。 如果你的环境不遵循你所创建的策略，则会收到建议。 你创建的任何自定义计划将与本教程中所述的法规符合性仪表板中的内置计划一起显示，从而[提高你的法规遵从性](security-center-compliance-dashboard.md)。

如 Azure 策略文档中[所述，](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location)当你为自定义计划指定位置时，它必须是管理组或订阅。 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>向订阅中添加自定义计划 

1. 在安全中心的边栏中，打开 "**安全策略**" 页。

1. 选择要向其添加自定义计划的订阅或管理组。

    [选择要为其创建自定义策略的订阅 ![](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > 你必须在订阅级别（或更高版本）添加自定义标准，以便在安全中心评估并显示它们。 
    >
    > 添加自定义标准时，它将向该范围分配*计划*。 因此，建议您选择该分配所需的范围最广的作用域。

1. 在 "安全策略" 页的 "自定义计划（预览）" 下，单击 "**添加自定义计划**"。

    [![单击 "添加自定义计划"](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    将显示以下页面：

    ![创建或添加策略](media/custom-security-policies/create-or-add-custom-policy.png)

1. 在 "添加自定义计划" 页上，查看已在组织中创建的自定义策略的列表。 如果你看到要分配给订阅的一个，请单击 "**添加**"。 如果列表中没有符合需求的计划，则跳过此步骤。

1. 若要创建新的自定义计划：

    1. 单击 "**新建**"。
    1. 输入定义的 "位置" 和 "名称"。
    1. 选择要包括的策略，然后单击 "**添加**"。
    1. 输入任何所需的参数。
    1. 单击“ **保存**”。
    1. 在 "添加自定义计划" 页中，单击 "刷新"，你的新计划将显示为 "可用"。
    1. 单击 "**添加**" 并将其分配给你的订阅。

    > [!NOTE]
    > 创建新的计划需要订阅所有者凭据。 有关 Azure 角色的详细信息，请参阅[Azure 安全中心中的权限](security-center-permissions.md)。

    你的新计划将生效，并且你可以通过以下两种方式来查看影响：

    * 在 "安全中心" 边栏下的 "策略 & 符合性" 下，选择 "合规**性**"。 相容性仪表板将打开，显示新的自定义计划和内置计划。
    
    * 如果你的环境未遵循你定义的策略，你将开始收到建议。

1. 若要查看策略的结果建议，请单击边栏中的 "**建议**" 以打开 "建议" 页。 建议将显示 "自定义" 标签，在大约一小时内可用。

    [![自定义建议](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>后续步骤

本文介绍了如何创建自定义安全策略。 

有关其他相关材料，请参阅以下文章： 

- [安全策略概述](tutorial-security-policy.md)
- [内置安全策略的列表](security-center-policy-definitions.md)