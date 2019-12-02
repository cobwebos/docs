---
title: 教程 - 使用 RBAC 和 Azure 资源管理器模板授予用户对 Azure 资源的访问权限
description: 本教程介绍如何通过 Azure 资源管理器模板使用基于角色的访问控制 (RBAC) 授予用户对 Azure 资源的访问权限。
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: ed143f85b4372348baa1d74b4ec7a7447943a74f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74418488"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>教程：使用 RBAC 和 Azure 资源管理器模板授予用户对 Azure 资源的访问权限

可以通过[基于角色的访问控制 (RBAC)](overview.md) 管理对 Azure 资源的访问权限。 在本教程中，将创建资源组并授予用户在资源组中创建和管理虚拟机的访问权限。 本教程重点介绍部署资源管理器模板以授予访问权限的过程。 有关开发资源管理器模板的详细信息，请参阅[资源管理器文档](/azure/azure-resource-manager/)和[模板参考](/azure/templates/microsoft.authorization/allversions
)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在资源组范围内为用户授予访问权限
> * 验证部署
> * 清理

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

若要添加和删除角色分配，必须具有：

* `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 权限，例如[用户访问管理员](built-in-roles.md#user-access-administrator)或[所有者](built-in-roles.md#owner)

## <a name="grant-access"></a>授予访问权限

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/)。 可在[此处](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization)找到更多与 Azure 授权相关的模板。

若要部署模板，请选择“试一试”，打开 Azure Cloud shell，然后将以下 PowerShell 脚本粘贴到 shell 窗口中  。 若要粘贴代码，请右键单击 shell 窗口并选择“粘贴”  。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>验证部署

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 打开在上一过程中创建的资源组。 默认名称是追加了 **rg** 的项目名称。
1. 从左侧菜单中选择“访问控制 (IAM)”  。
1. 选择“角色分配”  。 
1. 在“名称”中，输入在上一过程中键入的电子邮件地址  。 应看到拥有电子邮件地址的用户具有**虚拟机参与者**角色。

## <a name="clean-up"></a>清理

若要删除在上一过程中创建的资源组，请选择“试一试”，打开 Azure Cloud shell，然后将以下 PowerShell 脚本粘贴到 shell 窗口中  。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 RBAC 和 Azure PowerShell 授予用户对 Azure 资源的访问权限](tutorial-role-assignments-user-powershell.md)