---
title: 使用 Azure Policy 实现 Azure Cosmos DB 资源的治理和控制
description: 了解如何使用 Azure Policy 来实现 Azure Cosmos DB 资源的治理和控制。
author: plzm
ms.author: paelaz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: a1b1c01f7cf720690decd9c7aac5fb14b92121ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84431981"
---
# <a name="use-azure-policy-to-implement-governance-and-controls-for-azure-cosmos-db-resources"></a>使用 Azure Policy 实现 Azure Cosmos DB 资源的治理和控制

[Azure Policy](../governance/policy/overview.md) 有助于强制实施组织治理标准、评估资源符合性并实现自动修复。 常见的用例包括安全性、成本管理和配置一致性。

Azure Policy 提供内置策略定义。 可以为内置策略定义未解决的方案创建自定义策略定义。 有关更多详细信息，请参阅 [Azure Policy 文档](../governance/policy/overview.md)。

## <a name="assign-a-built-in-policy-definition"></a>分配内置策略定义

策略定义描述了资源符合性条件以及在满足某个条件时所产生的效果。 策略分配是根据策略定义创建的__ __。 可以对 Azure Cosmos DB 资源使用内置或自定义策略定义。 策略分配的范围为 Azure 管理组、Azure 订阅或资源组，策略分配将应用于所选范围内的资源。 或者，可以从所选范围中排除特定资源。

可以使用 [Azure 门户](../governance/policy/assign-policy-portal.md)、[Azure PowerShell](../governance/policy/assign-policy-powershell.md)、[Azure CLI](../governance/policy/assign-policy-azurecli.md) 或 [ARM 模板](../governance/policy/assign-policy-template.md)创建策略分配。

若要根据 Azure Cosmos DB 的内置策略定义创建策略分配，请使用[使用 Azure 门户创建策略分配](../governance/policy/assign-policy-portal.md)一文中的步骤。

在选择策略定义的步骤中，在“搜索”字段中输入 `Cosmos DB` 来筛选可用的内置策略定义。 选择一个可用的内置策略定义，然后选择“选择”继续创建策略分配****。

> [!TIP]
> 你还可以将“可用定义”格窗中显示的内置策略定义名称与 Azure PowerShell、Azure CLI 或 ARM 模板结合使用来创建策略分配****。

:::image type="content" source="./media/policy/available-definitions.png" alt-text="搜索 Azure Cosmos DB 内置策略定义":::

## <a name="create-a-custom-policy-definition"></a>创建自定义策略定义

对于内置策略未解决的特定方案，可以创建[自定义策略定义](../governance/policy/tutorials/create-custom-policy-definition.md)。 之后，根据自定义策略定义创建策略分配__ __。

### <a name="property-types-and-property-aliases-in-policy-rules"></a>策略规则中的属性类型和属性别名

使用[自定义策略定义步骤](../governance/policy/tutorials/create-custom-policy-definition.md)来标识创建策略规则所需的资源属性和属性别名。

若要确定特定于 Azure Cosmos DB 的属性别名，请将命名空间 `Microsoft.DocumentDB` 与“自定义策略定义步骤”一文中所述的其中一种方法结合使用。

#### <a name="use-the-azure-cli"></a>使用 Azure CLI：
```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for namespace Microsoft.DocumentDB
az provider show --namespace Microsoft.DocumentDB --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

#### <a name="use-azure-powershell"></a>使用 Azure PowerShell：
```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.DocumentDB namespace
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.DocumentDB').Aliases
```

这些命令输出 Azure Cosmos DB 属性的属性别名列表。 下面是输出的摘录：

```json
[
  "Microsoft.DocumentDB/databaseAccounts/sku.name",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*]",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*].id",
  "Microsoft.DocumentDB/databaseAccounts/isVirtualNetworkFilterEnabled",
  "Microsoft.DocumentDB/databaseAccounts/consistencyPolicy.defaultConsistencyLevel",
  "Microsoft.DocumentDB/databaseAccounts/enableAutomaticFailover",
  "Microsoft.DocumentDB/databaseAccounts/Locations",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*]",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*].locationName",
  "..."
]
```

可以在[自定义策略定义规则](../governance/policy/tutorials/create-custom-policy-definition.md#policy-rule)中使用这些属性别名中的任何一个。

下面是有关用于检查 Cosmos DB 帐户是否配置了多个写入位置的策略定义示例。 自定义策略定义包括两个规则：一个用于检查属性别名的具体类型，另一个用于检查类型的具体属性，在本例中即存储多个写入位置设置的字段。 这两个规则都使用别名。

```json
"policyRule": {
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DocumentDB/databaseAccounts"
      },
      {
        "field": "Microsoft.DocumentDB/databaseAccounts/enableMultipleWriteLocations",
        "notEquals": true
      }
    ]
  },
  "then": {
    "effect": "Audit"
  }
}
```

同内置策略定义一样，自定义策略定义可用于创建策略分配。

## <a name="policy-compliance"></a>策略符合性

创建策略分配后，Azure Policy 将评估分配范围内的资源。 每个资源会接受策略符合性评估__。 策略中指定的效果会应用到不符合的资源__。

你可以在 [Azure 门户](../governance/policy/how-to/get-compliance-data.md#portal)或通过 [Azure CLI](../governance/policy/how-to/get-compliance-data.md#command-line) 或 [Azure Monitor 日志](../governance/policy/how-to/get-compliance-data.md#azure-monitor-logs)查看符合性结果和修正详细信息。

以下屏幕截图显示了两个策略分配示例。

其中一个分配基于内置的策略定义，该定义检查 Azure Cosmos DB 资源是否仅部署到允许的 Azure 区域。 资源合规性显示范围内资源的策略评估结果（合规或不合规）。

另一个分配基于自定义策略定义。 此分配检查 Cosmos DB 帐户是否配置了多个写入位置。

部署策略分配后，符合性仪表板将显示评估结果。 请注意，部署策略分配后，可能需要 30 分钟才会显示评估结果。 此外，创建策略分配后即可[按需启动策略评估扫描](../governance/policy/how-to/get-compliance-data.md#on-demand-evaluation-scan)。

此屏幕截图显示了范围内 Azure Cosmos DB 帐户的以下合规性评估结果：

- 两个帐户均不遵从必须配置虚拟网络 (VNet) 筛选的策略。
- 两个帐户均不遵从帐户需配置多个写入位置的策略
- 两个帐户均不遵从将资源部署到允许的 Azure 区域的策略。

:::image type="content" source="./media/policy/compliance.png" alt-text="列出的 Azure 策略分配的合规性结果":::

若要修正不合规的资源，请参阅[如何使用 Azure Policy 修正资源](../governance/policy/how-to/remediate-resources.md)。

## <a name="next-steps"></a>后续步骤

- [查看 Azure Cosmos DB 的示例自定义策略定义](https://github.com/Azure/azure-policy/tree/master/samples/CosmosDB)，包括针对上述的多个写入位置和 VNet 筛选策略的内容。
- [在 Azure 门户中创建策略分配](../governance/policy/assign-policy-portal.md)
- [查看 Azure Cosmos DB 的 Azure Policy 内置策略定义](./policy-samples.md)
