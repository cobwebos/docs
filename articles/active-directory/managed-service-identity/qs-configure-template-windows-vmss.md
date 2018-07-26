---
title: 使用模板在 Azure 虚拟机规模集上配置 MSI
description: 分步说明如何使用 Azure 资源管理器模板在 Azure VMSS 上配置托管服务标识 (MSI)。
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: ab3982c85cfb008bde08495f8cb8aa86d066d8c0
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114848"
---
# <a name="configure-a-vmss-managed-service-identity-by-using-a-template"></a>使用模板配置 VMSS 托管服务标识

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

托管服务标识为 Azure 服务提供了 Azure Active Directory 中的自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

在本文中，你将了解如何使用 Azure 资源管理器部署模板在 Azure VMSS 上执行以下托管服务标识操作：
- 在 Azure VMSS 上启用和禁用系统分配标识
- 在 Azure VMSS 上添加和删除用户分配标识

## <a name="prerequisites"></a>先决条件

- 如果不熟悉托管服务标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配标识与用户分配标识之间的差异](overview.md#how-does-it-work)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。

## <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板

与 Azure 门户和脚本一样，[Azure 资源管理器](../../azure-resource-manager/resource-group-overview.md)模板支持部署由 Azure 资源组定义的新资源或修改后的资源。 有多种可用于执行模板编辑和部署的方法（包括本地方法和基于门户的方法），包括：

   - 使用 [Azure 市场中的自定义模板](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)，这样可以从头开始创建模板，也可以在现有常见模板或[快速入门模板](https://azure.microsoft.com/documentation/templates/)的基础之上操作。
   - 派生自现有资源组，具体方法是从[原始部署](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history)或[当前部署](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)导出模板。
   - 使用本地 [JSON 编辑器（例如 VS Code）](../../azure-resource-manager/resource-manager-create-first-template.md)，然后使用 PowerShell 或 CLI 进行上传和部署。
   - 使用 Visual Studio [Azure 资源组项目](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)同时创建和部署模板。  

无论选择哪种方法，在初始部署和重新部署期间，模板语法都是相同的。 在新的或现有 VM 上启用 MSI 是以相同的方式执行的。 此外，默认情况下，Azure 资源管理器还会对部署执行[增量更新](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)。

## <a name="system-assigned-identity"></a>系统分配标识

在此部分中，将使用 Azure 资源管理器模板启用和禁用系统分配标识。

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>在创建 Azure VMSS 的过程中或在现有 Azure VMSS 上启用系统分配标识

1. 将模板加载到编辑器中，并在 `resources` 节中找到相关的 `Microsoft.Compute/virtualMachineScaleSets` 资源。 你的屏幕截图可能与下面的略有不同，具体取决于所使用的编辑器，以及编辑后的模板是用于新部署，还是用于现有部署。
   
   ![模板的屏幕截图 - 查找 VM](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. 若要启用系统分配标识，请在与 `"type": "Microsoft.Compute/virtualMachineScaleSets"` 属性相同的级别添加 `"identity"` 属性。 使用以下语法：

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. （可选）添加虚拟机规模集 MSI 扩展作为 `extensionsProfile` 元素。 此步骤是可选的，因为你也可以使用 Azure 实例元数据服务 (IMDS) 标识来检索令牌。  使用以下语法：

   >[!NOTE] 
   > 下面的示例假定正在部署的是 Windows 虚拟机规模集扩展 (`ManagedIdentityExtensionForWindows`)。 对于 Linux，还可以改用 `ManagedIdentityExtensionForLinux` 来配置 `"name"` 和 `"type"` 元素。
   >

   ```JSON
   "extensionProfile": {
        "extensions": [
            {
                "name": "MSIWindowsExtension",
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

4. 完成后，模板应当类似于以下示例：

   ![更新后的模板的屏幕截图](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>从 Azure 虚拟机规模集中禁用系统分配标识

> [!NOTE]
> 目前不支持从虚拟机中禁用托管服务标识。 同时，你可以切换使用系统分配标识和用户分配标识。

如果某个虚拟机规模集不再需要系统分配标识，但仍需要用户分配标识：

- 将模板加载到编辑器中，并将标识类型更改为 `'UserAssigned'`

## <a name="user-assigned-identity"></a>用户分配标识

在此部分中，将使用 Azure 资源管理器模板向 Azure VMSS 分配用户分配标识。

> [!Note]
> 若要使用 Azure 资源管理器模板创建用户分配标识，请参阅[创建用户分配标识](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity)。

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>向 Azure VMSS 分配用户分配标识

1. 在 `resources` 元素下添加以下条目，以向 VMSS 分配用户分配标识。  请务必将 `<USERASSIGNEDIDENTITY>` 替换为你创建的用户分配标识的名称。

   > [!Important]
   > 以下示例中显示的 `<USERASSIGNEDIDENTITYNAME>` 值必须存储在变量中。  此外，对于当前支持的实现（在资源管理器模板中将用户分配的标识分配给虚拟机），API 版本必须与以下示例中的版本匹配。 

    ```json
    {
        "name": "[variables('vmssName')]",
        "apiVersion": "2017-03-30",
        "location": "[parameters(Location')]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
            ]
        }

    }
    ```
2. （可选）在 `extensionProfile` 元素下添加以下条目，以向 VMSS 分配托管标识扩展。 此步骤是可选的，因为你也可以使用 Azure 实例元数据服务 (IMDS) 标识终结点来检索令牌。 使用以下语法：
   
    ```JSON
       "extensionProfile": {
            "extensions": [
                {
                    "name": "MSIWindowsExtension",
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
3.  完成后，模板应当类似于以下示例：
   
      ![用户分配标识的屏幕截图](./media/qs-configure-template-windows-vmss/qs-configure-template-windows-final.PNG)

## <a name="next-steps"></a>后续步骤

- 若要更广泛地了解 MSI，请阅读[托管服务标识概述](overview.md)。

