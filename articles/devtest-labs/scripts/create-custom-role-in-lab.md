---
title: PowerShell-在 Azure 开发测试实验室的实验室中创建自定义角色
description: 本文提供了一个 Azure PowerShell 脚本，该脚本将外部用户添加到 Azure 开发测试实验室中的实验室。
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 9b6e4d47babd17916e31ab2762ec87f6aa433da6
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136195"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>使用 PowerShell 在 Azure 开发测试实验室的实验室中创建自定义角色

此示例 PowerShell 脚本在 Azure 开发测试实验室的实验室中创建要使用的自定义角色。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>先决条件
* 实验室****。 此脚本要求拥有现有的实验室。 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 说明 |
|---|---|
| [AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | 使用 Azure RBAC 获取作为安全对象的 Azure 资源提供程序的操作。 |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | 列出可用于分配的所有 Azure 角色。 |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | 创建自定义角色。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。

可以在 [Azure 实验室服务 PowerShell 示例](../samples-powershell.md)中找到其他 Azure 实验室服务 PowerShell 脚本示例。
