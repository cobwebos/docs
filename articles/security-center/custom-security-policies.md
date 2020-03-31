---
title: 在 Azure 安全中心创建自定义安全策略 |微软文档
description: Azure 自定义策略定义由 Azure 安全中心监视。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: memildin
ms.openlocfilehash: c709890ae6c57a001c6a0e9df4e973bd3bd24602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258254"
---
# <a name="using-custom-security-policies"></a>使用自定义安全策略

为了帮助保护系统和环境，Azure 安全中心生成安全建议。 这些建议基于行业最佳实践，这些最佳实践已纳入提供给所有客户的通用默认安全策略中。 它们也可以来自安全中心对行业和监管标准的了解。

使用此功能，您可以添加自己的*自定义*计划。 如果环境没有遵循您创建的策略，您将收到建议。 您创建的任何自定义计划都将[与教程中描述的](security-center-compliance-dashboard.md)法规遵从性仪表板中的内置计划一起出现。

如[Azure](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location)策略文档中所述，当您为自定义计划指定位置时，它必须是管理组或订阅。 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>向订阅添加自定义计划 

1. 从安全中心的侧边栏，打开**安全策略**页面。

1. 选择要向其添加自定义计划的订阅或管理组。

    [![选择要为其创建自定义策略的订阅](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > 您必须在订阅级别（或更高版本）添加自定义标准，才能在安全中心中评估和显示这些标准。 
    >
    > 添加自定义标准时，它将*计划*分配给该作用域。 因此，我们建议您选择该分配所需的最宽范围。

1. 在"安全策略"页中，在"自定义计划"下，单击"**添加自定义计划**"。

    [![单击 [添加自定义计划]](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    将显示以下页面：

    ![创建或添加策略](media/custom-security-policies/create-or-add-custom-policy.png)

1. 在"添加自定义计划"页中，查看组织中已创建的自定义策略的列表。 如果您看到要分配给订阅的订阅，请单击"**添加**"。 如果列表中没有满足您需求的计划，请跳过此步骤。

1. 要创建新的自定义计划，

    1. 单击 **"创建新**"。
    1. 输入定义的位置和名称。
    1. 选择要包含的策略，然后单击"**添加**"。
    1. 输入任何所需的参数。
    1. 单击“保存”。****
    1. 在"添加自定义计划"页中，单击刷新，您的新计划将显示为可用。
    1. 单击"**添加**"并将其分配给订阅。

    > [!NOTE]
    > 创建新计划需要订阅所有者凭据。 有关 Azure 角色的详细信息，请参阅[Azure 安全中心中的权限](security-center-permissions.md)。

    您的新计划生效，您可以通过以下两种方式看到影响：

    * 在安全中心侧边栏中，在策略&合规性下，选择**法规遵从性**。 合规性仪表板将打开，以显示您的新自定义计划以及内置计划。
    
    * 如果环境没有遵循您定义的策略，您将开始收到建议。

1. 要查看策略的结果建议，请单击侧边栏**中的建议**以打开建议页面。 建议将显示为"自定义"标签，大约在一小时内可用。

    [![自定义建议](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>后续步骤

在本文中，您学习了如何创建自定义安全策略。 

有关其他相关材料，请参阅以下文章： 

- [安全策略概述](tutorial-security-policy.md)
- [内置安全策略的列表](security-center-policy-definitions.md)