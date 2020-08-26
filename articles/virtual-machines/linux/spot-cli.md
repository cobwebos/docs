---
title: 使用 CLI 部署 Azure 点 Vm
description: 了解如何使用 CLI 部署 Azure 点 Vm 以节省成本。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 165a2c6221c8d3c14f71134deef962d0859bb438
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816705"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>使用 Azure CLI 部署专色 Vm

使用 [Azure 点 vm](spot-vms.md) ，你可以显著节省成本。 当 Azure 需要恢复容量时，Azure 基础结构将逐出点 Vm。 因此，专色 Vm 非常适合用于处理中断的工作负荷，如批处理作业、开发/测试环境、大型计算工作负荷等。

基于区域和 SKU，污点 Vm 的定价是可变的。 有关详细信息，请参阅适用于 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 和 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定价。 

你可以选择为 VM 设置你愿意支付的最大价格（每小时）。 可使用最多5个小数位 (USD) ，为专色 VM 设置最大价格。 例如，值 `0.98765` 表示最高价格为 0.98765 美元/小时。 如果将最大价格设置为 `-1` ，则不会根据价格收回 VM。 VM 的价格将是当前的价格价格或标准 VM 的价格，只要容量和配额可用，此价格就越小。 有关设置最大价格的详细信息，请参阅 [污点 vm-定价](../spot-vms.md#pricing)。

使用 Azure CLI 创建具有点的 VM 的过程与 [快速入门文章](./quick-create-cli.md)中详述的过程相同。 只需添加 "--priority 污点" 参数，将设置 `--eviction-policy` 为 "释放 (这是默认) 或 `Delete` ，并提供最大价格或 `-1` 。 


## <a name="install-azure-cli"></a>安装 Azure CLI

若要创建专色 Vm，需要运行 Azure CLI 版本2.0.74 或更高版本。 若要查找版本，请运行 **az --version**。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

使用 [az login](/cli/azure/reference-index#az-login) 登录到 Azure。

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>创建一个专色 VM

此示例演示如何部署将不会根据价格收回的 Linux 点 VM。 逐出策略设置为解除分配 VM，以便以后可以重新启动它。 如果要在虚拟机被逐出时删除 VM 和基础磁盘，请将设置 `--eviction-policy` 为 `Delete` 。

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



创建 VM 后，你可以查询以查看资源组中所有 Vm 的最大计费价格。

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**后续步骤**

你还可以使用 [Azure PowerShell](../windows/spot-powershell.md)、 [门户](../windows/spot-portal.md)或 [模板](spot-template.md)创建一个专色 VM。

如果遇到错误，请参阅 [错误代码](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
