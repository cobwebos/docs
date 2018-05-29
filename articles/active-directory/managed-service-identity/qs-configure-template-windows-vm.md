---
title: 如何使用模板在 Azure VM 上配置 MSI
description: 分步介绍了如何使用 Azure 资源管理器模板在 Azure VM 上配置托管服务标识 (MSI)。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 8c955e6ad9d47c6963a1c136600761fddee03835
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930268"
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>使用模板配置 VM 托管服务标识

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

托管服务标识为 Azure 服务提供了 Azure Active Directory 中的自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

在本文中，你将了解如何使用 Azure 资源管理器部署模板在 Azure VM 上执行以下托管服务标识操作：

## <a name="prerequisites"></a>先决条件

- 如果不熟悉托管服务标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配标识与用户分配标识之间的差异](overview.md#how-does-it-work)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。

## <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板

与 Azure 门户和脚本一样，[Azure 资源管理器](../../azure-resource-manager/resource-group-overview.md)模板支持部署由 Azure 资源组定义的新资源或修改后的资源。 有多种可用于执行模板编辑和部署的方法（包括本地方法和基于门户的方法），包括：

   - 使用 [Azure Marketplace 中的自定义模板](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)，这样可以从头开始创建模板，也可以在现有常见模板或[快速入门模板](https://azure.microsoft.com/documentation/templates/)的基础之上操作。
   - 派生自现有资源组，具体方法是从[原始部署](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history)或[当前部署](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)导出模板。
   - 使用本地 [JSON 编辑器（例如 VS Code）](../../azure-resource-manager/resource-manager-create-first-template.md)，然后使用 PowerShell 或 CLI 进行上传和部署。
   - 使用 Visual Studio [Azure 资源组项目](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)同时创建和部署模板。  

无论选择哪种方法，在初始部署和重新部署期间，模板语法都是相同的。 在新的或现有的 VM 上启用系统或用户分配的标识所采用的方式是相同的。 此外，默认情况下，Azure 资源管理器还会对部署执行[增量更新](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)。

## <a name="system-assigned-identity"></a>系统分配标识

在此部分中，将使用 Azure 资源管理器模板启用和禁用系统分配标识。

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>在创建 Azure VM 的过程中或在现有 VM 上启用系统分配标识

1. 无论是在本地登录到 Azure 还是通过 Azure 门户登录，请使用与包含 VM 的 Azure 订阅关联的帐户。 还需要确保该帐户属于可授予对 VM 的写权限的角色（例如，“虚拟机参与者”角色）。

2. 在编辑器中加载模板后，在 `resources` 部分中找到相关的 `Microsoft.Compute/virtualMachines` 资源。 你的屏幕截图可能与下面的略有不同，具体取决于所使用的编辑器，以及编辑后的模板是用于新部署，还是用于现有部署。

   >[!NOTE] 
   > 此示例假定变量（如 `vmName`、`storageAccountName` 和 `nicName`）已在模板中定义。
   >

   ![模板的屏幕截图 - 查找 VM](../media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. 若要启用系统分配标识，请在与 `"type": "Microsoft.Compute/virtualMachines"` 属性相同的级别添加 `"identity"` 属性。 使用以下语法：

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. （可选）将 VM MSI 扩展添加为 `resources` 元素。 此步骤是可选的，因为你也可以使用 Azure 实例元数据服务 (IMDS) 标识终结点来检索令牌。  使用以下语法：

   >[!NOTE] 
   > 下面的示例假定正在部署的是 Windows VM 扩展 (`ManagedIdentityExtensionForWindows`)。 对于 Linux，还可以改用 `ManagedIdentityExtensionForLinux` 来配置 `"name"` 和 `"type"` 元素。
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
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

5. 完成后，模板应当类似于以下示例：

   ![更新后的模板的屏幕截图](../media/msi-qs-configure-template-windows-vm/template-file-after.png)

### <a name="disable-a-system-assigned-identity-from-an-azure-vm"></a>从 Azure VM 中禁用系统分配标识

> [!NOTE]
> 目前不支持从虚拟机中禁用托管服务标识。 同时，你可以切换使用系统分配标识和用户分配标识。

如果 VM 不再需要托管服务标识，请执行以下操作：

1. 无论是在本地登录到 Azure 还是通过 Azure 门户登录，请使用与包含 VM 的 Azure 订阅关联的帐户。 还需要确保该帐户属于可授予对 VM 的写权限的角色（例如，“虚拟机参与者”角色）。

2. 将标识类型更改为 `UserAssigned`。

## <a name="user-assigned-identity"></a>用户分配标识

在此部分中，将使用 Azure 资源管理器模板向 Azure VM 分配用户分配标识。

> [!Note]
> 若要使用 Azure 资源管理器模板创建用户分配标识，请参阅[创建用户分配标识](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity)。

 ### <a name="assign-a-user-assigned-identity-to-an-azure-vm"></a>向 Azure VM 分配用户分配标识

1. 在 `resources` 元素下添加以下条目，以向 VM 分配用户分配标识。  请务必将 `<USERASSIGNEDIDENTITY>` 替换为你创建的用户分配标识的名称。
    ```json
    {
        "apiVersion": "2017-12-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/<USERASSIGNEDIDENTITYNAME>)']"
            ]
        },
    ```
    
2. （可选）接下来，在 `resources` 元素下添加以下条目，以向 VM 分配托管标识扩展。 此步骤是可选的，因为你也可以使用 Azure 实例元数据服务 (IMDS) 标识终结点来检索令牌。 使用以下语法：
    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
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
    ```
    
3.  完成后，模板应当类似于以下示例：

      ![用户分配标识的屏幕截图](./media/qs-configure-template-windows-vm/qs-configure-template-windows-vm-ua-final.PNG)


## <a name="related-content"></a>相关内容

- 若要更广泛地了解 MSI，请阅读[托管服务标识概述](overview.md)。

