---
title: Azure PowerShell 示例 - 安装应用
description: 此脚本创建运行 Windows Server 2016 的虚拟机规模集，并使用自定义脚本扩展安装基本 Web 应用程序。
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: powershell
ms.date: 03/27/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 63487cc09ab6635967dcee79e7c0ab0041f33dde
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701983"
---
# <a name="install-applications-into-a-virtual-machine-scale-set-with-powershell"></a>使用 PowerShell 将应用程序安装到虚拟机规模集中
此脚本创建运行 Windows Server 2016 的虚拟机规模集，并使用自定义脚本扩展安装基本 Web 应用程序。 运行脚本后，可以通过 Web 浏览器访问该 Web 应用。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/install-apps/install-apps.ps1 "Install apps into a scale set")]

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
|  [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组及其中包含的所有资源。 |

## <a name="next-steps"></a>后续步骤
有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。
