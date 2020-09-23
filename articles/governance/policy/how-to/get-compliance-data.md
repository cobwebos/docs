---
title: 获取策略符合性数据
description: Azure Policy 的评估和效果确定了符合性。 了解如何获取 Azure 资源的符合性详细信息。
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2ab75bdab0dcf910da91eb60b5f0cf23892d6c51
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90895426"
---
# <a name="get-compliance-data-of-azure-resources"></a>获取 Azure 资源的符合性数据

Azure Policy 的最大优势之一在于它针对订阅或订阅[管理组](../../management-groups/overview.md)中的资源提供的见解和控制度。 可通过许多不同的方式运用这种控制，例如，防止在错误的位置创建资源、强制实施常见且一致的标记用法，或者审核相应配置和设置的现有资源。 在所有情况下，数据都由 Azure Policy 生成，使你能够了解环境的符合性状态。

可通过多种方式访问策略和计划分配生成的符合性信息：

- 使用 [Azure 门户](#portal)
- 通过[命令行](#command-line)脚本

在探讨符合性报告方法之前，让我们了解符合性信息的更新时间和频率，以及触发评估周期的事件。

> [!WARNING]
> 如果符合性状态被报告为“未注册”，请验证是否已注册 Microsoft.PolicyInsights 资源提供程序，并验证用户是否具有适当的基于角色的访问控制 (RBAC) 权限，如 [Azure Policy 中的 RBAC](../overview.md#rbac-permissions-in-azure-policy) 所述。

## <a name="evaluation-triggers"></a>评估触发器

已完成的评估周期的结果通过 `PolicyStates` 和 `PolicyEvents` 操作在 `Microsoft.PolicyInsights` 资源提供程序中获取。 有关 [Azure Policy Insights](/rest/api/policy-insights/) REST API 操作的详细信息，请参阅 。

已分配的策略和计划的评估会在各种事件后发生：

- 最近已将策略或计划分配到某个范围。 需要大约花费 30 分钟将分配应用到定义的范围。 应用后，评估周期将从该范围内的资源开始，并与新分配的策略或计划有关，并根据策略或计划所用的影响，将资源标记为符合、不符合或豁免。 针对大范围的资源评估的大型策略或计划可能需要花费一段时间。 因此，在评估周期何时完成方面，无法预先定义预期目标。 完成评估后，更新的符合性结果会在门户和 SDK 中提供。

- 更新了已分配到某个范围的策略或计划。 此场景的评估周期和计时与新的范围分配相同。

- 资源将通过 Azure 资源管理器、REST API 或受支持的 SDK 部署到包含分配的范围或在其中进行更新。 在此场景中，个体资源的效果事件（追加、审核、拒绝、部署）和符合性状态将在大约 15 分钟后出现在门户与 SDK 中。 此事件不会导致对其他资源进行评估。

- 创建、更新或删除 [策略例外](../concepts/exemption-structure.md) 。 在此方案中，将为定义的免除范围计算相应的分配。

- 标准符合性评估周期。 分配每隔 24 小时自动重新评估一次。 涉及大量资源的大型策略或计划可能需要花费一段时间，因此，在评估周期何时完成方面，无法预先定义预期目标。 完成评估后，更新的符合性结果会在门户和 SDK 中提供。

- [来宾配置](../concepts/guest-configuration.md)资源提供程序更新受管理资源的符合性详细信息。

- 按需扫描

### <a name="on-demand-evaluation-scan"></a>按需评估扫描

可以通过 Azure CLI,、Azure PowerShell 或调用 REST API 来启动订阅或资源组的评估扫描。 此扫描是一个异步过程。

#### <a name="on-demand-evaluation-scan---azure-cli"></a>按需评估扫描 - Azure CLI

符合性扫描从 [az policy state trigger-scan](/cli/azure/policy/state#az-policy-state-trigger-scan) 命令开始。

默认情况下，`az policy state trigger-scan` 开始评估当前订阅中的所有资源。 若要对特定资源组开始评估，请使用 resource-group 参数。 以下示例启动对 MyRG 资源组的当前订阅的符合性扫描：

```azurecli-interactive
az policy state trigger-scan --resource-group "MyRG"
```

可以选择使用 no-wait 参数在继续之前不等待异步过程完成。

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>按需评估扫描 - Azure PowerShell

符合性扫描从 [Start-AzPolicyComplianceScan](/powershell/module/az.policyinsights/start-azpolicycompliancescan) cmdlet 开始。

默认情况下，`Start-AzPolicyComplianceScan` 开始评估当前订阅中的所有资源。 若要对特定资源组进行评估，请使用 ResourceGroupName 参数。 以下示例启动对 MyRG 资源组的当前订阅的符合性扫描：

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName 'MyRG'
```

你可以让 PowerShell 在提供结果输出前等待完成异步调用，或者将其作为[作业](/powershell/module/microsoft.powershell.core/about/about_jobs)在后台运行。 若要通过 PowerShell 作业在后台运行符合性扫描，请使用“Asob”参数，并将值设置为对象，例如本示例中的 `$job`：

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

你可以通过检查 `$job` 对象来检查作业的状态。 作业类型为 `Microsoft.Azure.Commands.Common.AzureLongRunningJob`。 使用 `$job` 对象上的 `Get-Member` 查看可用的属性和方法。

在运行符合性扫描时，检查 `$job` 对象会输出结果，如下所示：

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

符合性扫描完成后，“状态”属性更改为“已完成”。

#### <a name="on-demand-evaluation-scan---rest"></a>按需评估扫描 - REST

作为异步进程，启动扫描的 REST 终结点不是等到扫描完成才能响应。 而是提供一个 URI，用于查询请求的评估的状态。

在每个 REST API URI 中，包含替换为自己的值所使用的变量：

- `{YourRG}` - 替换为资源组的名称
- `{subscriptionId}` - 替换为订阅 ID

扫描支持评估订阅或资源组中的资源。 使用以下 URI 结构，通过 REST API POST 命令开始按范围扫描：

- 订阅

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- 资源组

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

该调用返回“202 Accepted”状态。 响应标头中包含 Location 属性，格式如下：

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

以静态方式为请求的范围生成了 `{ResourceContainerGUID}`。 如果某个范围已在运行按需扫描，则不会启动新扫描。 而是为新请求的状态提供相同的 `{ResourceContainerGUID}` 位置 URL。 在评估过程中，位置 URI 的 REST API GET 命令返回“202 Accepted”状态  。 评估扫描完成后，返回“200 OK”状态。 已完成的扫描的正文为 JSON 响应，其状态为：

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>符合性的工作原理

在分配中，如果资源不遵循策略或计划规则并且不_例外_，则该资源不**合规**。 下表显示了对于生成的符合性状态，不同的策略效果是如何与条件评估配合使用的：

| 资源状态 | 效果 | 策略评估 | 符合性状态 |
| --- | --- | --- | --- |
| Exists | Deny、Audit、Append\*、DeployIfNotExist\*、AuditIfNotExist\* | True | 不符合 |
| Exists | Deny、Audit、Append\*、DeployIfNotExist\*、AuditIfNotExist\* | False | 符合 |
| 新建 | Audit、AuditIfNotExist\* | True | 不符合 |
| 新建 | Audit、AuditIfNotExist\* | False | 符合 |

\* Modify、Append、DeployIfNotExist 和 AuditIfNotExist 效果要求 IF 语句为 TRUE。 这些效果还要求存在条件为 FALSE 才能将资源判定为不合规。 如果为 TRUE，则 IF 条件会触发相关资源存在条件的计算。

例如，假设有一个资源组 ContsoRG，其中包含一些向公共网络公开的存储帐户（以红色突出显示）。

:::image type="complex" source="../media/getting-compliance-data/resource-group01.png" alt-text="向 Contoso R G 资源组中的公用网络公开的存储帐户关系图。" border="false":::
   显示 Contoso R G 资源组中5个存储帐户的图像的关系图。  存储帐户1和3为蓝色，而存储帐户有两个、四个和五个为红色。
:::image-end:::

在此示例中，需要慎重考虑安全风险。 创建策略分配后，将针对 ContosoRG 资源组中的所有包含和非例外存储帐户对其进行评估。 它对这三个不合规的存储帐户进行审核，并因此将其状态更改为“不合规”。

:::image type="complex" source="../media/getting-compliance-data/resource-group03.png" alt-text="Contoso R G 资源组中的存储帐户符合性关系图。" border="false":::
   显示 Contoso R G 资源组中5个存储帐户的图像的关系图。 现在有一个和三个存储帐户，它们下面有绿色复选标记，而存储帐户有两个、四个和五个，现在它们下面有红色警告符号。
:::image-end:::

除了 **符合** 和 **不符合要求**以外，策略和资源具有四种其他状态：

- **豁免**：资源在分配范围内，但具有 [定义的例外](../concepts/exemption-structure.md)。
- **冲突**：存在两个或多个策略定义，但规则冲突。 例如，两个定义将同一标记追加到不同的值。
- **未启动**：尚未针对策略或资源启动评估周期。
- **未注册**：尚未注册 Azure Policy 资源提供程序，或者登录的帐户无权读取符合性数据。

Azure Policy 使用定义中的“类型”和“名称”字段来确定资源是否匹配 。 当资源匹配时，它将被视为适用，并且状态为 " **符合**"、" **不符合**" 或 " **例外**"。 如果 **类型** 或 **名称** 是定义中的唯一属性，则所有包含的和非免除的资源都将被视为适用并进行评估。

合规性百分比由资源_总量_的**符合**和**免除**资源来确定。 _资源总量_ 定义为 **符合**、 **不符合**、 **例外**和 **冲突** 资源的总和。 总体符合性数字是 **符合** 或 **免除** 的不同资源的总和除以所有不同资源的总和。 在下图中，有 20 种不同的资源适用，只有一种资源“不合规”。
因此，资源的整体符合性为 95%（19/20）。

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text=""符合性" 页中的策略符合性详细信息的屏幕截图。" border="false":::

> [!NOTE]
> Azure 策略中的规章遵从性是一项预览功能。 门户中 SDK 和页面的符合性属性对于已启用的计划有所不同。 有关详细信息，请参阅合规 [性](../concepts/regulatory-compliance.md)

## <a name="portal"></a>门户

Azure 门户展示了一个图形体验用于可视化和了解环境中的符合性状态。 在“策略”页上，“概述”选项提供了策略和计划符合性的可用范围的详细信息。  除了符合性状态和每个分配的计数以外，该页还包含一个图表，显示过去七天的符合性。 “符合性”页包含上述大量相同信息（图表除外），但提供附加的筛选和排序选项。

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="符合性页面、筛选选项和详细信息的屏幕截图。" border="false":::

由于策略或计划可分配到不同的范围，因此表中包含每个分配的范围，以及分配的定义类型。 还提供每个分配项中不合规资源和不合规策略的数量。 在表中选择策略或计划可以更深入地了解该特定分配的符合性。

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="符合性详细信息页的屏幕截图，包括计数和资源符合性详细信息。" border="false":::

“资源符合性”选项卡上的资源列表显示当前分配的现有资源的评估状态。 此选项卡默认为“不符合”，但是可以进行筛选。
创建资源的请求所触发的事件（追加、审核、拒绝、部署）显示在“事件”选项卡下。

> [!NOTE]
> 对于 AKS 引擎策略，显示的资源即是资源组。

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text=""符合性详细信息" 页上的 "事件" 选项卡屏幕截图。" border="false":::

对于[资源提供程序模式](../concepts/definition-structure.md#resource-provider-modes)资源，通过在“资源符合性”选项卡上选择资源或右键单击行并选择“查看符合性详细信息”可以打开组件符合性详细信息。 该页面还提供了选项卡，以查看分配给此资源、事件、组件事件和更改历史记录的策略。

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="资源提供程序模式分配的组件符合性选项卡和符合性详细信息的屏幕截图。" border="false":::

返回资源符合性页面，右键单击要收集其更多详细信息的事件所在的行，然后选择“显示活动日志”。 活动日志页将会打开，其中的搜索结果经过预先筛选，显示分配和事件的详细信息。 活动日志提供有关这些事件的其他上下文和信息。

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="Azure 策略活动和评估的活动日志屏幕截图。" border="false":::

### <a name="understand-non-compliance"></a>了解不符合性

如果资源不符合，可能有很多原因。 若要确定资源不符合的原因或查找更改负责人，请参阅[确定不符合性](./determine-non-compliance.md)。

## <a name="command-line"></a>命令行

可以使用 REST API（包括使用 [ARMClient](https://github.com/projectkudu/ARMClient)）、Azure PowerShell 和 Azure CLI 来检索门户中提供的相同信息。 有关 REST API 的完整详细信息，请参阅 [Azure Policy Insights](/rest/api/policy-insights/) 参考文章。 REST API 参考页上针对每个操作提供了一个绿色的“试用”按钮，使用该按钮可在浏览器中直接试用该操作。

对于 REST API 示例，使用 ARMClient 或类似工具来处理对 Azure 的身份验证。

### <a name="summarize-results"></a>汇总结果

使用 REST API 时，可以按容器、定义或分配进行汇总。 下面是使用 Azure Policy Insight 的[按订阅汇总](/rest/api/policy-insights/policystates/summarizeforsubscription)功能在订阅级别执行的汇总示例：

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
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
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant'",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>查询资源

在上面的示例中，**value.policyAssignments.policyDefinitions.results.queryResultsUri** 提供了一个示例 URI，用于特定策略定义的所有不符合资源。 查看 $filter 值，ComplianceState 等于 (eq)“NonCompliant”，PolicyAssignmentId 是针对策略定义，然后针对 PolicyDefinitionId 本身指定的。 在筛选器中包含 PolicyAssignmentId 的原因是，PolicyDefinitionId 可能在具有不同范围的多个策略或计划分配中存在。 通过指定 PolicyAssignmentId 和 PolicyDefinitionId，可以明确指定想要查找的结果。 以前，我们使用了 **latest** 作为 PolicyStates，因此将**起始**和**截止**时间范围自动设置成了过去 24 小时。

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
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
        "ComplianceState": "NonCompliant",
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
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2019-10-01
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

有关查询策略事件的详细信息，请参阅 [Azure Policy 事件](/rest/api/policy-insights/policyevents)参考文章。

### <a name="azure-cli"></a>Azure CLI

适用于 Azure Policy 的 [Azure CLI](/cli/azure/what-is-azure-cli) 命令组涵盖 REST 或 Azure PowerShell 中提供的大多数操作。 有关可用命令的完整列表，请参阅 [Azure CLI - Azure Policy 概述](/cli/azure/policy)。

示例：获取不符合资源数最多的、最前面的已分配策略的状态摘要。

```azurecli-interactive
az policy state summarize --top 1
```

响应的上半部分如以下示例所示：

```json
{
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
    "odataid": null,
    "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
            "policyDefinitions": [{
                "effect": "audit",
                "policyDefinitionGroupNames": [
                    ""
                ],
                "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
                "policyDefinitionReferenceId": "",
                "results": {
                    "nonCompliantPolicies": null,
                    "nonCompliantResources": 398,
                    "policyDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "policyGroupDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2020-07-14 14:01:22Z&$to=2020-07-15 14:01:22Z and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8' and PolicyDefinitionId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a'",
                    "resourceDetails": [{
                            "complianceState": "noncompliant",
                            "count": 398
                        },
                        {
                            "complianceState": "compliant",
                            "count": 4
                        }
                    ]
                }
            }],
    ...
```

示例：获取最近评估的资源的状态记录（默认按时间戳的降序排序）。

```azurecli-interactive
az policy state list --top 1
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "Compliant",
    "effectiveParameters": "",
    "isCompliant": true,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/securitycenterbuiltin",
    "policyAssignmentName": "SecurityCenterBuiltIn",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "auditifnotexists",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionName": "aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionReferenceId": "identityenablemfaforownerpermissionsmonitoring",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "security center",
    "policySetDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionName": "1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "",
    "resourceId": "/subscriptions/{subscriptionId}",
    "resourceLocation": "",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Resources/subscriptions",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.903433+00:00"
  }
]
```

示例：获取所有不符合虚拟网络资源的详细信息。

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'"
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

示例：获取在特定日期后发生的、与不符合虚拟网络资源相关的事件。

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'" --from '2020-07-14T00:00:00Z'
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

### <a name="azure-powershell"></a>Azure PowerShell

适用于 Azure Policy 的 Azure PowerShell 模块在 PowerShell 库中以 [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights) 的形式提供。 使用 PowerShellGet，可以使用 `Install-Module -Name Az.PolicyInsights` 安装模块（请确保已安装了最新版 [Azure PowerShell](/powershell/azure/install-az-ps)）：

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
ComplianceState            : NonCompliant
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
ComplianceState            : NonCompliant
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
ComplianceState            : NonCompliant
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

如果已将包含 [Activity Log Analytics 解决方案](../../../azure-monitor/platform/activity-log.md)中 `AzureActivity` 的 [Log Analytics 工作区](../../../azure-monitor/log-query/log-query-overview.md)绑定到订阅，则还可以使用简单的 Kusto 查询和 `AzureActivity` 表，查看评估周期中的不符合结果。 借助 Azure Monitor 日志中的详细信息，可对警报进行配置，以监视不符合情况。

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="显示 AzureActivity 表中的 Azure 策略操作 Azure Monitor 日志的屏幕截图。" border="false":::

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看 [Azure Policy 定义结构](../concepts/definition-structure.md)。
- 查看[了解策略效果](../concepts/effects.md)。
- 了解如何[以编程方式创建策略](programmatically-create.md)。
- 了解如何[修正不符合的资源](remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
