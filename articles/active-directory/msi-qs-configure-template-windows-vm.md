---
title: "如何使用模板在 Azure VM 上配置 MSI"
description: "逐步介绍了如何使用 Azure 资源管理器模板在 Azure VM 上配置托管服务标识 (MSI)。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 266458323ca54d9805aea12108faed79e69d30b0
ms.contentlocale: zh-cn
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-a-template"></a>使用模板配置 VM 托管服务标识 (MSI)

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

托管服务标识为 Azure 服务提供了 Azure Active Directory 中的自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文将介绍如何使用 Azure 资源管理器部署模板为 Azure Windows VM 启用和禁用 MSI。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>在 Azure VM 创建过程中或在现有 VM 上启用 MSI

与 Azure 门户和脚本一样，Azure 资源管理器模板支持部署由 Azure 资源组定义的新资源/修改后的资源。 执行模板编辑和部署的方法有多种，包括本地方法和基于门户/Web 的方法。 其中包括：

   - 使用 [Azure Marketplace 中的自定义模板](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)，这样可以从头开始创建模板，也可以在现有常见模板或[快速入门模板](https://azure.microsoft.com/documentation/templates/)的基础之上操作。
   - 派生自现有资源组，具体方法是从[原始部署](../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history)或[当前部署](../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)导出模板。
   - 使用本地 [JSON 编辑器（如 VS Code）](../azure-resource-manager/resource-manager-create-first-template.md)，再使用 PowerShell 或 CLI 上传/部署。
   - 使用 Visual Studio 的 [Azure 资源组项目](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)，以同时创建和部署模板。  

无论采用哪种方法，初始部署和重新部署期间模板语法都是一样的，这样就可以相同方式在新的或现有的 VM 上启用 MSI。 此外，默认情况下，Azure 资源管理器还会对部署执行[增量更新](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)：

1. 在编辑器中加载模板后，在 `resources` 部分中找到相关的 `Microsoft.Compute/virtualMachines` 资源。 大家的屏幕截图可能与下面的略有不同，具体取决于要使用的编辑器，以及编辑后的模板是用于新部署，还是用于现有部署：

   >[!NOTE] 
   > 第 2 步还假定模板中定义了变量 `vmName`、`storageAccountName` 和 `nicName`。
   >

   ![编辑前的模板的屏幕截图 - 查找 VM](./media/msi-qs-configure-template-windows-vm/template-file-before.png) 

2. 运行以下语法，添加与 `"type": "Microsoft.Compute/virtualMachines"` 属性的级别相同的 `"identity"` 属性：

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. 然后，运行以下语法，将 VM MSI 扩展添加为 `resources` 元素：

   >[!NOTE] 
   > 下面的示例假定正在部署的是 Windows VM 扩展 (`ManagedIdentityExtensionForWindows`)。 也可以改用 `ManagedIdentityExtensionForLinux` 为 Linux 配置。
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

4. 完成后，模板应如以下示例所示：

   ![编辑后的模板](./media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>在 Azure VM 上禁用 MSI

如果虚拟机不再需要 MSI，只需删除在上一示例中添加的两个元素（VM 的 `"identity"` 属性和 `"Microsoft.Compute/virtualMachines/extensions"` 资源），即可禁用 MSI。

## <a name="related-content"></a>相关内容

- [托管服务标识概述](msi-overview.md)

使用以下评论部分提供反馈，帮助我们改进内容。


