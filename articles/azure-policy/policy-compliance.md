---
title: 在 Azure 策略中获取符合性数据
description: Azure 策略的评估和效果确定了符合性。 了解如何获取符合性详细信息。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 390935d80e903631287b1a4b9f1075e547298d99
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39250139"
---
# <a name="getting-compliance-data"></a>获取符合性数据

Azure 策略的最大优势之一在于它针对订阅或订阅[管理组](../azure-resource-manager/management-groups-overview.md)中的资源提供的见解和控制度。 可通过许多不同的方式运用这种控制，例如，防止在错误的位置创建资源、强制实施常见且一致的标记用法，或者审核相应配置和设置的现有资源。 在所有情况下，数据都由策略生成，使你能够了解环境的符合性状态。

可通过多种方式访问策略和计划分配生成的符合性信息：

- 使用 [Azure 门户](#portal)
- 通过[命令行](#command_line)脚本

在探讨符合性报告方法之前，让我们了解符合性信息的更新时间和频率，以及触发评估周期的事件。

## <a name="evaluation-triggers"></a>评估触发器

已完成的评估周期的结果通过 `PolicyStates` 和 `PolicyEvents` 操作反映在 `Microsoft.PolicyInsights` 资源提供程序中。 有关策略见解 REST API 的选项和功能的详细信息，请参阅[策略见解](/rest/api/policy-insights/)。

已分配的策略和计划的评估会在各种事件后发生：

- 最近已将策略或计划分配到某个范围。 发生这种情况时，需要大约花费 30 分钟将分配应用到定义的范围。 应用分配后，将会针对新分配的策略或计划，对该范围内的资源执行评估周期，同时，会根据策略或计划使用的效果，将资源标记为合规或不合规。 针对大范围的资源评估的大型策略或计划可能需要花费一段时间，因此，在评估周期何时完成方面，无法预先定义预期目标。 完成评估后，更新的符合性结果会在门户和 SDK 中提供。
- 更新了已分配到某个范围的策略或计划。 此场景的评估周期和计时与新的范围分配相同。
- 资源将通过资源管理器、REST、Azure CLI 或 Azure PowerShell 部署到包含分配的范围。 在此场景中，效果事件（追加、审核、拒绝、部署）和符合性状态将在大约 15 分钟后出现在门户与 SDK 中。
- 标准符合性评估周期。 分配每隔 24 小时自动重新评估一次。 针对大范围的资源评估的大型策略或计划可能需要花费一段时间，因此，在评估周期何时完成方面，无法预先定义预期目标。 完成评估后，更新的符合性结果会在门户和 SDK 中提供。

## <a name="how-compliance-works"></a>符合性的工作原理

在分配中，如果某个资源不遵循策略或计划规则，则该资源不合规。 下表显示了对于生成的符合性状态，不同的策略效果是如何与条件评估配合使用的：

| 资源状态 | 效果 | 策略评估 | 符合性状态 |
| --- | --- | --- | --- |
| Exists | Deny、Audit、Append\*、DeployIfNotExist\*、AuditIfNotExist\* | True | 不合规 |
| Exists | Deny、Audit、Append\*、DeployIfNotExist\*、AuditIfNotExist\* | False | 符合 |
| 新建 | Audit、AuditIfNotExist\* | True | 不合规 |
| 新建 | Audit、AuditIfNotExist\* | False | 符合 |

\*Append、DeployIfNotExist 和 AuditIfNotExist 效果要求 IF 语句为 TRUE。
这些效果还要求存在条件为 FALSE 才能将资源判定为不合规。 如果为 TRUE，则 IF 条件会触发相关资源存在条件的计算。

为了更好地理解如何将资源标记为不合规，让我们使用前面创建的策略分配示例。

例如，假设有一个资源组 ContsoRG，其中包含一些向公共网络公开的存储帐户（以红色突出显示）。

![向公共网络公开的存储帐户](media/policy-insights/resource-group01.png)

在此示例中，需要慎重考虑安全风险。 创建策略分配后，将会针对 ContosoRG 资源组中的所有存储帐户评估该分配。 系统会审核三个不合规的存储帐户，因而将其状态更改为“不合规”。

![已审核不合规的存储帐户](media/policy-insights/resource-group03.png)

## <a name="portal"></a>门户

Azure 门户展示了一个图形体验用于可视化和了解环境中的符合性状态。 在“策略”页上，“概述”选项提供了策略和计划符合性的可用范围的详细信息。 除了符合性状态和每个分配的计数以外，该页还包含一个图表，显示过去七天的符合性。 “符合性”页包含上述大量相同信息（图表除外），但提供附加的筛选和排序选项。

![策略符合性页](media/policy-compliance/compliance-page.png)

由于策略或计划可分配到不同的范围，因此在表中请注意每个分配的范围，以及分配到该范围的定义类型。 此页还提供每个分配的不合规性策略和不合规资源数目。 单击表中的某个策略或计划可以更深入地了解该特定分配的符合性。

![策略符合性详细信息](media/policy-compliance/compliance-details.png)

“不合规资源”选项卡上的资源列表反映了当前分配的现有资源的评估状态，而创建资源的请求所触发的事件（追加、审核、拒绝、部署）则显示在“事件”选项卡下。

![策略符合性事件](media/policy-compliance/compliance-events.png)

右键单击要收集其更多详细信息的事件所在的行，然后选择“显示活动日志”。 活动日志页将会打开，其中的搜索结果经过预先筛选，显示分配和事件的详细信息。 活动日志提供有关这些事件的其他上下文和信息。

![策略符合性活动日志](media/policy-compliance/compliance-activitylog.png)

## <a name="command-line"></a>命令行

可以直接使用 REST API（包括使用 [ARMClient](https://github.com/projectkudu/ARMClient)），或者结合 REST API 使用 Azure PowerShell，来检索门户中提供的相同信息。 有关 REST API 的完整详细信息，请参阅[策略见解](/rest/api/policy-insights/)参考文章。 REST API 参考页上针对每个操作提供了一个绿色的“试用”按钮，使用该按钮可在浏览器中直接试用该操作。

若要在 Azure PowerShell 中使用以下示例，请使用此示例代码构造身份验证令牌。 然后，将示例中的 $restUri 替换为所需的字符串，以检索随后可分析的 JSON 对象。

```azurepowershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

$azContext = Get-AzureRmContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Define the REST API to communicate with
# Use double quotes for $restUri as some endpoints take strings passed in single quotes
$restUri = "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04"

# Invoke the REST API
$response = Invoke-RestMethod -Uri $restUri -Method POST -Headers $authHeader

# View the response object (as JSON)
$response
```

### <a name="summarize-results"></a>汇总结果

使用 REST API 可按管理组、订阅、资源组、资源、计划、策略、订阅级分配或资源组级分配执行汇总。 下面是使用策略见解的[按订阅汇总](/rest/api/policy-insights/policystates/summarizeforsubscription)功能在订阅级别执行的汇总示例:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

输出将汇总订阅。 在以下示例输出中，汇总的符合性位于 **value.results.nonCompliantResources** 和 **value.results.nonCompliantPolicies** 下面。 此请求提供更多详细信息，包括构成不合规数的每个分配，以及每个分配的定义信息。 层次结构中的每个策略对象提供一个可用于获取该级别的更多详细信息的 **queryResultsUri**。

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>查询资源

沿用上面的示例，**value.policyAssignments.policyDefinitions.results.queryResultsUri** 为我们提供了一个示例 URI 用于获取特定策略定义的所有不合规资源。 查看 **$filter** 值，IsCompliant 等于 (eq) false，PolicyAssignmentId 是针对策略定义，然后针对 PolicyDefinitionId 本身指定的。
在筛选器中包含 PolicyAssignmentId 的原因是，PolicyDefinitionId 可能在具有各种范围的多个策略或计划分配中存在。 通过指定 PolicyAssignmentId 和 PolicyDefinitionId，可以明确指定想要查找的结果。 前面我们对 PolicyStates 使用了 **latest**（只允许在“按订阅汇总”运算符中为 **policyStatesSummaryResource** 使用此值），因此将**起始**和**截止**时间范围自动设置成了过去 24 小时。

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

为简洁起见，以下示例响应已被截断，只显示一个不合规资源（请注意，@odata.count 实际上是 15，它与上述示例中的不合规资源计数相匹配）。 详细响应提供了有关资源、策略（或计划）和分配的多个数据片段。 请注意，还可以查看已将哪些分配参数传递给了策略定义。

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>查看事件

创建或更新资源时，将生成策略评估结果。 结果称为“策略事件”。 使用以下 URI 查看与订阅关联的最近策略事件。

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

结果应如以下示例所示：

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

有关查询策略事件的详细信息，请参阅[策略事件](/rest/api/policy-insights/policyevents)参考文章。

### <a name="azure-powershell"></a>Azure PowerShell

适用于策略的 Azure PowerShell 模块在 PowerShell 库中以 [AzureRM.PolicyInsights](https://www.powershellgallery.com/packages/AzureRM.PolicyInsights) 的形式提供。 使用 PowerShellGet，可以使用 `Install-Module -Name AzureRM.PolicyInsights` 安装模块（请确保已安装了最新版 [Azure PowerShell](/powershell/azure/install-azurerm-ps)）：

```powershell
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name AzureRM.PolicyInsights

# Import the downloaded module
Import-Module AzureRM.PolicyInsights

# Login with Connect-AzureRmAccount if not using Cloud Shell
Connect-AzureRmAccount
```

该模块包含三个 cmdlet：

- `Get-AzureRmPolicyStateSummary`
- `Get-AzureRmPolicyState`
- `Get-AzureRmPolicyEvent`

示例：获取不合规资源数最多的、最前面的已分配策略的状态摘要。

```powershell
PS > Get-AzureRmPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

示例：获取最近评估的资源的状态记录（默认按时间戳的降序排序）。

```powershell
PS > Get-AzureRmPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

示例：获取所有不合规虚拟网络资源的详细信息。

```powershell
PS > Get-AzureRmPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

示例：获取在特定日期后发生的、与不合规虚拟网络资源相关的事件。

```powershell
PS > Get-AzureRmPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

可以结合 Azure PowerShell cmdlet `Get-AzureRmADUser` 使用 **PrincipalOid** 字段来获取特定的用户。 请将 **{principalOid}** 替换为在前一示例中获取的响应。

```powershell
PS > (Get-AzureRmADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="log-analytics"></a>Log Analytics

如果已将包含 `AzureActivity` 解决方案的 [Log Analytics](../log-analytics/log-analytics-overview.md) 工作区绑定到订阅，则还可以使用简单的 Kusto 查询和 `AzureActivity` 表，查看评估周期中的不合规结果。 如果 Log Analytics 中出现不合规详细信息，则也意味着可以配置警报来根据特定的资源、资源组甚至不合规项数的阈值（例如，在过去 24 小时内超过 10 个）来监视不合规情况。

![使用 Log Analytics 获取策略符合性](media/policy-compliance/compliance-loganalytics.png)

## <a name="next-steps"></a>后续步骤

- 查看[策略定义结构](policy-definition.md)。
- 查看[了解策略效果](policy-effects.md)。
- 参阅[使用 Azure 管理组来组织资源](../azure-resource-manager/management-groups-overview.md)，了解什么是管理组