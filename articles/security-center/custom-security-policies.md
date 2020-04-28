---
title: 在 Azure 安全中心创建自定义安全策略 |Microsoft Docs
description: Azure 安全中心监视的 azure 自定义策略定义。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: memildin
ms.openlocfilehash: d703ea38c39ed556102271ac0cf9a609ce449bc3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195912"
---
# <a name="using-custom-security-policies"></a>使用自定义安全策略

为了帮助保护你的系统和环境，Azure 安全中心会生成安全建议。 这些建议基于行业最佳实践，该方案合并到为所有客户提供的通用默认安全策略中。 它们还可以来自安全中心的行业和法规标准知识。

利用此功能，你可以添加自己的*自定义*计划。 如果你的环境不遵循你所创建的策略，则会收到建议。 你创建的任何自定义计划将显示在 "合规性" 仪表板中，如[本教程中](security-center-compliance-dashboard.md)所述。

如[Azure 策略文档](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location)中所述，当你为自定义计划指定位置时，它必须是管理组或订阅。 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>向订阅中添加自定义计划 

1. 在安全中心的边栏中，打开 "**安全策略**" 页。

1. 选择要向其添加自定义计划的订阅或管理组。

    [![选择要为其创建自定义策略的订阅](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > 你必须在订阅级别（或更高版本）添加自定义标准，以便在安全中心评估并显示它们。 
    >
    > 添加自定义标准时，它将向该范围分配*计划*。 因此，建议您选择该分配所需的范围最广的作用域。

1. 在 "安全策略" 页的 "自定义计划" 下，单击 "**添加自定义计划**"。

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
    1. 在 "添加自定义计划" 页中，单击 "刷新"。 新计划将显示为 "可用"。
    1. 单击 "**添加**" 并将其分配给你的订阅。

    > [!NOTE]
    > 创建新的计划需要订阅所有者凭据。 有关 Azure 角色的详细信息，请参阅[Azure 安全中心中的权限](security-center-permissions.md)。

    你的新计划将生效，并且你可以通过以下两种方式来查看影响：

    * 在 "安全中心" 边栏下的 "策略 & 符合性" 下，选择 "合规**性**"。 相容性仪表板将打开，显示新的自定义计划和内置计划。
    
    * 如果你的环境未遵循你定义的策略，你将开始收到建议。

1. 若要查看策略的结果建议，请单击边栏中的 "**建议**" 以打开 "建议" 页。 建议将显示 "自定义" 标签，在大约一小时内可用。

    [![自定义建议](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

## <a name="enhancing-your-custom-recommendations-with-detailed-information"></a>利用详细信息增强你的自定义建议

Azure 安全中心提供的内置建议包括严重性级别和更正说明等详细信息。 如果要将此类型的信息添加到自定义建议，使其显示在 Azure 门户或访问建议的任何位置，则需要使用 REST API。 

可以添加的两种类型的信息是：

- **RemediationDescription** – String
- **严重性**–枚举 [低、中、高]

应将元数据添加到作为自定义计划的一部分的策略的策略定义中。 它应位于 "securityCenter" 属性中，如下所示：

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High",
    },
```

下面是包括 metadata/securityCenter 属性的自定义策略的示例：

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "remediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "severity": "High",
        },
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

有关使用 securityCenter 属性的另一个示例，请参阅[REST API 文档中的此部分](https://docs.microsoft.com/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples)。


## <a name="next-steps"></a>后续步骤

本文介绍了如何创建自定义安全策略。 

有关其他相关材料，请参阅以下文章： 

- [安全策略概述](tutorial-security-policy.md)
- [内置安全策略的列表](security-center-policy-definitions.md)