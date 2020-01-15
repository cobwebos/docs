---
title: Azure PowerShell 示例 - 区域冗余规模集
description: 此脚本创建跨多个可用性区域运行 Windows Server 2016 的虚拟机规模集。
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 424b2d84239c6efb4e2ab5a9e20e4ba9595051c4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452453"
---
# <a name="create-a-zone-redundant-virtual-machine-scale-set-with-powershell"></a>使用 PowerShell 创建区域冗余虚拟机规模集
此脚本创建跨多个可用性区域运行 Windows Server 2016 的虚拟机规模集。 运行脚本后，可通过 RDP 访问虚拟机。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1 "Create zone-redundant scale set")]

## <a name="clean-up-deployment"></a>清理部署
运行以下命令可删除资源组、规模集和所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>脚本说明
此脚本使用以下命令创建部署。 表中的每一项均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | 创建虚拟机规模集和所有支持资源，包括虚拟网络、负载均衡器和 NAT 规则。 |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | 获取有关虚拟机规模集的信息。 |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | 添加自定义脚本的 VM 扩展以安装基本的 Web 应用程序。 |
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss) | 更新虚拟机规模集模型以应用 VM 扩展。 |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | 获取有关负载均衡器使用的分配公共 IP 地址的信息。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组及其中包含的所有资源。 |


## <a name="next-steps"></a>后续步骤
有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure 虚拟机规模集文档](../powershell-samples.md)中找到其他虚拟机规模集 PowerShell 脚本示例。
