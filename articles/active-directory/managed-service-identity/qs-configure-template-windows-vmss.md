---
title: "使用模板在 Azure 虚拟机规模集上配置 MSI"
description: "分步说明如何使用 Azure 资源管理器模板在 Azure VMSS 上配置托管服务标识 (MSI)。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 7f65c2ce53e30aa8682a9ee798af9001b4f210dc
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>使用模板配置 VM 托管服务标识

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

托管服务标识 (MSI) 为 Azure 服务提供了 Azure Active Directory (Azure AD) 中的一个自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文介绍如何使用 Azure 资源管理器部署模板为 Azure 虚拟机规模集启用和删除 MSI。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set-or-an-existing-azure-virtual-machine-scale-set"></a>在创建 Azure 虚拟机规模集的过程中启用 MSI，或为现有 Azure 虚拟机规模集启用 MSI

与 Azure 门户和脚本一样，Azure 资源管理器模板支持部署由 Azure 资源组定义的新资源或修改后的资源。 有多种可用于执行模板编辑和部署的方法（包括本地方法和基于门户的方法），包括：

   - 使用 [Azure Marketplace 中的自定义模板](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)，这样可以从头开始创建模板，也可以在现有常见模板或[快速入门模板](https://azure.microsoft.com/documentation/templates/)的基础之上操作。
   - 派生自现有资源组，具体方法是从[原始部署](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history)或[当前部署](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)导出模板。
   - 使用本地 [JSON 编辑器（例如 VS Code）](../../azure-resource-manager/resource-manager-create-first-template.md)，然后使用 PowerShell 或 CLI 进行上传和部署。
   - 使用 Visual Studio [Azure 资源组项目](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)同时创建和部署模板。  

无论选择哪种方法，在初始部署和重新部署期间，模板语法都是相同的。 在新的或现有 Azure 虚拟机规模集上启用 MSI 是以相同的方式执行的。 此外，默认情况下，Azure 资源管理器还会对部署执行[增量更新](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)：

1. 无论是在本地登录到 Azure 还是通过 Azure 门户登录，请使用与包含虚拟机规模集的 Azure 订阅关联的帐户。

2. 在编辑器中加载模板后，在 `resources` 部分中找到相关的 `Microsoft.Compute/virtualMachineScaleSets` 资源。 你的屏幕截图可能与下面的略有不同，具体取决于所使用的编辑器，以及编辑后的模板是用于新部署，还是用于现有部署。
   
   ![模板的屏幕截图 - 查找 VM](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

3. 在与 `"type": "Microsoft.Compute/virtualMachineScaleSets"` 属性相同的级别添加 `"identity"` 属性。 使用以下语法：

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. 然后添加虚拟机规模集 MSI 扩展作为 `extensionsProfile` 元素。 使用以下语法：

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

5. 完成后，模板应当类似于以下示例：

   ![更新后的模板的屏幕截图](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>从 Azure 虚拟机规模集中删除 MSI

如果虚拟机规模集不再需要 MSI，请执行以下操作：

1. 无论是在本地登录到 Azure 还是通过 Azure 门户登录，请使用与包含虚拟机规模集的 Azure 订阅关联的帐户。

2. 删除上一部分中添加的两个元素：虚拟机规模集的 `"identity"` 和 `"extensionsProfile"` 属性。

## <a name="next-steps"></a>后续步骤

- 若要更广泛地了解 MSI，请阅读[托管服务标识概述](overview.md)。

