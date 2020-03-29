---
title: 在 Azure 开发人员测试实验室中自动添加实验室用户 |微软文档
description: 本文演示如何使用 Azure 资源管理器模板、PowerShell 和 CLI 自动将用户添加到 Azure 开发人员测试实验室中的实验室。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 70a6359923734c83590d4677bb2c93966c925d14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718133"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>自动将实验室用户添加到 Azure 开发人员测试实验室中的实验室
Azure 开发人员测试实验室允许您使用 Azure 门户快速创建自助服务开发测试环境。 但是，如果您有多个团队和多个 DevTest Labs 实例，则自动化创建过程可以节省时间。 [Azure 资源管理器模板](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)允许您创建实验室、实验室 VM、自定义映像、公式，并自动添加用户。 本文特别侧重于将用户添加到 DevTest Labs 实例。

要将用户添加到实验室，您需要将该用户添加到实验室的**DevTest Labs 用户**角色。 本文演示如何使用以下方法之一自动将用户添加到实验室：

- Azure 资源管理器模板
- Azure PowerShell cmdlet 
- Azure CLI。

## <a name="use-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板
以下示例资源管理器模板指定要添加到实验室的**DevTest Labs 用户**角色的用户。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

如果要在创建实验室的同一模板中分配角色，请记住在角色分配资源和实验室之间添加依赖项。 有关详细信息，请参阅在[Azure 资源管理器模板一文中定义依赖项](../azure-resource-manager/templates/define-resource-dependency.md)。

### <a name="role-assignment-resource-information"></a>角色分配资源信息
角色分配资源需要指定类型和名称。

需要注意的第一件事是，资源的类型与资源组的类型不`Microsoft.Authorization/roleAssignments`相同。  相反，资源类型遵循模式`{provider-namespace}/{resource-type}/providers/roleAssignments`。 在这种情况下，资源类型将为`Microsoft.DevTestLab/labs/providers/roleAssignments`。

角色分配名称本身需要全局唯一。  赋值的名称使用模式`{labName}/Microsoft.Authorization/{newGuid}`。 `newGuid`是模板的参数值。 它确保角色分配名称是唯一的。 由于没有用于创建 GUID 的模板函数，因此您需要使用任何 GUID 生成器工具自行生成 GUID。  

在模板中，角色分配的名称由`fullDevTestLabUserRoleName`变量定义。 模板的确切行是：

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>角色分配资源属性
角色分配本身定义三个属性。 它需要`roleDefinitionId`和`principalId`。 `scope`

### <a name="role-definition"></a>角色定义
角色定义 ID 是现有角色定义的字符串标识符。 角色 ID 在 窗体`/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`中。 

订阅 ID 是使用`subscription().subscriptionId`模板函数获取的。  

您需要获取`DevTest Labs User`内置角色的角色定义。 要获取[开发人员测试实验室用户](../role-based-access-control/built-in-roles.md#devtest-labs-user)角色的 GUID，可以使用[角色分配 REST API](/rest/api/authorization/roleassignments)或[获取 ARole 定义](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0)cmdlet。

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

角色 ID 在变量部分中定义，并命名为`devTestLabUserRoleId`。 在模板中，角色 ID 设置为：111111111-0000-0000-1111111111111111111111。 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>主体 ID
主体 ID 是要作为实验室用户添加到实验室的用户、组或服务主体的对象 ID。 模板使用 作为`ObjectId`参数。

您可以使用[获取 AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0)、[获取 AzureRMADGroup 或[获取 AzureRMAD 服务主](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0)电源 Shell cmdlet 获得 ObjectId。 这些 cmdlet 返回具有 ID 属性（即您需要的对象 ID）的单个或活动目录对象的列表。 下面的示例演示如何获取公司中单个用户的对象 ID。

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

您还可以使用 Azure 活动目录 PowerShell cmdlet，其中包括[获取 MsolUser、Get-MsolGroup](/powershell/module/msonline/get-msoluser?view=azureadps-1.0)和[Get-MsolService 原则](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0)。 [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)

### <a name="scope"></a>范围
范围指定角色分配应为其应用的资源或资源组。 对于资源，作用域以： `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`。 `subscription().subscriptionId`模板使用函数`subscription-id`填充零件和`resourceGroup().name`模板函数来填充`resource-group-name`零件。 使用这些函数意味着要为其分配角色的实验室必须存在于当前订阅中，并且与模板部署到的同一资源组中存在。 最后一`resource-name`部分， 是实验室的名称。 此值通过此示例中的模板参数接收。 

模板中的角色范围： 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>部署模板
首先，创建一个参数文件（例如：azuredeploy.parameters.json），该文件传递资源管理器模板中参数的值。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

然后，使用[新 AzureRmResourceResourceResourceGroup 部署](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0)电源 Shell cmdlet 部署资源管理器模板。 以下示例命令将人员、组或服务主体分配给实验室的 DevTest Labs 用户角色。

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

请务必注意，组部署名称和角色分配 GUID 需要是唯一的。 如果尝试使用非唯一 GUID 部署资源分配，则将出现`RoleAssignmentUpdateNotPermitted`错误。

如果计划多次使用该模板将多个活动目录对象添加到实验室的 DevTest Labs 用户角色，请考虑在 PowerShell 命令中使用动态对象。 下面的示例使用[New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) cmdlet 动态指定资源组部署名称和角色分配 GUID。

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>使用 Azure PowerShell
如简介中所述，您将创建新的 Azure 角色分配，以将用户添加到实验室的**DevTest Labs 用户**角色。 在 PowerShell 中，通过使用["新建 Azure 皇家分配](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0)"cmdlet 执行此操作。 此 cmdlet 具有许多可选参数，允许灵活性。 可以`ObjectId`指定`SigninName`为`ServicePrincipalName`授予权限的对象。  

下面是一个示例 Azure PowerShell 命令，该命令将用户添加到指定实验室中的 DevTest Labs 用户角色。

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

指定授予权限的资源`ResourceName`可以由 的 合并`ResourceType`指定 ，`ResourceGroup`或 参数。 `scope` 无论使用参数组合如何，都向 cmdlet 提供足够的信息，以唯一标识 Active Directory 对象（用户、组或服务主体）、作用域（资源组或资源）和角色定义。

## <a name="use-azure-command-line-interface-cli"></a>使用 Azure 命令行接口 （CLI）
在 Azure CLI 中，使用 命令`az role assignment create`将实验室用户添加到实验室。 有关 Azure CLI cmdlet 的详细信息，请参阅[使用 RBAC 和 Azure CLI 管理对 Azure 资源的访问](../role-based-access-control/role-assignments-cli.md)。

授予访问权限的对象可以由`objectId`、`signInName`参数`spn`指定。 授予对象访问权限的实验室可以通过`scope`url 或`resource-name`和`resource-type``resource-group`参数的组合进行标识。

以下 Azure CLI 示例演示如何将人员添加到指定实验室的 DevTest Labs 用户角色。  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [使用 Azure CLI 通过开发测试实验室创建和管理虚拟机](devtest-lab-vmcli.md)
- [使用 Azure PowerShell 使用开发人员测试实验室创建虚拟机](devtest-lab-vm-powershell.md)
- [使用命令行工具启动和停止 Azure 开发人员测试实验室虚拟机](use-command-line-start-stop-virtual-machines.md)

