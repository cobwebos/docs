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
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: f9cc157f25e70a8154614b659fb7e59b7fd06ed1
ms.contentlocale: zh-cn
ms.lasthandoff: 06/02/2017


---
# <a name="azure-vm-scale-sets-and-managed-disks"></a>Azure VM 规模集和托管磁盘

Azure [虚拟机规模集](/azure/virtual-machine-scale-sets/)现支持具有托管磁盘的虚拟机。 在规模集中使用托管磁盘有以下好处：

* 不再需要预先创建和管理存储帐户以存储规模集 VM 的 OS 磁盘。

* 可以将托管的数据磁盘附加到规模集。

* 使用托管磁盘后，规模集的容量可高达 1,000 个 VM（如果基于平台映像）或者 100 个 VM（如果基于自定义映像）。

## <a name="get-started"></a>入门

使用托管磁盘规模集的一种简单入门方法是在 Azure 门户中部署一个这样的规模集。 有关详细信息，请参阅[此文章](./virtual-machine-scale-sets-portal-create.md)。 另一种简单的入门方法是使用 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) 部署规模集。 以下示例演示如何创建具有 10 个 VM 的基于 Ubuntu 的规模集，其中每个 VM 都有 50 GB 和 100 GB 的数据磁盘：

```azurecli
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

或者，可以在 [Azure 快速入门模板 GitHub 存储库](https://github.com/Azure/azure-quickstart-templates)中查找包含 `vmss` 的文件夹，以查看预先构建的用于部署规模集的模板示例。 若要区分哪些模板已使用托管磁盘，可以参考[此列表](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)。

## <a name="api-versions"></a>API 版本

带有托管磁盘的规模集需要 `2016-04-30-preview` 或更高版本的 Microsoft.Compute APi。 即使在支持托管磁盘的新 API 版本中，使用非托管磁盘的规模集也可以与当前一样继续工作。 但是，使用非托管磁盘的规模集无法获得托管磁盘的优点，即使在这些新的 API 版本中也是如此。

## <a name="next-steps"></a>后续步骤

若要查找一般情况下有关托管磁盘的详细信息，请参阅[本文](../storage/storage-managed-disks-overview.md)。

若要了解如何转换 Resource Manager 模板以预配使用托管磁盘的规模集，请参阅[本文](./virtual-machine-scale-sets-convert-template-to-md.md)。 对 Resource Manager 模板的修改也适用于 Azure REST API。

若要了解有关在规模集中使用托管数据磁盘的详细信息，请参阅[本文](./virtual-machine-scale-sets-attached-disks.md)。

若要开始使用大型规模集，请参阅[本文](./virtual-machine-scale-sets-placement-groups.md)。



