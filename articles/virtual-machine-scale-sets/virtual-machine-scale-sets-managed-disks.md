---
title: "在 Azure 虚拟机规模集中使用托管磁盘 | Microsoft Docs"
description: "了解在虚拟机规模集中使用托管磁盘的原因和使用方式"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/01/2017
ms.author: negat
ms.openlocfilehash: 338144eb103c68c7fff407cbeccce11734c1c34b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-vm-scale-sets-and-managed-disks"></a>Azure VM 规模集和托管磁盘

Azure [虚拟机规模集](/azure/virtual-machine-scale-sets/)支持具有托管磁盘的虚拟机。 在规模集中使用托管磁盘有以下好处：

* 不再需要预先创建和管理存储帐户以存储规模集 VM 的 OS 磁盘。

* 可以将托管的数据磁盘附加到规模集。

* 使用托管磁盘后，规模集的容量可高达 1,000 个 VM（如果基于平台映像）或者 300 个 VM（如果基于自定义映像）。

## <a name="get-started"></a>入门

使用托管磁盘规模集的一种简单入门方法是在 Azure 门户中部署一个这样的规模集。 有关详细信息，请参阅[此文章](./virtual-machine-scale-sets-portal-create.md)。 另一种简单的入门方法是使用 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) 部署规模集。 以下示例演示如何创建具有 10 个 VM 的基于 Ubuntu 的规模集，其中每个 VM 都有 50 GB 和 100 GB 的数据磁盘：

```azurecli
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

或者，可以在 [Azure 快速入门模板 GitHub 存储库](https://github.com/Azure/azure-quickstart-templates)中查找包含 `vmss` 的文件夹，以查看预先构建的用于部署规模集的模板示例。 若要区分哪些模板已使用托管磁盘，可以参考[此列表](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)。

## <a name="next-steps"></a>后续步骤

有关托管磁盘的一般性详细信息，请参阅[此文](../virtual-machines/windows/managed-disks-overview.md)。

若要了解如何转换 Resource Manager 模板以预配使用托管磁盘的规模集，请参阅[本文](./virtual-machine-scale-sets-convert-template-to-md.md)。 对 Resource Manager 模板的修改也适用于 Azure REST API。

若要了解有关在规模集中使用托管数据磁盘的详细信息，请参阅[本文](./virtual-machine-scale-sets-attached-disks.md)。

若要开始使用大型规模集，请参阅[本文](./virtual-machine-scale-sets-placement-groups.md)。


