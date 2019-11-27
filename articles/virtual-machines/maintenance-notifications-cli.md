---
title: 使用 CLI 获取 Azure Vm 的维护通知
description: 查看 Azure 中运行的虚拟机的维护通知，并使用 Azure CLI 启动自助维护。
services: virtual-machines
author: shants123
tags: azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 3c75adc2bf5974c1bce9f05306342068396ae62b
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535855"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>使用 Azure CLI 处理计划内维护通知

**本文适用于运行 Linux 和 Windows 的虚拟机。**

你可以使用 CLI 来查看何时计划进行[维护](maintenance-notifications.md)的 vm。 可从[az vm get-help 获取](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-get-instance-view)计划内维护信息。
 
仅当有计划内维护时，才会返回维护信息。 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

## <a name="start-maintenance"></a>开始维护

如果 `IsCustomerInitiatedMaintenanceAllowed` 设置为 true，则以下调用将在 VM 上启动维护。

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>经典部署


如果你仍有通过使用经典部署模型部署的旧 VM，则可以使用 Azure 经典 CLI 查询 VM，并启动维护。

通过键入以下内容确保在正确模式下使用经典 VM：

```
azure config mode asm
```

若要获取名为 myVM 的 VM 维护状态，请键入：

```
azure vm show myVM 
``` 

若要在名为 myVM 的经典 VM 的 myService服务和 myDeployment部署中启动维护，请键入：

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>后续步骤

你还可以使用[Azure PowerShell](maintenance-notifications-powershell.md)或[门户](maintenance-notifications-portal.md)处理计划内维护。
