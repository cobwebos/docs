---
title: 使用 CLI 部署 Azure Spot VM（预览）
description: 了解如何使用 CLI 部署 Azure Spot VM 以节省成本。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 110e935671ab1d640b2ff3dc26c203b262e999fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77163086"
---
# <a name="preview-deploy-spot-vms-using-the-azure-cli"></a>预览：使用 Azure CLI 部署 Spot VM

使用[Azure Spot VM](spot-vms.md)使您能够利用我们未使用的容量，从而显著节省成本。 在 Azure 需要返回容量的任何时间点，Azure 基础结构将驱逐 Spot VM。 因此，Spot VM 非常适合处理批处理作业、开发/测试环境、大型计算工作负载等中断的工作负载。

现货 VM 的定价基于区域和 SKU 是可变的。 有关详细信息，请参阅[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)的 VM 定价。 

您可以选择为 VM 设置您愿意每小时支付的最高价格。 Spot VM 的最高价格可以用美元 （USD） 设置，最多使用 5 个小数位。 例如，该值`0.98765`将是每小时 0.98765 美元的最高价格。 如果将最高价格设置为`-1`，则 VM 不会根据价格被逐出。 VM 的价格将是 Spot 的当前价格或标准 VM 的价格，只要容量和配额可用，标准 VM 的价格就更少了。 有关设置最高价格的详细信息，请参阅[现货 VM - 定价](spot-vms.md#pricing)。

使用 Azure CLI 使用 Spot 创建 VM 的过程与[快速入门文章中](/azure/virtual-machines/linux/quick-create-cli)详述的过程相同。 只需添加"-优先级点"参数并提供最高价格或`-1`。

> [!IMPORTANT]
> 竞价实例当前处于公共预览版中。
> 不建议生产工作负载使用此预览版本。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>



## <a name="install-azure-cli"></a>安装 Azure CLI

要创建 Spot VM，需要运行 Azure CLI 版本 2.0.74 或更高版本。 若要查找版本，请运行 **az --version**。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

使用 [az login](/cli/azure/reference-index#az-login) 登录到 Azure。

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>创建 Spot VM

此示例演示如何部署不会根据价格逐出的 Linux Spot VM。 

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1
```

创建 VM 后，可以查询以查看资源组中所有 VM 的最大计费价格。

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**后续步骤**

您还可以使用[Azure PowerShell](../windows/spot-powershell.md)或[模板](spot-template.md)创建 Spot VM。

如果遇到错误，请参阅[错误代码](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
