---
title: 跨订阅和资源组部署资源
description: 介绍如何在部署期间将多个 Azure 订阅和资源组作为目标。
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 2ef68dcb933075833c323d973b023cdaee61bd2f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650635"
---
# <a name="deploy-azure-resources-across-subscriptions-or-resource-groups"></a>跨订阅或资源组部署 Azure 资源

资源管理器使你可以在单个部署中部署到多个资源组。 使用嵌套模板可指定与部署操作中的资源组不同的资源组。 资源组可以存在于不同的订阅中。

> [!NOTE]
> 在单个部署中可以部署到 800 个资源组。 通常情况下，此限制意味着，在嵌套或链接的部署中可以部署到为父模板指定的一个资源组和最多 799 个资源组。 但是，如果父模板仅包含嵌套或链接的模板，并且本身不部署任何资源，则在嵌套或链接的部署中最多可包含 800 个资源组。

## <a name="specify-subscription-and-resource-group"></a>指定订阅和资源组

若要将与父模板不同的资源组作为目标，请使用[嵌套或链接模板](linked-templates.md)。 在部署资源类型中，为要将嵌套模板部署到的订阅 ID 和资源组指定值。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

如果未指定订阅 ID 或资源组，将使用父模板中的订阅和资源组。 在运行部署之前，所有资源组都必须存在。

部署模板的帐户必须有权部署到指定的订阅 ID。 如果指定的订阅存在于不同的 Azure Active Directory 租户中，则必须[从另一个目录添加来宾用户](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)。

以下示例部署两个存储帐户。 第一个存储帐户部署到在部署操作中指定的资源组。 第二个存储帐户部署到在 `secondResourceGroup` 和 `secondSubscriptionID` 参数中指定的资源组：

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

如果将 `resourceGroup` 设置为不存在的资源组的名称，则部署会失败。

若要测试以上模板并查看结果，请使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要将两个存储帐户部署到同一订阅中的两个资源组，请使用：

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

若要将两个存储帐户部署到两个订阅，请使用：

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

若要将两个存储帐户部署到同一订阅中的两个资源组，请使用：

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

若要将两个存储帐户部署到两个订阅，请使用：

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

## <a name="use-functions"></a>使用函数

[resourceGroup()](template-functions-resource.md#resourcegroup) 和 [subscription()](template-functions-resource.md#subscription) 函数根据指定模板的方式以不同的方式解析。 链接到外部模板时，函数始终解析为该模板的范围。 在父模板中嵌套模板时，请使用 `expressionEvaluationOptions` 属性指定函数是解析为父模板还是嵌套模板的资源组和订阅。 将该属性设置为 `inner` 可解析为嵌套模板的范围。 将该属性设置为 `outer` 可解析为父模板的范围。

下表显示函数是解析为父级还是嵌入资源组和订阅。

| 模板类型 | 范围 | 解决方法 |
| ------------- | ----- | ---------- |
| 嵌套        | 外部（默认值） | 父资源组 |
| 嵌套        | 内部 | 子资源组 |
| 链接        | 空值   | 子资源组 |

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json)演示：

* 具有默认（外部）范围的嵌套模板
* 具有内部范围的嵌套模板
* 链接模板

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

若要测试以上模板并查看结果，请使用 PowerShell 或 Azure CLI。

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

* 若要了解如何在模板中定义参数，请参阅[了解 Azure 资源管理器模板的结构和语法](template-syntax.md)。
* 有关解决常见部署错误的提示，请参阅[排查使用 Azure 资源管理器时的常见 Azure 部署错误](common-deployment-errors.md)。
* 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](secure-template-with-sas-token.md)。
