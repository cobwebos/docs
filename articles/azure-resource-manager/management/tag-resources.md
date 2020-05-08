---
title: 为逻辑组织标记资源、资源组和订阅
description: 演示如何应用标记来整理 Azure 资源以便进行计费和管理。
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 9ba7c58f6fa56b8ef2c233a5fe7f8f8e04fe29e1
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864481"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>使用标记来组织 Azure 资源和管理层次结构

将标记应用于 Azure 资源、资源组和订阅，以逻辑方式将它们组织到分类。 每个标记由名称和值对组成。 例如，可以对生产中的所有资源应用名称“Environment”和值“Production”。

有关如何实现标记策略的建议，请参阅[资源命名和标记决策指南](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)。

> [!IMPORTANT]
> 标记名称不区分大小写。 标记值区分大小写。

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>所需访问权限

若要将标记应用到资源，则必须对 " **Microsoft .resources/标记**" 资源类型具有写入访问权限。 [标记参与者](../../role-based-access-control/built-in-roles.md#tag-contributor)角色使你可以将标记应用到实体，而无需访问实体本身。 目前，标记参与者角色无法通过门户将标记应用到资源或资源组。 它可以通过门户将标记应用到订阅。 它通过 PowerShell 和 REST API 支持所有标记操作。  

"[参与者](../../role-based-access-control/built-in-roles.md#contributor)" 角色还授予对任何实体应用标记所需的访问权限。 要将标记仅应用于一种资源类型，请使用该资源的参与者角色。 例如，要将标记应用到虚拟机，请使用[虚拟机参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)。

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>应用标记

Azure PowerShell 提供了两个命令用于应用标记- [AzTag](/powershell/module/az.resources/new-aztag)和[AzTag](/powershell/module/az.resources/update-aztag)。 必须具有 Az module module 1.12.0 或更高版本。 你可以通过检查你的`Get-Module Az.Resources`版本。 可以安装该模块，也可以[安装 Azure PowerShell](/powershell/azure/install-az-ps) 3.6.1 或更高版本。

**AzTag**将替换资源、资源组或订阅上的所有标记。 调用命令时，传递要标记的实体的资源 ID。

下面的示例将一组标记应用到存储帐户：

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

命令完成后，请注意该资源有两个标记。

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

如果再次运行该命令，但此时间具有不同的标记，请注意，前面的标记将被删除。

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

若要将标记添加到已包含标记的资源，请使用**AzTag**。 将 **-Operation**参数设置为**Merge**。

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

请注意，这两个新标记添加到了两个现有标记中。

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

每个标记名称只能有一个值。 如果为标记提供新值，则即使使用合并操作，也会替换旧值。 下面的示例将状态标记从正常更改为绿色。

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

如果将 **-Operation**参数设置为**Replace**，则现有标记将替换为新的标记集。

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

只有新的标记保留在资源上。

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

相同的命令也可用于资源组或订阅。 传入要标记的资源组或订阅的标识符。

若要将一组新的标记添加到资源组，请使用：

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

若要更新资源组的标记，请使用：

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

若要向订阅添加一组新的标记，请使用：

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

若要更新订阅的标记，请使用：

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

一个资源组中可能有多个同名资源。 在这种情况下，可以通过以下命令设置每个资源：

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>列出标记

若要获取资源、资源组或订阅的标记，请使用[AzTag](/powershell/module/az.resources/get-aztag)命令并传入实体的资源 ID。

若要查看资源的标记，请使用：

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

若要查看资源组的标记，请使用：

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

若要查看订阅的标记，请使用：

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>按标记列出

若要获取具有特定标记名称和值的资源，请使用：

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

若要获取具有特定标记名称和任何标记值的资源，请使用：

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

若要获取具有特定标记名称和值的资源组，请使用：

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>删除标记

若要删除特定标记，请使用**AzTag**并将**操作**设置为 "**删除**"。 传入要删除的标记。

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

指定的标记将被删除。

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

若要删除所有标记，请使用[AzTag](/powershell/module/az.resources/remove-aztag)命令。

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure CLI

### <a name="apply-tags"></a>应用标记

将标记添加到资源组或资源时，可以覆盖现有的标记，或将新标记追加到现有标记之后。

若要覆盖资源的标记，请使用：

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

若将标记追加到资源的现有标记之后，请使用：

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

若要覆盖资源组的现有标记，请使用：

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

若要将标记追加到资源组的现有标记之后，请使用：

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

目前 Azure CLI 不支持将标记应用于订阅。

### <a name="list-tags"></a>列出标记

若要查看资源的现有标记，请使用：

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

若要查看资源组的现有标记，请使用：

```azurecli-interactive
az group show -n examplegroup --query tags
```

该脚本返回以下格式：

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

### <a name="list-by-tag"></a>按标记列出

若要获取具有特定标记和值的所有资源，请使用 `az resource list`：

```azurecli-interactive
az resource list --tag Dept=Finance
```

若要获取具有特定标记的资源组，请使用 `az group list`：

```azurecli-interactive
az group list --tag Dept=IT
```

### <a name="handling-spaces"></a>处理空间

如果标记名称或值包含空格，则必须执行几个额外的步骤。 下面的示例在标记可能包含空格时将资源组中的所有标记应用于其资源。

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '{}"' | sed 's/: /=/g' | sed "s/\"/'/g" | sed 's/, /,/g' | sed 's/ *$//g' | sed 's/^ *//g')
origIFS=$IFS
IFS=','
read -a tagarr <<< "$tags"
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags "${tagarr[@]}" --id $id
done
IFS=$origIFS
```

## <a name="templates"></a>模板

你可以使用资源管理器模板在部署过程中标记资源、资源组和订阅。

### <a name="apply-values"></a>应用值

以下示例部署了包含三个标记的存储帐户。 两个标记（`Dept`和`Environment`）设置为文本值。 一个标记（`LastDeployed`）设置为默认为当前日期的参数。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
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
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
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

### <a name="apply-an-object"></a>应用对象

可以定义一个对象参数，用于存储多个标记，并将该对象应用于标记元素。 此方法比上一个示例具有更大的灵活性，因为对象可以具有不同的属性。 对象中的每个属性将成为该资源的单独标记。 以下示例有一个名为 `tagValues` 的参数，应用于标记元素。

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

### <a name="apply-a-json-string"></a>应用 JSON 字符串

要将多个值存储在单个标记中，请应用表示值的 JSON 字符串。 整个 JSON 字符串存储为一个标记，该标记不能超过 256 个字符。 以下示例有一个名为 `CostCenter` 的标记，其中包含 JSON 字符串中的几个值：  

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

### <a name="apply-tags-from-resource-group"></a>应用资源组中的标记

若要将资源组中的标记应用于资源，请使用 [resourceGroup](../templates/template-functions-resource.md#resourcegroup) 函数。 获取标记值时，请使用 `tags[tag-name]` 语法而不是 `tags.tag-name` 语法，因为有些字符在点表示法中无法正确解析。

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

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>将标记应用到资源组或订阅

可以通过**部署 ""，将**标记添加到资源组或订阅。 标记将应用于部署的目标资源组或订阅。 每次部署模板时，都将替换以前应用的任何标记。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

若要将标记应用到资源组，请使用 PowerShell 或 Azure CLI。 部署到要标记的资源组。

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

若要将标记应用到订阅，请使用 PowerShell 或 Azure CLI。 部署到要标记的订阅。

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

以下模板将对象中的标记添加到资源组或订阅中。

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>门户

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

若要通过 Azure REST API 处理标记，请使用：

* [标记-在范围内创建或更新](/rest/api/resources/tags/createorupdateatscope)（PUT 操作）
* [标记-在范围内更新](/rest/api/resources/tags/updateatscope)（修补操作）
* [标记-获取范围](/rest/api/resources/tags/getatscope)（获取操作）
* [标记-在范围内删除](/rest/api/resources/tags/deleteatscope)（删除操作）

## <a name="inherit-tags"></a>继承标记

应用于资源组或订阅的标记不会由资源继承。 若要将订阅或资源组中的标记应用于资源，请参阅[Azure 策略-标记](tag-policies.md)。

## <a name="tags-and-billing"></a>标记和计费

可使用标记对计费数据进行分组。 例如，如果针对不同组织运行多个 VM，可以使用标记根据成本中心对使用情况进行分组。 还可使用标记根据运行时环境对成本进行分类；例如，在生产环境中运行的虚拟机的计费使用情况。

可以通过 [Azure 资源使用情况与费率卡 API](../../billing/billing-usage-rate-card-overview.md) 或者使用情况逗号分隔值 (CSV) 文件检索有关标记的信息。 可从 [Azure 帐户中心](https://account.azure.com/Subscriptions)或 Azure 门户下载使用情况文件。 有关详细信息，请参阅[下载或查看 Azure 帐单发票和每日使用数据](../../billing/billing-download-azure-invoice-daily-usage-date.md)。 从 Azure 帐户中心下载使用情况文件时，选择**版本 2**。 对于支持为账单提供标记的服务，标记会显示在“标记”列中。****

有关 REST API 操作，请参阅 [Azure 计费 REST API 参考](/rest/api/billing/)。

## <a name="limitations"></a>限制

以下限制适用于标记：

* 并非所有资源类型都支持标记。 若要确定是否可以将标记应用到资源类型，请参阅 [Azure 资源的标记支持](tag-support.md)。
* 管理组当前不支持标记。
* 每个资源、资源组和订阅最多可以有50个标记名称/值对。 如果需要应用的标记超过最大允许数量，请使用 JSON 字符串作为标记值。 JSON 字符串可以包含多个应用于单个标记名称的值。 资源组或订阅可以包含多个资源，每个资源具有50标记名称/值对。
* 标记名称不能超过 512 个字符，标记值不能超过 256 个字符。 对于存储帐户，标记名称不能超过 128 个字符，标记值不能超过 256 个字符。
* 通用化 VM 不支持标记。
* 不能将标记应用到云服务等经典资源。
* 标记名称不能包含以下字符：`<`、`>`、`%`、`&`、`\`、`?`、`/`

   > [!NOTE]
   > 目前，Azure DNS 区域和流量管理器服务也不允许在标记中使用空格。
   >
   > Azure 前门不支持`#`在标记名称中使用。

## <a name="next-steps"></a>后续步骤

* 并非所有资源类型都支持标记。 若要确定是否可以将标记应用到资源类型，请参阅 [Azure 资源的标记支持](tag-support.md)。
* 有关如何实现标记策略的建议，请参阅[资源命名和标记决策指南](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)。
