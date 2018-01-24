---
title: "如何使用 Azure 模板为 Azure VM 配置用户分配的 MSI"
description: "分步介绍了如何使用 Azure 资源管理器模板为 Azure VM 配置用户分配的托管服务标识 (MSI)。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d97f0fa2d6c1c92aaa3d5c74dd6715de00d32438
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2017
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-template"></a>使用 Azure 模板为 VM 配置用户分配的托管服务标识 (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

托管服务标识在 Azure Active Directory 中为 Azure 服务提供托管标识。 此标识可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中输入凭据了。 

本文介绍如何使用 Azure 资源管理器部署模板为 Azure VM 启用和删除用户分配的 MSI。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>在 Azure VM 创建过程中或在现有 VM 上启用 MSI

与 Azure 门户和脚本一样，Azure 资源管理器模板支持部署由 Azure 资源组定义的新资源或修改后的资源。 有多种可用于执行模板编辑和部署的方法（包括本地方法和基于门户的方法），包括：

   - 使用 [Azure Marketplace 中的自定义模板](~/articles/azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)，这样可以从头开始创建模板，也可以在现有常见模板或[快速入门模板](https://azure.microsoft.com/documentation/templates/)的基础之上操作。
   - 派生自现有资源组，具体方法是从[原始部署](~/articles/azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history)或[当前部署](~/articles/azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)导出模板。
   - 使用本地 [JSON 编辑器（例如 VS Code）](~/articles/azure-resource-manager/resource-manager-create-first-template.md)，然后使用 PowerShell 或 CLI 进行上传和部署。
   - 使用 Visual Studio [Azure 资源组项目](~/articles/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)同时创建和部署模板。  

无论选择哪种方法，在初始部署和重新部署期间，模板语法都是相同的。 创建用户分配的 MSI 并将其分配给一个新的或现有的 VM，采用的方式是相同的。 此外，默认情况下，Azure 资源管理器还会对部署执行[增量更新](~/articles/azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)：

1. 无论是在本地登录到 Azure 还是通过 Azure 门户登录，请使用与包含 MSI 和 VM 的 Azure 订阅关联的帐户。 还需要确保该帐户属于可授予对订阅或资源的写入权限的角色（例如，“所有者”角色）。

2. 在编辑器中加载模板后，在 `resources` 部分中找到相关的 `Microsoft.Compute/virtualMachines` 资源。 你的屏幕截图可能与下面的略有不同，具体取决于所使用的编辑器，以及编辑后的模板是用于新部署，还是用于现有部署。

   >[!NOTE] 
   > 此示例假定变量（如 `vmName`、`storageAccountName` 和 `nicName`）已在模板中定义。
   >

   ![模板的屏幕截图 - 查找 VM](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. 在与 `"type": "Microsoft.Compute/virtualMachines"` 属性相同的级别添加 `"identity"` 属性。 使用以下语法：

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. 然后，将 VM MSI 扩展添加为 `resources` 元素。 使用以下语法：

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

   ![更新后的模板的屏幕截图](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>在 Azure VM 上禁用 MSI

如果 VM 不再需要 MSI，请执行以下操作：

1. 无论是在本地登录到 Azure 还是通过 Azure 门户登录，请使用与包含 VM 的 Azure 订阅关联的帐户。 还需要确保该帐户属于可授予对 VM 的写权限的角色（例如，“虚拟机参与者”角色）。

2. 删除上一部分中添加的两个元素：VM 的 `"identity"` 属性和 `"Microsoft.Compute/virtualMachines/extensions"` 资源。

## <a name="related-content"></a>相关内容

- 若要更广泛地了解 MSI，请阅读[托管服务标识概述](msi-overview.md)。

