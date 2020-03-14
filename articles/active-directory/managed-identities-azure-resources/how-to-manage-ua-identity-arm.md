---
title: 创建 & 使用 Azure 资源管理器删除用户分配的托管标识
description: 分步说明如何使用 Azure 资源管理器创建和删除用户分配的托管标识。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 244965da4e22c0808fd1ea9088aa182b27eaf484
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253372"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>使用 Azure 资源管理器创建、列出和删除用户分配的托管标识


Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个托管标识。 此标识可用于向支持 Azure AD 身份验证的服务进行身份验证，这样就无需在代码中输入凭据了。 

本文将使用 Azure 资源管理器创建用户分配的托管标识。

无法使用 Azure 资源管理器模板列出和删除用户分配的托管标识。  请参阅以下文章来创建和列出用户分配的托管标识：

- [列出用户分配的托管标识](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [删除用户分配的托管标识](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>系统必备

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#how-does-the-managed-identities-for-azure-resources-work)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。

## <a name="template-creation-and-editing"></a>模板创建和编辑

与 Azure 门户和脚本一样，Azure 资源管理器模板支持部署由 Azure 资源组定义的新资源或修改后的资源。 有多种可用于执行模板编辑和部署的方法（包括本地方法和基于门户的方法），包括：

- 使用 [Azure 市场中的自定义模板](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)，这样可以从头开始创建模板，也可以在现有常见模板或[快速入门模板](https://azure.microsoft.com/documentation/templates/)的基础之上操作。
- 派生自现有资源组，具体方法是从[原始部署](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)或[当前部署](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)导出模板。
- 使用本地 [JSON 编辑器（例如 VS Code）](../../azure-resource-manager/resource-manager-create-first-template.md)，然后使用 PowerShell 或 CLI 进行上传和部署。
- 使用 Visual Studio [Azure 资源组项目](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)同时创建和部署模板。 

## <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识 

若要创建用户分配的托管标识，你的帐户需要[托管标识参与者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色分配。

若要创建用户分配的托管标识，请使用以下模板。 将 `<USER ASSIGNED IDENTITY NAME>` 值替换为自己的值：

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>后续步骤

有关如何使用 Azure 资源管理器模板向 Azure VM 分配用户分配的托管标识的信息，请参阅[使用模板在 Azure VM 上配置 Azure 资源的托管标识](qs-configure-template-windows-vm.md)。


 
