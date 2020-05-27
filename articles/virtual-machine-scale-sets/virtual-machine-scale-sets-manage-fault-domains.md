---
title: 管理 Azure 虚拟机规模集中的容错域
description: 了解如何在创建虚拟机规模集时选择适当数量的 FD。
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 12/18/2018
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 1f5b253dbc2161892bcc2aee07c8cd3788fa5fb5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83200034"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>为虚拟机规模集选择适当数量的容错域
默认情况下，系统会在没有区域的 Azure 区域中创建具有五个容错域的虚拟机规模集。 对于支持虚拟机规模集分区部署等等区域，如果已选中此选项，则每个区域的容错域计数的默认值为 1。 在这种情况下，FD = 1 意味着尽最大努力将属于规模集的 VM 实例分布在多个机架上。

你还可以考虑使规模集容错域数量与托管磁盘容错域数量对齐。 如果整个托管磁盘容错域发生故障，此对齐有助于防止仲裁。 FD 计数可以设置为小于或等于每个区域中可用托管磁盘容错域的数量。 请参阅此[文档](../virtual-machines/windows/manage-availability.md)，了解按区域划分的托管磁盘容错域数。

## <a name="rest-api"></a>REST API
可以将 `properties.platformFaultDomainCount` 属性设置为 1、2 或 3（如果未指定，则默认值为 5）。 请参阅[此处](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate)的 REST API 文档。

## <a name="azure-cli"></a>Azure CLI
可以将参数 `--platform-fault-domain-count` 设置为 1、2 或 3（如果未指定，则默认值为 5）。 请参阅[此处](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)的 Azure CLI 文档。

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

创建和配置所有的规模集资源和 VM 需要几分钟时间。

## <a name="next-steps"></a>后续步骤
- 详细了解 Azure 环境的[可用性和冗余功能](../virtual-machines/windows/availability.md)
