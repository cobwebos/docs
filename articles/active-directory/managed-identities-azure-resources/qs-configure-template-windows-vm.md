---
title: 如何使用模板在 Azure VM 上配置 Azure 资源的托管标识
description: 分步说明如何使用 Azure 资源管理器模板在 Azure VM 上配置 Azure 资源的托管标识。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: f50714538cdfd1a0bd258a3b78d0885e3c7beed1
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2018
ms.locfileid: "48018479"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-a-templates"></a>使用模板在 Azure VM 上配置 Azure 资源的托管标识

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文将介绍如何在 Azure VM 上使用 Azure 资源管理器部署模板来执行以下 Azure 资源的托管标识操作：

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 管理器部署模板，请查看[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#how-does-it-work)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 若要执行本文中的管理操作，帐户需要以下基于 Azure 角色的访问控制分配：

    > [!NOTE]
    > 无需其他 Azure AD 目录角色分配。

    - [虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)，可创建 VM，并从 Azure VM 启用和删除系统和/或用户分配的托管标识。
    - [托管标识参与者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色，可以创建用户分配的托管标识。
    - [托管标识操作员](/azure/role-based-access-control/built-in-roles#managed-identity-operator)角色，可在 VM 中分配和删除用户分配的托管标识。

## <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板

与 Azure 门户和脚本一样，[Azure 资源管理器](../../azure-resource-manager/resource-group-overview.md)模板支持部署由 Azure 资源组定义的新资源或修改后的资源。 有多种可用于执行模板编辑和部署的方法（包括本地方法和基于门户的方法），包括：

   - 使用 [Azure Marketplace 中的自定义模板](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)，这样可以从头开始创建模板，也可以在现有常见模板或[快速入门模板](https://azure.microsoft.com/documentation/templates/)的基础之上操作。
   - 派生自现有资源组，具体方法是从[原始部署](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history)或[当前部署](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)导出模板。
   - 使用本地 [JSON 编辑器（例如 VS Code）](../../azure-resource-manager/resource-manager-create-first-template.md)，然后使用 PowerShell 或 CLI 进行上传和部署。
   - 使用 Visual Studio [Azure 资源组项目](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)同时创建和部署模板。  

无论选择哪种方法，在初始部署和重新部署期间，模板语法都是相同的。 在新的或现有 VM 上启用系统或用户分配的托管标识所采用的方式是相同的。 此外，默认情况下，Azure 资源管理器还会对部署执行[增量更新](../../azure-resource-manager/deployment-modes.md)。

## <a name="system-assigned-managed-identity"></a>系统分配的托管标识

在此部分中，将使用 Azure 资源管理器模板启用和禁用系统分配的托管标识。

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>在创建 Azure VM 的过程中或在现有 VM 上启用系统分配的托管标识

1. 无论是在本地登录到 Azure 还是通过 Azure 门户登录，请使用与包含 VM 的 Azure 订阅关联的帐户。

2. 若要启用系统分配的托管标识，请将模板加载到编辑器中，在 `resources` 节中找到所关注的 `Microsoft.Compute/virtualMachines` 资源，并在与 `"type": "Microsoft.Compute/virtualMachines"` 属性相同的级别添加 `"identity"` 属性。 使用以下语法：

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   },
   ```

3. （可选）将 Azure 资源扩展的 VM 托管标识添加为 `resources` 元素。 此步骤是可选的，因为也可以使用 Azure 实例元数据服务 (IMDS) 标识终结点来检索令牌。  使用以下语法：

   >[!NOTE] 
   > 下面的示例假定正在部署的是 Windows VM 扩展 (`ManagedIdentityExtensionForWindows`)。 对于 Linux，还可以改用 `ManagedIdentityExtensionForLinux` 来配置 `"name"` 和 `"type"` 元素。 VM 扩展计划于 2019 年 1 月弃用。
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2018-06-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.ManagedIdentity",
           "type": "ManagedIdentityExtensionForWindows",
           "typeHandlerVersion": "1.0",
           "autoUpgradeMinorVersion": true,
           "settings": {
               "port": 50342
           },
           "protectedSettings": {}
       }
   }
   ```

4. 完成后，以下各节应当会添加到模板的 `resource` 节，该节应当呈现如下：

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                },
            },
            {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForLinux')]",
            "apiVersion": "2018-06-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>向 VM 的系统分配的托管标识分配一个角色

在 VM 上启用系统分配的托管标识后，建议向其授予一个角色，例如对创建它的资源组的“读者”访问权限。

1. 无论是在本地登录到 Azure 还是通过 Azure 门户登录，请使用与包含 VM 的 Azure 订阅关联的帐户。
 
2. 将模板加载到[编辑器](#azure-resource-manager-templates)并添加以下信息，向 VM 授予对创建它的资源组的“读者”访问权限。  模板结构可能会有所不同，具体取决于所选的编辑器和部署模型。
   
   在 `parameters` 部分下添加以下代码：

    ```JSON
    "builtInRoleType": {
          "type": "string",
          "defaultValue": "Reader"
        },
        "rbacGuid": {
          "type": "string"
        }
    ```

    在 `variables` 部分下添加以下代码：

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    在 `resources` 部分下添加以下代码：

    ```JSON
    {
        "apiVersion": "2017-09-01",
         "type": "Microsoft.Authorization/roleAssignments",
         "name": "[parameters('rbacGuid')]",
         "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
                "scope": "[resourceGroup().id]"
          },
          "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ]
    }
    ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>从 Azure VM 中禁用系统分配的托管标识

如果 VM 不再需要系统分配的托管标识，请执行以下操作：

1. 无论是在本地登录到 Azure 还是通过 Azure 门户登录，请使用与包含 VM 的 Azure 订阅关联的帐户。

2. 将模板加载到[编辑器](#azure-resource-manager-templates)，并在 `resources` 部分找到相关的 `Microsoft.Compute/virtualMachines` 资源。 如果 VM 只有系统分配的托管标识，则可以将标识类型更改为 `None` 来禁用它。  
   
   **Microsoft.Compute/virtualMachines API 版本 2018-06-01**

   如果 VM 同时具有系统和用户分配的托管标识，请从标识类型中删除 `SystemAssigned` 并保留 `UserAssigned` 以及 `userAssignedIdentities` 字典值。

   **Microsoft.Compute/virtualMachines API 版本 2018-06-01**
   
   如果 `apiVersion` 为 `2017-12-01` 并且 VM 同时具有系统和用户分配的托管标识，请从标识类型中删除 `SystemAssigned` 并保留 `UserAssigned` 以及用户分配的托管标识的 `identityIds` 数组。  
   
以下示例演示如何从没有用户分配的托管标识的 VM 删除系统分配的托管标识：

```JSON
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[parameters('vmName')]",
    "location": "[resourceGroup().location]",
    "identity": { 
        "type": "None"
}
```

## <a name="user-assigned-managed-identity"></a>用户分配的托管标识

在此部分中，将使用 Azure 资源管理器模板向 Azure VM 分配用户分配的托管标识。

> [!Note]
> 要使用 Azure 资源管理器模板创建用户分配托管标识，请参阅[创建用户分配托管标识](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)。

 ### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>向 Azure VM 分配用户分配的托管标识

1. 在 `resources` 元素下添加以下条目，以向 VM 分配用户分配的托管标识。  请务必将 `<USERASSIGNEDIDENTITY>` 替换为你创建的用户分配的托管标识的名称。

   **Microsoft.Compute/virtualMachines API 版本 2018-06-01**

   如果 `apiVersion` 为 `2018-06-01`，则用户分配的托管标识以 `userAssignedIdentities` 字典格式存储，并且 `<USERASSIGNEDIDENTITYNAME>` 值必须存储在模板的 `variables` 节中定义的某个变量中。

   ```json
   {
       "apiVersion": "2018-06-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
        }
   }
   ```
   
   **Microsoft.Compute/virtualMachines API 版本 2017-12-01**
    
   如果 `apiVersion` 为 `2017-12-01`，则用户分配的托管标识存储在 `identityIds` 数组中，并且 `<USERASSIGNEDIDENTITYNAME>` 值必须存储在模板的 `variables` 节中定义的某个变量中。
    
   ```json
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```
       

2. （可选）接下来，在 `resources` 元素下添加以下条目，以便向 VM（计划于 2019 年 1 月弃用）分配托管标识扩展。 此步骤是可选的，因为也可以使用 Azure 实例元数据服务 (IMDS) 标识终结点来检索令牌。 使用以下语法：
    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
3. 完成后，以下各节应当会添加到模板的 `resource` 节，该节应当呈现如下：
   
   **Microsoft.Compute/virtualMachines API 版本 2018-06-01**    

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForLinux')]",
            "apiVersion": "2018-06-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
            }
        }
       }
    ]
   ```
   **Microsoft.Compute/virtualMachines API 版本 2017-12-01**
   
   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForLinux')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
            }
        }
       }
    ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>从 Azure VM 中删除用户分配的托管标识

如果 VM 不再需要用户分配的托管标识，请执行以下操作：

1. 无论是在本地登录到 Azure 还是通过 Azure 门户登录，请使用与包含 VM 的 Azure 订阅关联的帐户。

2. 将模板加载到[编辑器](#azure-resource-manager-templates)，并在 `resources` 部分找到相关的 `Microsoft.Compute/virtualMachines` 资源。 如果 VM 只有用户分配的托管标识，则可以通过将标识类型更改为 `None` 来禁用它。
 
   以下示例演示如何从没有系统分配的托管标识的 VM 中删除所有用户分配的托管标识：
   
   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```
   
   **Microsoft.Compute/virtualMachines API 版本 2018-06-01**
    
   若要从 VM 中删除单个用户分配的托管标识，请将其从 `useraAssignedIdentities` 字典中删除。

   如果具有系统分配的托管标识，请将其保持在 `identity` 值下的 `type` 值中。
 
   **Microsoft.Compute/virtualMachines API 版本 2017-12-01**

   若要从 VM 中删除单个用户分配的托管标识，请将其从 `identityIds` 数组中删除。

   如果具有系统分配的托管标识，请将其保持在 `identity` 值下的 `type` 值中。
   
## <a name="next-steps"></a>后续步骤

- [Azure 资源概述的托管标识](overview.md)。

