---
title: 获取策略符合性数据
description: Azure Policy 的评估和效果确定了符合性。 了解如何获取符合性详细信息。
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 428a1614889409300064420e1d3d4fbc0423a0ec
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237530"
---
# <a name="get-compliance-data-of-azure-resources"></a>获取 Azure 资源的符合性数据

Azure Policy 的最大优势之一在于它针对订阅或订阅[管理组](../../management-groups/overview.md)中的资源提供的见解和控制度。 可通过许多不同的方式运用这种控制，例如，防止在错误的位置创建资源、强制实施常见且一致的标记用法，或者审核相应配置和设置的现有资源。 在所有情况下，数据生成的 Azure 策略来帮助你了解你的环境的符合性状态。

可通过多种方式访问策略和计划分配生成的符合性信息：

- 使用 [Azure 门户](#portal)
- 通过[命令行](#command-line)脚本

在探讨符合性报告方法之前，让我们了解符合性信息的更新时间和频率，以及触发评估周期的事件。

> [!WARNING]
> 如果符合性状态被报告为“未注册”  ，请验证是否已注册 **Microsoft.PolicyInsights** 资源提供程序，并验证用户是否具有适当的基于角色的访问控制 (RBAC) 权限，如 [Azure Policy 中的 RBAC](../overview.md#rbac-permissions-in-azure-policy) 所述。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="evaluation-triggers"></a>评估触发器

已完成的评估周期的结果通过 `PolicyStates` 和 `PolicyEvents` 操作在 `Microsoft.PolicyInsights` 资源提供程序中获取。 有关 Azure 策略见解 REST API 的操作的详细信息，请参阅[Azure 策略见解](/rest/api/policy-insights/)。

已分配的策略和计划的评估会在各种事件后发生：

- 最近已将策略或计划分配到某个范围。 需要大约花费 30 分钟将分配应用到定义的范围。 应用分配后，将会针对新分配的策略或计划，对该范围内的资源执行评估周期，同时，会根据策略或计划使用的效果，将资源标记为合规或不合规。 针对大范围的资源评估的大型策略或计划可能需要花费一段时间。 因此，在评估周期何时完成方面，无法预先定义预期目标。 完成评估后，更新的符合性结果会在门户和 SDK 中提供。

- 更新了已分配到某个范围的策略或计划。 此场景的评估周期和计时与新的范围分配相同。

- 资源将通过资源管理器、REST、Azure CLI 或 Azure PowerShell 部署到包含分配的范围。 在此场景中，个体资源的效果事件（追加、审核、拒绝、部署）和符合性状态将在大约 15 分钟后出现在门户与 SDK 中。 此事件不会导致对其他资源进行评估。

- 标准符合性评估周期。 分配每隔 24 小时自动重新评估一次。 涉及大量资源的大型策略或计划可能需要花费一段时间，因此，在评估周期何时完成方面，无法预先定义预期目标。 完成评估后，更新的符合性结果会在门户和 SDK 中提供。

- [来宾配置](../concepts/guest-configuration.md)资源提供程序更新受管理资源的符合性详细信息。

- 按需扫描

### <a name="on-demand-evaluation-scan"></a>按需评估扫描

可以通过调用 REST API 来启动订阅或资源组的评估扫描。 此扫描是一个异步过程。 因此，启动扫描的 REST 终结点不是等到扫描完成才能响应。 而是提供一个 URI，用于查询请求的评估的状态。

在每个 REST API URI 中，包含替换为自己的值所使用的变量：

- `{YourRG}` - 替换为资源组的名称
- `{subscriptionId}` - 替换为订阅 ID

扫描支持评估订阅或资源组中的资源。 使用以下 URI 结构，通过 REST API POST 命令开始按范围扫描  ：

- 订阅

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- 资源组

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

该调用返回“202 Accepted”状态  。 响应标头中包含 Location 属性，格式如下  ：

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

以静态方式为请求的范围生成了 `{ResourceContainerGUID}`。 如果某个范围已在运行按需扫描，则不会启动新扫描。 而是为新请求的状态提供相同的 `{ResourceContainerGUID}` 位置 URI  。 在评估过程中，位置 URI 的 REST API GET 命令返回“202 Accepted”状态    。 评估扫描完成后，返回“200 OK”状态  。 已完成的扫描的正文为 JSON 响应，其状态为：

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>符合性的工作原理

在分配中，如果某资源不符合策略或计划规则，则该资源不合规  。
下表显示了对于生成的符合性状态，不同的策略效果是如何与条件评估配合使用的：

| 资源状态 | 效果 | 策略评估 | 符合性状态 |
| --- | --- | --- | --- |
| Exists | Deny、Audit、Append\*、DeployIfNotExist\*、AuditIfNotExist\* | True | 不符合 |
| Exists | Deny、Audit、Append\*、DeployIfNotExist\*、AuditIfNotExist\* | False | 符合 |
| 新建 | Audit、AuditIfNotExist\* | True | 不符合 |
| 新建 | Audit、AuditIfNotExist\* | False | 符合 |

\*Append、DeployIfNotExist 和 AuditIfNotExist 效果要求 IF 语句为 TRUE。
这些效果还要求存在条件为 FALSE 才能将资源判定为不合规。 如果为 TRUE，则 IF 条件会触发相关资源存在条件的计算。

例如，假设有一个资源组 ContsoRG，其中包含一些向公共网络公开的存储帐户（以红色突出显示）。

![向公共网络公开的存储帐户](../media/getting-compliance-data/resource-group01.png)

在此示例中，需要慎重考虑安全风险。 创建策略分配后，将会针对 ContosoRG 资源组中的所有存储帐户评估该分配。 它对这三个不合规的存储帐户进行审核，并因此将其状态更改为“不合规”  。

![已审核不合规的存储帐户](../media/getting-compliance-data/resource-group03.png)

除“符合”和“不符合”外，政策和资源还有 3 种状态   ：

- **冲突**：两项或多项策略的规则存在冲突。 例如，两项策略向不同的值附加了相同的标记。
- **未启动**：尚未针对策略或资源启动评估周期。
- **未注册**：尚未注册 Azure Policy 资源提供程序，或者登录的帐户无权读取符合性数据。

使用 azure 策略**类型**并**名称**要确定某个资源是否匹配项的定义中的字段。 如果资源匹配，则被视为适用，状态为“符合”或“不符合”   。 如果“类型”或“名称”是定义中的唯一属性，则将所有资源视为适用并对其进行评估   。

符合百分比是合规资源与总资源之比   。
根据定义，总资源是指合规资源、不合规资源和冲突资源的总和     。 整体符合性是不同合规资源的总和除以所有唯一资源  。 在下图中，有 20 种不同的资源适用，只有一种资源“不合规”  。 因此，资源的整体符合性为 95%（19/20）。

![符合性页面上的策略符合性示例](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>门户

Azure 门户展示了一个图形体验用于可视化和了解环境中的符合性状态。 在“策略”页上，“概述”选项提供了策略和计划符合性的可用范围的详细信息。   除了符合性状态和每个分配的计数以外，该页还包含一个图表，显示过去七天的符合性。 “符合性”页包含上述大量相同信息（图表除外），但提供附加的筛选和排序选项。 

![Azure 策略符合性页的示例](../media/getting-compliance-data/compliance-page.png)

由于策略或计划可分配到不同的范围，因此表中包含每个分配的范围，以及分配的定义类型。 还提供每个分配项中不合规资源和不合规策略的数量。 单击表中的某个策略或计划可以更深入地了解该特定分配的符合性。

![Azure 策略符合性详细信息页示例](../media/getting-compliance-data/compliance-details.png)

“资源符合性”选项卡上的资源列表显示当前分配的现有资源的评估状态。  此选项卡默认为“不符合”，但是可以进行筛选。 
创建资源的请求所触发的事件（追加、审核、拒绝、部署）显示在“事件”选项卡下。 

![Azure 策略符合性事件的示例](../media/getting-compliance-data/compliance-events.png)

右键单击要收集其更多详细信息的事件所在的行，然后选择“显示活动日志”。  活动日志页将会打开，其中的搜索结果经过预先筛选，显示分配和事件的详细信息。 活动日志提供有关这些事件的其他上下文和信息。

![Azure 策略符合性活动日志的示例](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>了解非符合性

<a name="change-history-preview"></a>

当资源被确定为**符合**，有许多可能的原因。 若要确定资源的原因**不符合**或者若要查找负责更改，请参阅[确定不符合性](./determine-non-compliance.md)。

## <a name="command-line"></a>命令行

可以使用 REST API（包括使用 [ARMClient](https://github.com/projectkudu/ARMClient)）或 Azure PowerShell 来检索门户中提供的相同信息。 有关 REST API 的完整详细信息，请参阅[Azure 策略见解](/rest/api/policy-insights/)引用。 REST API 参考页上针对每个操作提供了一个绿色的“试用”按钮，使用该按钮可在浏览器中直接试用该操作。

若要在 Azure PowerShell 中使用以下示例，请使用此示例代码构造身份验证令牌。 然后，将示例中的 $restUri 替换为字符串，以检索随后可分析的 JSON 对象。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
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

使用 REST API 时，可以按容器、定义或分配进行汇总。 下面是在订阅级别使用 Azure 策略见解摘要的示例[汇总订阅](/rest/api/policy-insights/policystates/summarizeforsubscription):

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

在上面的示例中，**value.policyAssignments.policyDefinitions.results.queryResultsUri** 提供了一个示例 URI，用于特定策略定义的所有不符合资源。 查看 **$filter** 值，IsCompliant 等于 (eq) false，PolicyAssignmentId 是针对策略定义，然后针对 PolicyDefinitionId 本身指定的。 在筛选器中包含 PolicyAssignmentId 的原因是，PolicyDefinitionId 可能在具有不同范围的多个策略或计划分配中存在。 通过指定 PolicyAssignmentId 和 PolicyDefinitionId，可以明确指定想要查找的结果。 以前，我们使用了 **latest** 作为 PolicyStates，因此将**起始**和**截止**时间范围自动设置成了过去 24 小时。

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

为简洁起见，以下示例响应已被截断，只显示一个不符合资源。 详细响应包含有关资源、策略或计划以及分配的多个数据片段。 请注意，还可以查看已将哪些分配参数传递给了策略定义。

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

创建或更新资源时，将生成策略评估结果。 结果称为“策略事件”。  使用以下 URI 查看与订阅关联的最近策略事件。

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

有关查询策略事件的详细信息，请参阅[Azure 策略事件](/rest/api/policy-insights/policyevents)参考文章。

### <a name="azure-powershell"></a>Azure PowerShell

Azure 策略的 Azure PowerShell 模块与 PowerShell 库不可[Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights)。
使用 PowerShellGet，可以使用 `Install-Module -Name Az.PolicyInsights` 安装模块（请确保已安装了最新版 [Azure PowerShell](/powershell/azure/install-az-ps)）：

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

该模块拥有以下 cmdlet：

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

示例：获取不符合资源数最多的、最前面的已分配策略的状态摘要。

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

示例：获取最近评估的资源的状态记录（默认按时间戳的降序排序）。

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

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

示例：获取所有不符合虚拟网络资源的详细信息。

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

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

示例：获取在特定日期后发生的、与不符合虚拟网络资源相关的事件。

```azurepowershell-interactive
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

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

可以结合 Azure PowerShell cmdlet `Get-AzADUser` 使用 **PrincipalOid** 字段来获取特定的用户。 请将 **{principalOid}** 替换为在前一示例中获取的响应。

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure Monitor 日志

如果有[Log Analytics 工作区](../../../log-analytics/log-analytics-overview.md)与`AzureActivity`从[Activity Log Analytics 解决方案](../../../azure-monitor/platform/activity-log-collect.md)绑定到你的订阅，你还可以查看从评估周期中使用的非符合性结果简单的 Kusto 查询和`AzureActivity`表。 借助 Azure Monitor 日志中的详细信息，可对警报进行配置，以监视不符合情况。


![使用 Azure Monitor 日志的 azure 策略符合性](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>后续步骤

- 查看示例[Azure 策略示例](../samples/index.md)。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何[以编程方式创建策略](programmatically-create.md)。
- 了解如何[修正的不合规资源](remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。