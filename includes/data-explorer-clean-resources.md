---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: 9598724db630db7fc48545b58f2b0c1cad464aa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667857"
---
## <a name="clean-up-resources"></a>清理资源

不再需要 Azure 资源时，请通过删除资源组来清理部署的资源。 

### <a name="clean-up-resources-using-the-azure-portal"></a>使用 Azure 门户清理资源

按[清理资源](../articles/data-explorer/create-cluster-database-portal.md#clean-up-resources)中的步骤删除 Azure 门户中的资源。

### <a name="clean-up-resources-using-powershell"></a>使用 PowerShell 清理资源

如果 Cloud Shell 仍处于打开状态，则无需复制/运行第一行 (Read-Host)。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```