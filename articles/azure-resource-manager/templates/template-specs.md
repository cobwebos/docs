---
title: 模板规格概述
description: 描述如何创建模板规范并将其与组织中的其他用户共享。
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 829aaa41bc60b3dcbf78ef6083457fff3b794914
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497794"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Azure 资源管理器模板规范（预览版）

模板规范是用于在 Azure 中存储 Azure 资源管理器模板（ARM 模板）以供以后部署的新资源类型。 此资源类型使你可以与组织中的其他用户共享 ARM 模板。 就像任何其他 Azure 资源一样，你可以使用基于角色的访问控制（RBAC）来共享模板规范。

对于模板规格，" **templateSpecs** " 是新的资源类型。 它包含主模板和任意数量的链接模板。 Azure 将模板规范安全地存储在资源组中。 模板规范支持[版本控制](#versioning)。

若要部署模板规范，请使用 Azure 等标准 Azure 工具、Azure CLI、Azure 门户、REST 以及其他受支持的 Sdk 和客户端。 使用相同的命令并传入模板的相同参数。

> [!NOTE]
> 模板规范当前为预览版。 若要使用它，必须[注册等待列表](https://aka.ms/templateSpecOnboarding)。

## <a name="why-use-template-specs"></a>为什么使用模板规范？

如果你当前在 GitHub 存储库或存储帐户中使用了模板，则在尝试共享和使用模板时遇到几个难题。 要使用户部署该模板，模板必须为本地模板或模板的 URL 必须可公开访问。 若要绕过此限制，你可以与需要部署模板的用户共享模板的副本，或打开存储库或存储帐户的访问权限。 如果用户拥有模板的本地副本，这些副本最终可能会与原始模板分离。 当你公开访问存储库或存储帐户时，可能会允许非预期用户访问该模板。

使用模板规范的好处是，你可以创建规范模板并将其与组织中的团队共享。 模板规范是安全的，因为它们适用于 Azure 资源管理器进行部署，但没有 RBAC 权限的用户无法访问。 用户只需要对模板规范的 "读取" 访问权限来部署模板，因此你可以共享模板，而无需允许其他人修改它。

你的模板规范中包含的模板应由组织中的管理员验证，以遵循组织的要求和指导。

## <a name="create-template-spec"></a>创建模板规范

以下示例显示了一个用于在 Azure 中创建存储帐户的简单模板。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ]
}
```

创建模板规范时，会将主模板文件传递给 PowerShell 或 CLI 命令。 如果主模板引用链接的模板，则命令将查找并打包它们以创建模板规范。若要了解详细信息，请参阅[使用链接模板创建模板规范](#create-a-template-spec-with-linked-templates)。

使用以下内容创建模板规范：

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -TemplateJsonFile ./mainTemplate.json
```

你可以通过使用查看订阅中的所有模板规范：

```azurepowershell
Get-AzTemplateSpec
```

可以查看模板规范的详细信息，包括其版本：

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

## <a name="deploy-template-spec"></a>部署模板规范

创建模板规范后，具有模板规范的**读取**访问权限的用户可以对其进行部署。 有关授予访问权限的信息，请参阅[教程：使用 Azure PowerShell 向组授予对 Azure 资源的访问权限](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)。

可以通过门户、PowerShell、Azure CLI 或更大模板部署中的链接模板来部署模板规范。 组织中的用户可以将模板规范部署到 Azure 中的任何作用域（资源组、订阅、管理组或租户）。

可以通过提供其资源 ID 来部署模板规范，而不是传入模板的路径或 URI。 资源 ID 的格式如下：

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

请注意，资源 ID 包括模板规范的版本号。

例如，使用以下 PowerShell 命令部署模板规范。

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

在实践中，您通常会运行 `Get-AzTemplateSpec` 以获取您要部署的模板规范的 ID。

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

## <a name="create-a-template-spec-with-linked-templates"></a>使用链接模板创建模板规范

如果模板规范的主模板引用了链接模板，则 PowerShell 和 CLI 命令可以自动查找并打包本地驱动器中的链接模板。 无需手动配置存储帐户或存储库即可托管模板规范-模板规范资源中的所有内容都是自包含的。

下面的示例包含具有两个链接模板的主模板。 该示例只是模板的摘录。 请注意，它使用名为的属性 `relativePath` 链接到其他模板。 你必须 `apiVersion` `2020-06-01` 为部署资源使用或更高版本。

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

如果对前面的示例执行了创建模板规范的 PowerShell 或 CLI 命令，该命令将查找三个文件-主模板、web 应用模板（ `webapp.json` ）和数据库模板（ `database.json` ），并将其打包到模板规范。

有关详细信息，请参阅[教程：使用链接模板创建模板规范](template-specs-create-linked.md)。

## <a name="deploy-template-spec-as-a-linked-template"></a>将模板规范部署为链接模板

一旦创建了模板规范，就可以轻松地从 ARM 模板或另一个模板规范重复使用它。可以通过将其资源 ID 添加到模板来链接到模板规范。 部署主模板时，会自动部署链接模板规范。 此行为允许你开发模块化模板规范并根据需要重复使用。

例如，你可以创建一个部署网络资源的模板规范，以及另一个部署存储资源的模板规范。 在 ARM 模板中，你可以在任何需要配置网络或存储资源的情况下链接到这两个模板规范。

下面的示例与前面的示例类似，但使用 `id` 属性链接到模板规范，而不是链接 `relativePath` 到本地模板的属性。 用于 `2020-06-01` 部署资源的 API 版本。 在此示例中，模板规范位于名为**templateSpecsRG**的资源组中。

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

有关链接模板规范的详细信息，请参阅[教程：将模板规范部署为链接模板](template-specs-deploy-linked-template.md)。

## <a name="versioning"></a>版本控制

创建模板规范时，为其提供版本号。 当你循环访问模板代码时，你可以更新现有版本（对于修补程序）或发布新版本。 版本为文本字符串。 你可以选择遵循任何版本控制系统，包括语义版本控制。 模板规范的用户可以提供要在部署时使用的版本号。

## <a name="next-steps"></a>后续步骤

* 若要创建和部署模板规范，请参阅[快速入门：创建和部署模板规范](quickstart-create-template-specs.md)。

* 有关模板规范中链接模板的详细信息，请参阅[教程：使用链接模板创建模板规范](template-specs-create-linked.md)。

* 有关将模板规范部署为链接模板的详细信息，请参阅[教程：将模板规范部署为链接模板](template-specs-deploy-linked-template.md)。
