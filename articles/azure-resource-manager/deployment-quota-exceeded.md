---
title: 超出了 Azure 部署配额
description: 介绍如何解决在资源组历史记录中部署800以上的错误。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 10/01/2019
ms.author: tomfitz
ms.openlocfilehash: f06aff74e2cf800d44115f34921825122b09a9e7
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719430"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>在部署计数超过800时解决错误

在部署历史记录中，每个资源组限制为800个部署。 本文介绍部署失败时收到的错误，因为它会超出允许的800部署。 若要解决此错误，请从资源组历史记录中删除部署。 从历史记录中删除部署不会影响部署的任何资源。

## <a name="symptom"></a>症状

在部署期间，会收到一条错误，指出当前部署将超过800个部署的配额。

## <a name="solution"></a>解决方案

### <a name="azure-cli"></a>Azure CLI

使用[az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete)命令从历史记录中删除部署。

```azurecli-interactive
az group deployment delete --resource-group exampleGroup --name deploymentName
```

可以通过以下命令获取部署历史记录中的当前计数：

```azurecli-interactive
az group deployment list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

使用[AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment)命令可从历史记录中删除部署。

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

可以通过以下命令获取部署历史记录中的当前计数：

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

