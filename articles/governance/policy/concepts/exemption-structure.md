---
title: 策略例外结构的详细信息
description: 介绍 Azure 策略使用的策略免除定义，使资源免于评估计划或定义。
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: e6ced56c1dc65ca68998c5c58d3e985b63873e0b
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950170"
---
# <a name="azure-policy-exemption-structure"></a>Azure 策略免除结构

Azure 策略免除 (预览版) 功能用于从计划或定义的评估中 _免除_ 资源层次结构或单个资源。 不计入总体符合性，但不能评估或临时 _弃权的资源_ 。 有关详细信息，请参阅 [了解 Azure 策略中的作用域](./scope.md)。 Azure 策略免除仅适用于 [资源管理器模式](./definition-structure.md#resource-manager-modes) ，不适用于 [资源提供程序模式](./definition-structure.md#resource-provider-modes)。

> [!IMPORTANT]
> 此功能在 **预览**期间是免费的。 有关定价详细信息，请参阅 [Azure 策略定价](https://azure.microsoft.com/pricing/details/azure-policy/)。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 JSON 创建策略例外。 策略例外包含的元素：

- 显示名称
- description
- metadata
- 策略分配
- 计划中的策略定义
- 豁免类别
- expiration

> [!NOTE]
> 策略例外创建为资源层次结构上的子对象或授予免除的单个资源，因此目标不会包含在免除定义中。

例如，下面的 JSON 在名为的计划分配的资源的 **弃权** 类别中显示策略例外 `resourceShouldBeCompliantInit` 。 资源仅从计划中的两个策略定义中 _免除_ ， `customOrgPolicy` 自定义策略定义 (引用 `requiredTags`) 和 "允许位置" 内置策略定义 (ID： `e56962a6-4747-49cd-b67b-bf8b01975c4c` ，引用 `allowedLocations`) ：

```json
{
    "id": "/subscriptions/{subId}/resourceGroups/ExemptRG/providers/Microsoft.Authorization/policyExemptions/resourceIsNotApplicable",
    "name": "resourceIsNotApplicable",
    "type": "Microsoft.Authorization/policyExemptions",
    "properties": {
        "displayName": "This resource is scheduled for deletion",
        "description": "This resources is planned to be deleted by end of quarter and has been granted a waiver to the policy.",
        "metadata": {
            "requestedBy": "Storage team",
            "approvedBy": "IA",
            "approvedOn": "2020-07-26T08:02:32.0000000Z",
            "ticketRef": "4baf214c-8d54-4646-be3f-eb6ec7b9bc4f"
        },
        "policyAssignmentId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyAssignments/resourceShouldBeCompliantInit",
        "policyDefinitionReferenceIds": [
            "requiredTags",
            "allowedLocations"
        ],
        "exemptionCategory": "waiver",
        "expiresOn": "2020-12-31T23:59:00.0000000Z"
    }
}
```

具有策略例外使用的匹配的相关计划的代码段 `policyDefinitionReferenceIds` ：

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/customOrgPolicy",
        "policyDefinitionReferenceId": "requiredTags",
        "parameters": {
            "reqTags": {
                "value": "[parameters('init_reqTags')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
        "policyDefinitionReferenceId": "allowedLocations",
        "parameters": {
            "listOfAllowedLocations": {
                "value": "[parameters('init_listOfAllowedLocations')]"
            }
        }
    }
]
```

## <a name="display-name-and-description"></a>显示名称和说明

使用 **displayName** 和 **description** 标识策略例外，并为其使用特定资源提供上下文。 **displayName** 的最大长度为 128 个字符，**description** 的最大长度为 512 个字符。

## <a name="metadata"></a>Metadata

**Metadata**属性允许创建存储相关信息所需的任何子属性。 在上面的示例中，属性 **event.request.requestedby.displayname**、 **approvedBy**、 **approvedOn**和 **ticketRef** 包含客户值，以提供有关请求豁免的人员、批准该豁免的人员、时间以及请求的内部跟踪票证的信息。 这些 **元数据** 属性为示例，但不是必需的，并且 **元数据** 不限于这些子属性。

## <a name="policy-assignment-id"></a>策略分配 ID

此字段必须是策略分配或计划分配的完整路径名称。
`policyAssignmentId` 是字符串，而不是数组。 此属性定义父资源层次结构或单个资源的 _免除_ 源分配。

## <a name="policy-definition-ids"></a>策略定义 Id

如果 `policyAssignmentId` 适用于计划分配，则 `policyDefinitionReferenceIds` 可以使用属性来指定在主题资源对其具有豁免的计划中 () 的策略定义。 由于可以从包含的一个或多个策略定义中免除资源，因此此属性是一个 _数组_。 这些值必须与字段的计划定义中的值匹配 `policyDefinitions.policyDefinitionReferenceId` 。

## <a name="exemption-category"></a>豁免类别

存在两个例外类别，并用于将免除分组：

- **缓解**：授予免除，因为策略意向是通过另一种方法实现的。
- **弃权**：已授予免除，因为暂时接受资源的不符合性状态。 使用此类别的另一个原因是，应该排除在某个计划中的一个或多个定义中的资源或资源层次结构，但不能将其从整个计划中排除。

## <a name="expiration"></a>过期时间

若要设置资源层次结构或单个资源何时不再 _豁免_ 分配，请设置 `expiresOn` 属性。 此可选属性必须采用通用 ISO 8601 DateTime 格式 `yyyy-MM-ddTHH:mm:ss.fffffffZ` 。

> [!NOTE]
> 到达日期时，不会删除策略例外 `expiresOn` 。 保留对象用于记录保留，但免除不再有效。

## <a name="required-permissions"></a>所需的权限

管理策略例外对象所需的 Azure RBAC 权限位于 `Microsoft.Authorization/policyExemptions` 操作组中。 内置角色 [资源策略参与者](../../../role-based-access-control/built-in-roles.md#resource-policy-contributor) 和 [安全管理员](../../../role-based-access-control/built-in-roles.md#security-admin) 都具有 `read` 和 `write` 权限，并且 [ (预览) ](../../../role-based-access-control/built-in-roles.md#policy-insights-data-writer-preview) 具有 `read` 权限。

免除包含附加的安全措施，因为这会影响对豁免的授权。 除了要求 `Microsoft.Authorization/policyExemptions/write` 对资源层次结构或单个资源的操作，例外的创建者必须在 `exempt/Action` 目标分配上具有谓词。

## <a name="next-steps"></a>后续步骤

- 了解[策略定义结构](./definition-structure.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取符合性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。