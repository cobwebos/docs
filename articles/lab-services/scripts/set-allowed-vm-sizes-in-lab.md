---
title: PowerShell 脚本：在 Azure 实验室服务中设置允许的 VM 大小 | Microsoft Docs
description: 本文包含一个示例 PowerShell 脚本，用于在 Azure 实验室服务中设置允许的虚拟机（VM）大小。
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a1b0e9a4aed475f04ec8dcffa9bc95b7c7c713e1
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760434"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>使用 PowerShell 在 Azure 实验室服务中设置允许的 VM 大小

此示例 PowerShell 脚本在 Azure 实验室服务中设置允许的虚拟机 (VM) 大小。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>必备组件
* 实验室。 此脚本要求拥有现有的实验室。 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 说明 |
|---|---|
| Find-AzResource | 基于指定参数搜索资源。 |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 获取资源。 |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | 修改资源。 |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | 创建资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](https://docs.microsoft.com/powershell/)。

可以在 [Azure 实验室服务 PowerShell 示例](../samples-powershell.md)中找到其他 Azure 实验室服务 PowerShell 脚本示例。
