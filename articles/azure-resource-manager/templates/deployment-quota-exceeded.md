---
title: 超出部署配额
description: 介绍如何解决这样一个错误：在资源组历史记录中有 800 多个部署。
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: 919cd9a3482401cd47516e2677b0bf58387488b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80245083"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>解决部署计数超出 800 的错误

每个资源组在其部署历史记录中最多只能有 800 个部署。 本文介绍在部署因超出允许的 800 个部署的限制而失败时可能会出现的错误。 若要解决此错误，请从资源组历史记录中删除部署。 从历史记录中删除部署不会影响已部署的任何资源。

## <a name="symptom"></a>症状

在部署过程中，你收到错误，该错误指出当前部署会超出 800 个部署的配额。

## <a name="solution"></a>解决方案

### <a name="azure-cli"></a>Azure CLI

使用[az deployment group delete](/cli/azure/group/deployment)命令删除历史记录中的部署。

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

若要删除五天以前的所有部署，请使用：

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

可使用以下命令获取部署历史记录中的当前计数：

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

使用 [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) 命令从历史记录中删除部署。

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

若要删除五天以前的所有部署，请使用：

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

可使用以下命令获取部署历史记录中的当前计数：

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>第三方解决方案

以下外部解决方案解决特定场景的问题：

* [Azure 逻辑应用和 PowerShell 解决方案](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [AzDevOps 扩展](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
