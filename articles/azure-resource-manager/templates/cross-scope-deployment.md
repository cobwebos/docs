---
title: 跨范围部署资源
description: 介绍如何在部署过程中以多个范围为目标。 范围可以是租户、管理组、订阅和资源组。
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 6161401ac039551a814b595715f56df1ac62dd6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87374502"
---
# <a name="deploy-azure-resources-across-scopes"></a>跨范围部署 Azure 资源

使用 Azure 资源管理器模板（ARM 模板），可以在单次部署中部署到多个范围。 可用的范围是租户、管理组、订阅和资源组。 例如，可以将资源部署到一个资源组，并在同一模板中将资源部署到另一个资源组。 或者，可以将资源部署到管理组，并且也将资源部署到该管理组中的资源组。

请使用[嵌套或链接模板](linked-templates.md)来指定与部署操作的主范围不同的范围。

## <a name="available-scopes"></a>可用的范围

用于部署操作的范围决定其他哪些范围可用。 你可以部署到[租户](deploy-to-tenant.md)、[管理组](deploy-to-management-group.md)、[订阅](deploy-to-subscription.md)或[资源组](deploy-powershell.md)。 从主部署级别中，不能在层次结构中向上提升级别。 例如，如果部署到订阅，则不能向上提升级别以将资源部署到管理组。 但是，可以部署到管理组并向下降低级别以部署到订阅或资源组。

对于每一个范围，部署模板的用户必须具有创建资源所必需的权限。

## <a name="cross-resource-groups"></a>跨资源组

若要将与父模板不同的资源组作为目标，请使用[嵌套或链接模板](linked-templates.md)。 在部署资源类型中，为要将嵌套模板部署到的订阅 ID 和资源组指定值。 资源组可以存在于不同的订阅中。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

如果未指定订阅 ID 或资源组，将使用父模板中的订阅和资源组。 在运行部署之前，所有资源组都必须存在。

> [!NOTE]
> 在单个部署中可以部署到 800 个资源组。 通常情况下，此限制意味着，在嵌套或链接的部署中可以部署到为父模板指定的一个资源组和最多 799 个资源组。 但是，如果父模板仅包含嵌套或链接的模板，并且本身不部署任何资源，则在嵌套或链接的部署中最多可包含 800 个资源组。

以下示例部署两个存储帐户。 第一个存储帐户部署到在部署操作中指定的资源组。 第二个存储帐户部署到在 `secondResourceGroup` 和 `secondSubscriptionID` 参数中指定的资源组：

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

如果将 `resourceGroup` 设置为不存在的资源组的名称，则部署会失败。

若要测试上述模板并查看结果，请使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要将两个存储帐户部署到**同一订阅**中的两个资源组，请使用：

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

若要将两个存储帐户部署到**两个订阅**，请使用：

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要将两个存储帐户部署到**同一订阅**中的两个资源组，请使用：

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

若要将两个存储帐户部署到**两个订阅**，请使用：

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="cross-subscription-management-group-and-tenant"></a>跨订阅、管理组和租户

为订阅、管理组和租户级别的部署指定不同范围时，可像资源组的示例那样使用嵌套部署。 用来指定范围的属性可能会有所不同。 有关部署级别的文章中介绍了这些方案。 有关详情，请参阅：

* [在订阅级别创建资源组和资源](deploy-to-subscription.md)
* [在管理组级别创建资源](deploy-to-management-group.md)
* [在租户级别创建资源](deploy-to-tenant.md)

## <a name="how-functions-resolve-in-scopes"></a>函数在范围中解析的方式

当部署到多个范围时，根据指定模板的方式不同，[resourceGroup()](template-functions-resource.md#resourcegroup) 和 [subscription()](template-functions-resource.md#subscription) 函数解析的方式也有所不同。 链接到外部模板时，函数始终解析为该模板的作用域。 在父模板中嵌套模板时，请使用 `expressionEvaluationOptions` 属性指定函数是否解析为父模板或嵌套模板的资源组和订阅。 将属性设置为 `inner`，以便解析为嵌套模板的范围。 将属性设置为 `outer`，以便解析为父模板的范围。

下表显示了函数是解析为父资源组，还是解析为嵌入资源组和订阅。

| 模板类型 | 范围 | 解决方法 |
| ------------- | ----- | ---------- |
| 嵌套        | 外部（默认值） | 父资源组 |
| 嵌套        | 内部 | 子资源组 |
| 链接        | 空值   | 子资源组 |

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json)演示：

* 具有默认（外部）范围的嵌套模板
* 具有内部范围的嵌套模板
* 链接的模板

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

若要测试上述模板并查看结果，请使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

前述示例的输出为：

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

前述示例的输出为：

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---



## <a name="next-steps"></a>后续步骤

* 若要了解如何在模板中定义参数，请参阅[了解 Azure Resource Manager 模板的结构和语法](template-syntax.md)。
* 有关解决常见部署错误的提示，请参阅[排查使用 Azure Resource Manager 时的常见 Azure 部署错误](common-deployment-errors.md)。
* 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](secure-template-with-sas-token.md)。
