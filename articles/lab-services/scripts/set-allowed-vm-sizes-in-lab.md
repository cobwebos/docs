---
title: PowerShell 脚本：在 Azure 实验室服务中设置允许的 VM 大小 | Microsoft Docs
description: 此 PowerShell 脚本在 Azure 实验室服务中设置允许的 VM 大小。
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
ms.openlocfilehash: 2fc7001e5594b1c37e6566cde1ee586194737e3c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>使用 PowerShell 在 Azure 实验室服务中设置允许的 VM 大小

此示例 PowerShell 脚本在 Azure 实验室服务中设置允许的虚拟机 (VM) 大小。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>先决条件
* **自定义实验室**。 此脚本要求拥有现有的自定义实验室。 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a custom lab")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 说明 |
|---|---|
| [Find-AzureRmResource](/module/azurerm.resources/find-azurermresource) | 基于指定参数搜索资源。 |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | 获取资源。 |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | 修改资源。 |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | 创建资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure 实验室服务 PowerShell 示例](../samples-powershell.md)中找到其他 Azure 实验室服务 PowerShell 脚本示例。