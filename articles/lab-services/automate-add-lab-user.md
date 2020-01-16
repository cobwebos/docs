---
title: 在 Azure 开发测试实验室中自动添加实验室用户 |Microsoft Docs
description: 了解如何在 Azure 开发测试实验室中自动向实验室添加实验室用户。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: deec67a2c64a57bbb380b3fd87bf820499e6efed
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980055"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>自动向 Azure 开发测试实验室中的实验室添加实验室用户
Azure 开发测试实验室允许使用 Azure 门户快速创建自助服务开发测试环境。 但是，如果有多个团队和多个开发测试实验室实例，则自动执行创建过程可以节省时间。 利用[Azure 资源管理器模板](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)，你可以创建实验室、实验室 vm、自定义映像、公式，并以自动方式添加用户。 本文专门介绍如何将用户添加到开发测试实验室实例中。

若要将用户添加到实验室，请将用户添加到实验室的**开发测试实验室用户**角色。 本文说明如何使用以下方法之一自动将用户添加到实验室：

- Azure 资源管理器模板
- Azure PowerShell cmdlet 
- Azure CLI。

## <a name="use-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板
下面的示例资源管理器模板指定要添加到实验室的**开发测试实验室用户**角色的用户。 

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

如果要在创建实验室的同一模板中分配角色，请记住在角色分配资源和实验室之间添加依赖关系。 有关详细信息，请参阅[在 Azure 资源管理器模板中定义依赖项](../azure-resource-manager/templates/define-resource-dependency.md)。

### <a name="role-assignment-resource-information"></a>角色分配资源信息
角色分配资源需要指定类型和名称。

首先要注意的是，资源的类型并不是 `Microsoft.Authorization/roleAssignments` 资源组。  而资源类型则遵循模式 `{provider-namespace}/{resource-type}/providers/roleAssignments`。 在这种情况下，将 `Microsoft.DevTestLab/labs/providers/roleAssignments`资源类型。

角色分配名称本身需要是全局唯一的。  分配的名称使用模式 `{labName}/Microsoft.Authorization/{newGuid}`。 `newGuid` 是模板的参数值。 它可确保角色分配名称是唯一的。 由于没有用于创建 Guid 的模板功能，因此需要使用任何 GUID 生成器工具自行生成 GUID。  

在模板中，角色分配的名称由 `fullDevTestLabUserRoleName` 变量定义。 模板中的确切行是：

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>角色分配资源属性
角色分配本身定义了三个属性。 它需要 `roleDefinitionId`、`principalId`和 `scope`。

### <a name="role-definition"></a>角色定义
角色定义 ID 是现有角色定义的字符串标识符。 角色 ID 的格式 `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`。 

使用 `subscription().subscriptionId` 模板函数获取订阅 ID。  

需要获取 `DevTest Labs User` 内置角色的角色定义。 若要获取[开发测试实验室用户](../role-based-access-control/built-in-roles.md#devtest-labs-user)角色的 GUID，可以使用 REST API 或[AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) cmdlet 的[角色分配](/rest/api/authorization/roleassignments)。

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

角色 ID 在 variables 节和命名 `devTestLabUserRoleId`中定义。 在模板中，角色 ID 设置为：111111111-0000-0000-11111111111111111。 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>主体 ID
主体 ID 是要作为实验室用户添加到实验室的 Active Directory 用户、组或服务主体的对象 ID。 模板使用 `ObjectId` 作为参数。

可以通过使用[get-azurermaduser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0)、[Get-azurermadgroup 或[new-azurermadserviceprincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell cmdlet 来获取 ObjectId。 这些 cmdlet 返回 Active Directory 对象的单个或列表，这些对象具有 ID 属性，该属性是所需的对象 ID。 下面的示例演示如何获取公司中单个用户的对象 ID。

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

你还可以使用包含[set-msoluser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0)、 [get-msolgroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)和[new-msolserviceprincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0)的 Azure Active Directory PowerShell cmdlet。

### <a name="scope"></a>范围
作用域指定应应用角色分配的资源或资源组。 对于资源，范围格式为： `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`。 该模板使用 `subscription().subscriptionId` 函数填充 `subscription-id` 部分，并使用 `resourceGroup().name` 模板函数填充 `resource-group-name` 部分。 使用这些函数意味着你要向其分配角色的实验室必须存在于当前订阅中，以及模板部署所属的同一资源组。 最后一部分 `resource-name`是实验室的名称。 此值通过此示例中的模板参数接收。 

模板中的角色作用域： 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>部署模板
首先，创建一个参数文件（例如： azuredeploy.json），该文件传递资源管理器模板中参数的值。 

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

然后，使用[New-azurermresourcegroupdeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) PowerShell cmdlet 部署资源管理器模板。 下面的示例命令将用户、组或服务主体分配给实验室的开发测试实验室用户角色。

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

需要特别注意的是，组部署名称和角色分配 GUID 需要是唯一的。 如果尝试使用非唯一 GUID 部署资源分配，则会出现 `RoleAssignmentUpdateNotPermitted` 错误。

如果计划多次使用该模板将多个 Active Directory 对象添加到实验室的开发测试实验室用户角色，请考虑使用 PowerShell 命令中的动态对象。 以下示例使用新的[Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) cmdlet 动态指定资源组部署名称和角色分配 Guid。

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>使用 Azure PowerShell
如简介中所述，创建新的 Azure 角色分配，以便将用户添加到实验室的**开发测试实验室用户**角色。 在 PowerShell 中，可以使用[new-azurermroleassignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) cmdlet 来执行此操作。 此 cmdlet 具有多个可选参数，以提供灵活性。 可以将 `ObjectId`、`SigninName`或 `ServicePrincipalName` 指定为向其授予权限的对象。  

下面是将用户添加到指定实验室中开发测试实验室用户角色的 Azure PowerShell 命令示例。

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

若要指定要向其授予权限的资源，可以通过 `ResourceName`、`ResourceType`、`ResourceGroup` 或 `scope` 参数的组合来指定。 无论使用哪种参数组合，都可以为 cmdlet 提供足够的信息来唯一标识 Active Directory 对象（用户、组或服务主体）、作用域（资源组或资源）以及角色定义。

## <a name="use-azure-command-line-interface-cli"></a>使用 Azure 命令行接口（CLI）
在 Azure CLI 中，使用 `az role assignment create` 命令向实验室添加实验室用户。 有关 Azure CLI cmdlet 的详细信息，请参阅[使用 RBAC 和 Azure CLI 管理对 Azure 资源的访问权限](../role-based-access-control/role-assignments-cli.md)。

被授予访问权限的对象可以由 `objectId`、`signInName``spn` 参数指定。 要向其授予访问权限的实验室，可以通过 `scope` url 或 `resource-name`、`resource-type`和 `resource-group` 参数的组合来识别。

以下 Azure CLI 示例演示了如何将人员添加到指定实验室的开发测试实验室用户角色。  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [使用 Azure CLI 在开发测试实验室中创建和管理虚拟机](devtest-lab-vmcli.md)
- [使用 Azure PowerShell 创建包含开发测试实验室的虚拟机](devtest-lab-vm-powershell.md)
- [使用命令行工具启动和停止 Azure 开发测试实验室虚拟机](use-command-line-start-stop-virtual-machines.md)

