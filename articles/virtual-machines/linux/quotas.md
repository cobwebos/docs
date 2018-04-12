---
title: Azure 的 vCPU 配额 | Microsoft Docs
description: 了解 Azure 的 vCPU 配额。
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: Drewm3
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: a4e0bbe1c6d9b121dfb422934cdd67ff19f80482
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="virtual-machine-vcpu-quotas"></a>虚拟机 vCPU 配额

对于每个区域中的每个订阅，虚拟机和虚拟机规模集的 vCPU 配额被安排在两个层中。 第一层是区域的 vCPU 总数，第二层是各种 VM 大小系列核心（如标准 D 系列 vCPU）。 每当部署新 VM 时，新部署的 VM 的 vCPU 数不能超过特定 VM 大小系列的 vCPU 配额或区域 vCPU 配额总数。 如果超过了上述任一配额，将不允许部署 VM。 此外，区域中还有虚拟机总数的配额。 有关上述每个配额的详细信息，可以在 [Azure 门户](https://portal.azure.com)的“订阅”页的“使用情况 + 配额”部分中查看，也可以使用 Azure CLI 查询值。


## <a name="check-usage"></a>查看使用情况

可以使用 [az vm list-usage](/cli/azure/vm#az_vm_list_usage) 查看配额使用情况。

```azurecli-interactive
az vm list-usage --location "East US"
[
  …
  {
    "currentValue": 4,
    "limit": 260,
    "name": {
      "localizedValue": "Total Regional vCPUs",
      "value": "cores"
    }
  },
  {
    "currentValue": 4,
    "limit": 10000,
    "name": {
      "localizedValue": "Virtual Machines",
      "value": "virtualMachines"
    }
  },
  {
    "currentValue": 1,
    "limit": 2000,
    "name": {
      "localizedValue": "Virtual Machine Scale Sets",
      "value": "virtualMachineScaleSets"
    }
  },
  {
    "currentValue": 1,
    "limit": 10,
    "name": {
      "localizedValue": "Standard B Family vCPUs",
      "value": "standardBFamily"
    }
  },
```
## <a name="reserved-vm-instances"></a>保留 VM 实例
保留 VM 实例（其范围限定为单个订阅）将向 vCPU 配额添加一个新方面。 这些值描述一定能够部署在订阅中的所述大小的实例数。 它们在配额系统中作为占位符使用以确保保留配额，从而确保保留实例可在订阅中部署。 例如，如果特定订阅有 10 个 Standard_D1 保留实例，则 Standard_D1 保留实例的使用量限制将为 10。 这会导致 Azure 确保总区域 vCPU 配额中始终至少有 10 个 vCPU 可用于 Standard_D1 实例，并且标准 D 系列 vCPU 配额中始终至少有 10 个 vCPU 可用于 Standard_D1 实例。

如果需要增加配额以购买单个订阅 RI，则可以在订阅上[请求增加配额](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。

## <a name="next-steps"></a>后续步骤

有关计费和配额的详细信息，请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json)。
