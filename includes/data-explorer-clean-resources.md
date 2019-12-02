---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: 9598724db630db7fc48545b58f2b0c1cad464aa4
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667857"
---
## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。 

### <a name="clean-up-resources-using-the-azure-portal"></a>使用 Azure 门户清理资源

按照[清理资源](../articles/data-explorer/create-cluster-database-portal.md#clean-up-resources)中的步骤操作，删除 Azure 门户中的资源。

### <a name="clean-up-resources-using-powershell"></a>使用 PowerShell 清理资源

如果 Cloud Shell 仍处于打开状态，则无需复制/运行第一行（读取主机）。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```