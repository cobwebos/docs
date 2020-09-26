---
title: 模板规格概述
description: 介绍如何创建模板规格并与组织中的其他用户共享。
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 0516947ff134992d684aa6826999c4d65bba1457
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369070"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Azure 资源管理器模板规格（预览版）

模板规格是一种新资源类型，用于在 Azure 中存储 Azure 资源管理器模板（ARM 模板），以便之后进行部署。 通过该资源类型，你可以与组织中的其他用户共享 ARM 模板。 就像任何其他 Azure 资源一样，你可以使用 Azure 基于角色的访问控制 (Azure RBAC) 来共享模板规范。

Microsoft.Resources/templateSpecs 是模板规格的新资源类型。 它包含一个主模板和任意数量的链接模板。 Azure 将模板规格安全存储在资源组中。 模板规格支持[版本控制](#versioning)。

若要部署模板规格，请使用标准 Azure 工具（如 PowerShell）、Azure CLI、Azure 门户、REST 和其他受支持的 SDK 和客户端。 请使用针对模板使用的相同命令。

> [!NOTE]
> 模板规格当前提供预览版。 若要使用它，必须[注册等待列表](https://aka.ms/templateSpecOnboarding)。

## <a name="why-use-template-specs"></a>为什么使用模板规格？

如果 GitHub 存储库或存储帐户中当前有模板，在尝试共享和使用这些模板时会遇到一些困难。 如果用户要部署这些模板，要么模板必须是本地模板，要么它的 URL 必须是可公开访问的。 为应对此限制，你可能要与需要部署模板的用户共享该模板的副本，或者开放对存储库或存储帐户的访问权限。 如果用户拥有模板的本地副本，这些副本最终可能会与原始模板有所不同。 当将存储库或存储帐户设置为可公开访问时，可能会导致非预期用户能够访问模板。

使用模板规格的好处是，可以创建规范化的模板并与组织中的团队共享。 模板规范是安全的，因为它们适用于 Azure 资源管理器进行部署，但没有 Azure RBAC 权限的用户无法访问。 用户只需具有模板规格的读取访问权限即可部署模板，因此可以在不允许其他人进行修改的情况下共享该模板。

模板规格中包含的模板应由组织中的管理员按照组织的要求和指南进行验证。

## <a name="create-template-spec"></a>创建模板规格

以下示例显示了一个用于在 Azure 中创建存储帐户的简单模板。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[concat('store', uniquestring(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      }
    }
  ]
}
```

创建模板规格时，会将 PowerShell 或 CLI 命令传递给主模板文件。 如果主模板引用链接模板，则命令将查找并打包它们以创建模板规格。若要了解详细信息，请参阅[创建具有链接模板的模板规格](#create-a-template-spec-with-linked-templates)。

使用以下命令创建模板规格：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -Location westus2 -TemplateJsonFile ./mainTemplate.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

---

可以使用以下命令查看订阅中的所有模板规格：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts list
```

---

可以使用以下命令查看模板规格的详细信息（包括其版本）：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show \
    --name storageSpec \
    --resource-group templateSpecRG \
    --version "1.0"
```

---

## <a name="deploy-template-spec"></a>部署模板规格

创建模板规格后，具有对模板规格的读取访问权限的用户可以部署它。 有关授予访问权限的信息，请参阅[教程：使用 Azure PowerShell 授予组对 Azure 资源的访问权限](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)。

可通过门户、PowerShell、Azure CLI 部署模板规格，或者在更大型的模板部署中将其作为链接模板进行部署。 组织中的用户可以将模板规格部署到 Azure 中的任何范围（资源组、订阅、管理组或租户）。

可通过提供模板规格的资源 ID 来部署该模板规格，而无需传递模板的路径或 URI。 资源 ID 采用以下格式：

/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}

请注意，资源 ID 包括模板规格的版本号。

例如，使用以下命令部署模板规范。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

实际上，通常需要运行 `Get-AzTemplateSpec` 来获取要部署的模板规格的 ID。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

## <a name="parameters"></a>参数

将参数传递到模板规格与将参数传递到 ARM 模板的过程是完全相同的。 以内联方式添加参数值或将参数值添加到参数文件中。

若要以内联方式传递参数，请使用：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -StorageAccountType Standard_GRS
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters storageAccountType='Standard_GRS'
```

---

若要创建本地参数文件，请使用：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "StorageAccountType": {
      "value": "Standard_GRS"
    }
  }
}
```

并使用以下命令传递该参数文件：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -TemplateParameterFile ./mainTemplate.parameters.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters "./mainTemplate.parameters.json"
```

---

## <a name="create-a-template-spec-with-linked-templates"></a>创建具有链接模板的模板规格

如果模板规格的主模板引用了链接模板，则 PowerShell 和 CLI 命令可以自动查找并打包本地驱动器中的链接模板。 无需手动配置存储帐户或存储库即可托管模板规格 - 模板规格资源中包含了所有内容。

以下示例中包含了具有两个链接模板的主模板。 该示例只是模板的摘录。 请注意，它使用名为 `relativePath` 的属性链接到其他模板。 必须为部署资源使用 `2020-06-01` 的 `apiVersion` 或更高版本。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/webapp.json"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/database.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

在上一个示例中执行用于创建模板规格的 PowerShell 或 CLI 命令时，该命令会查找三个文件 - 主模板、Web 应用模板 (`webapp.json`) 和数据库模板 (`database.json`) - 并将它们打包到模板规格中。

有关详细信息，请参阅[教程：创建具有链接模板的模板规格](template-specs-create-linked.md)。

## <a name="deploy-template-spec-as-a-linked-template"></a>将模板规格部署为链接模板

创建模板规格之后，可以轻松从 ARM 模板或其他模板规格中重复使用它。可以通过将模板规格的资源 ID 添加到模板来链接该模板规范。 部署主模板时，会自动部署链接的模板规格。 此行为使你能够开发模块化模板规范并根据需要重复利用。

例如，可以创建一个用于部署网络资源的模板模板规格和一个用于部署存储资源的模板规格。 在 ARM 模板中，可随时在需要配置网络或存储资源时链接到这两个模板规格。

以下示例与前面的示例的类似，不同的是使用 `id` 属性链接到模板规格，而不是使用 `relativePath` 属性链接到本地模板。 使用 `2020-06-01` 作为部署资源的 API 版本。 在此示例中，模板规格位于名为 templateSpecsRG 的资源组中。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "networkingDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0')]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
        }
      }
    }
  ],
  "outputs": {}
}
```

有关链接模板规格的详细信息，请参阅[教程：将模板规格部署为链接模板](template-specs-deploy-linked-template.md)。

## <a name="versioning"></a>版本控制

创建模板规格时，需要为其提供版本号。 循环访问模板代码时，可以更新现有版本（获取修补程序）或发布新版本。 版本是文本字符串。 可以选择遵循任意版本控制系统，包括语义化版本控制。 模板规格的用户可以提供部署模板规格时要使用的版本号。

## <a name="next-steps"></a>后续步骤

* 若要创建和部署模板规格，请参阅[快速入门：创建和部署模板规格](quickstart-create-template-specs.md)。

* 有关链接模板规格中的模板的详细信息，请参阅[教程：创建具有链接模板的模板规格](template-specs-create-linked.md)。

* 有关将模板规格部署为链接模板的详细信息，请参阅[教程：将模板规格部署为链接模板](template-specs-deploy-linked-template.md)。
