---
title: 逻辑组织的标记资源
description: 演示如何应用标记来整理 Azure 资源以便进行计费和管理。
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: a0ba5ba89b966de4aa1d1394f7d90c99f8352115
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893019"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>使用标记整理 Azure 资源

将标记应用于 Azure 资源，以逻辑方式将它们组织到分类。 每个标记由名称和值对组成。 例如，可以对生产中的所有资源应用名称“Environment”和值“Production”。

应用标记以后，即可使用该标记名称和值检索订阅中的所有资源。 使用标记可以从不同资源组中检索相关资源。 需要为计费或管理目的组织资源时，此方法十分有用。

除了 autotagging 策略外，分类还应考虑使用自助服务元数据标记策略来降低用户的负担并提高准确性。

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="limitations"></a>限制

以下限制适用于标记：

* 并非所有资源类型都支持标记。 若要确定是否可以将标记应用到资源类型，请参阅 [Azure 资源的标记支持](tag-support.md)。
* 每个资源或资源组最多可以有50个标记名称/值对。 如果需要应用超过允许的最大数目的标记，请将 JSON 字符串用于标记值。 JSON 字符串可以包含多个应用于单个标记名称的值。 资源组可以包含多个资源，每个资源具有50标记名称/值对。
* 标记名称不能超过 512 个字符，标记值不能超过 256 个字符。 对于存储帐户，标记名称不能超过 128 个字符，标记值不能超过 256 个字符。
* 通用化 Vm 不支持标记。
* 应用于资源组的标记不会被该资源组中的资源继承。
* 不能将标记应用到云服务等经典资源。
* 标记名称不能包含以下字符：`<`、`>`、`%`、`&`、`\`、`?`、`/`

   > [!NOTE]
   > 目前 Azure DNS 区域和流量管理器服务也不允许在标记中使用空格。 

## <a name="required-access"></a>必需的访问权限

要向资源应用标记，用户必须具有对该资源类型的写权限。 要将标记应用于所有资源类型，请使用[参与者](../role-based-access-control/built-in-roles.md#contributor)角色。 要将标记仅应用于一种资源类型，请使用该资源的参与者角色。 例如，要将标记应用到虚拟机，请使用[虚拟机参与者](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)。

## <a name="policies"></a>策略

可以使用 [Azure Policy ](../governance/policy/overview.md)来强制实施标记规则和约定。 通过创建策略，可以避免将与预期的组织标记不相符的资源部署到订阅。 无需手动应用标记或搜索不符合的资源，可以创建一个策略，在部署期间自动应用所需标记。 现在，还可以使用新的[修改](../governance/policy/concepts/effects.md#modify)效果和[修正任务](../governance/policy/how-to/remediate-resources.md)将标记应用到现有资源。 以下部分展示标记策略示例。

[!INCLUDE [Tag policies](../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

若要查看*资源组*的现有标记，请使用：

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

该脚本返回以下格式：

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

若要查看*具有指定名称和资源组的资源*的现有标记，请使用：

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

或者，如果有资源的资源 ID，可以传递该资源 ID 以获取标记。

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

若要获取*具有特定标记名称和值的资源组*，请使用：

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "Dept"="Finance" }).ResourceGroupName
```

若要获取*具有特定标记名称和值的资源*，请使用：

```azurepowershell-interactive
(Get-AzResource -Tag @{ "Dept"="Finance"}).Name
```

若要获取具有特定标记名称的资源，请使用：

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

每次将标记应用到某个资源或资源组时，都会覆盖该资源或资源组中的现有标记。 因此，必须根据该资源或资源组是否包含现有标记来使用不同的方法。

若要将标记添加到*不包含现有标记的资源组*，请使用：

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ "Dept"="IT"; "Environment"="Test" }
```

若要将标记添加到包含现有标记的资源组，请检索现有标记，添加新标记，然后重新应用标记：

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

若要将标记添加到*不包含现有标记的资源*，请使用：

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $resource.ResourceId -Force
```

资源组中可能存在多个具有相同名称的资源。 在这种情况下，可以通过以下命令设置每个资源：

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Force }
```

若要将标记添加到包含现有标记的资源，请使用：

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$resource.Tags.Add("Status", "Approved")
Set-AzResource -Tag $resource.Tags -ResourceId $resource.ResourceId -Force
```

若要将资源组中的所有标记应用于其资源，并且*不保留资源上的现有标记*，请使用以下脚本：

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
Get-AzResource -ResourceGroupName $group.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $group.Tags -Force }
```

若要将资源组中的所有标记应用于其资源，并*将现有标记保留在不重复的资源上*，请使用以下脚本：

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

若要删除所有标记，请传递一个空哈希表：

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Azure CLI

若要查看*资源组*的现有标记，请使用：

```azurecli
az group show -n examplegroup --query tags
```

该脚本返回以下格式：

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

若要查看具有指定名称、类型和资源组的资源的现有标记，请使用：

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

循环访问资源集合时，可能想要按资源 ID 显示资源。 本文稍后介绍一个完整的示例。 若要查看具有指定资源 ID 的资源的现有标记，请使用：

```azurecli
az resource show --id <resource-id> --query tags
```

若要获取具有特定标记的资源组，请使用 `az group list`：

```azurecli
az group list --tag Dept=IT
```

若要获取具有特定标记和值的所有资源，请使用 `az resource list`：

```azurecli
az resource list --tag Dept=Finance
```

每次将标记应用到某个资源或资源组时，都会覆盖该资源或资源组中的现有标记。 因此，必须根据该资源或资源组是否包含现有标记来使用不同的方法。

若要将标记添加到*不包含现有标记的资源组*，请使用：

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

若要将标记添加到*不包含现有标记的资源*，请使用：

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

若要将标记添加到已带标记的资源，请检索现有标记，重新格式化该值，然后重新应用现有标记和新标记：

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags -o json)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

若要将资源组中的所有标记应用于其资源，并且*不保留资源上的现有标记*，请使用以下脚本：

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags -o json)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

若要将资源组中的所有标记应用于其资源，并且*保留资源上的现有标记*，请使用以下脚本：

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags -o json)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags -o json)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>模板

若要在部署过程中标记资源，请将 `tags` 元素添加到正在部署的资源。 提供标记名称和值。

### <a name="apply-a-literal-value-to-the-tag-name"></a>将文本值应用到标记名称

以下示例显示了一个带两个标记（`Dept` 和 `Environment`）的存储帐户，这两个标记设置为文本值：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

若要将标记设置为 datetime 值，请使用[utcNow 函数](resource-group-template-functions-string.md#utcnow)。

### <a name="apply-an-object-to-the-tag-element"></a>将对象应用到标记元素

可以定义一个对象参数，用于存储多个标记，并将该对象应用于标记元素。 对象中的每个属性将成为该资源的单独标记。 以下示例有一个名为 `tagValues` 的参数，应用于标记元素。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string-to-the-tag-name"></a>将 JSON 字符串应用到标记名称

要将多个值存储在单个标记中，请应用表示值的 JSON 字符串。 整个 JSON 字符串存储为一个不能超过256个字符的标记。 以下示例有一个名为 `CostCenter` 的标记，其中包含 JSON 字符串中的几个值：  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>从资源组应用标记

若要将资源组中的标记应用于资源，请使用[resourceGroup](resource-group-template-functions-resource.md#resourcegroup)函数。 获取标记值时，请使用 `tags.[tag-name]` 语法而不是 `tags.tag-name` 语法，因为在点表示法中某些字符不能正确解析。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

## <a name="portal"></a>门户

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Azure 门户和 PowerShell 均在后台使用[资源管理器 REST API](https://docs.microsoft.com/rest/api/resources/)。 如果需要将标记集成到其他环境中，可对资源 ID 使用 GET 以获取标记，并使用 PATCH 调用更新标记集。

## <a name="tags-and-billing"></a>标记和计费

可使用标记对计费数据进行分组。 例如，如果针对不同组织运行多个 VM，可以使用标记根据成本中心对使用情况进行分组。 还可使用标记根据运行时环境对成本进行分类；例如，在生产环境中运行的虚拟机的计费使用情况。

可以通过 [Azure 资源使用情况与费率卡 API](../billing/billing-usage-rate-card-overview.md) 或者使用情况逗号分隔值 (CSV) 文件检索有关标记的信息。 可从 [Azure 帐户中心](https://account.azure.com/Subscriptions)或 Azure 门户下载使用情况文件。 有关详细信息，请参阅[下载或查看 Azure 帐单发票和每日使用数据](../billing/billing-download-azure-invoice-daily-usage-date.md)。 从 Azure 帐户中心下载使用情况文件时，选择**版本 2**。 对于支持为账单提供标记的服务，标记会显示在“标记”列中。

有关 REST API 操作，请参阅 [Azure 计费 REST API 参考](/rest/api/billing/)。

## <a name="next-steps"></a>后续步骤

* 并非所有资源类型都支持标记。 若要确定是否可以将标记应用到资源类型，请参阅 [Azure 资源的标记支持](tag-support.md)。
* 有关使用门户的说明，请参阅[使用 Azure 门户管理 Azure 资源](manage-resource-groups-portal.md)。  
