---
title: PowerShell 脚本：将外部用户添加到 Azure 开发测试实验室中的实验室 | Microsoft Docs
description: 此 PowerShell 脚本将外部用户添加到 Azure 开发测试实验室中的实验室。
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: bd86e44c21ca3c0b9c061f9a5c24bd19c4b207f9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636437"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>使用 PowerShell 将外部用户添加到 Azure 开发测试实验室中的实验室

此示例 PowerShell 脚本将外部用户添加到 Azure 开发测试实验室中的实验室。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>先决条件
* 实验室。 此脚本要求拥有现有的实验室。 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 说明 |
|---|---|
| [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser) | 从 Azure active directory 重试用户对象。 |
| [New-AzureRmRoleAssignment](/module/azurerm.resources/new-azurermroleassignment) | 在指定范围内将指定的角色分配给指定的主体。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure 实验室服务 PowerShell 示例](../samples-powershell.md)中找到其他 Azure 实验室服务 PowerShell 脚本示例。