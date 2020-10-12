---
title: vCPU 配额
description: 了解 Azure 的 vCPU 配额。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 028da1fa9d2b16b4d87c345d8435c99b565e1774
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88949688"
---
# <a name="check-vcpu-quotas-using-the-azure-cli"></a>使用 Azure CLI 检查 vCPU 配额

虚拟机和虚拟机规模集的 vCPU 配额已根据每个区域中的每个订阅划分成两层。 第一层是区域的 vCPU 总数，第二层是各种 VM 大小系列核心（如 D 系列 vCPU）。 每当部署新 VM 时，VM 的 vCPU 数不能超过 VM 大小系列的 vCPU 配额或区域 vCPU 配额总数。 如果超过了上述任一配额，将不允许部署 VM。 此外，区域中的虚拟机总数也有一个配额。 有关上述每个配额的详细信息，可以在 [Azure 门户](https://portal.azure.com)的“订阅”页的“使用情况 + 配额”部分中查看，也可以使用 Azure CLI 查询值。

> [!NOTE]
> 配额基于所使用的核心总数（已分配和已解除分配）进行计算。 如果需要额外核心，则[请求增加配额](../../azure-portal/supportability/resource-manager-core-quotas-request.md)或删除不再需要的 VM。 


## <a name="check-usage"></a>检查使用情况

可以使用 [az vm list-usage](/cli/azure/vm) 检查配额用量。

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

输出应类似于：


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>虚拟机预留实例
虚拟机预留实例（其范围限定为单个订阅而不具有 VM 大小灵活性）将为 vCPU 配额添加新的方面。 这些值描述一定能够部署在订阅中的所述大小的实例数。 它们在配额系统中用作占位符，确保预留该配额，以便能够在订阅中部署 Azure 预留。 例如，如果特定订阅包含 10 个 Standard_D1 预留，则 Standard_D1 预留的用量限制将是 10。 这会导致 Azure 确保总区域 vCPU 配额中始终至少有 10 个 vCPU 可用于 Standard_D1 实例，并且标准 D 系列 vCPU 配额中始终至少有 10 个 vCPU 可用于 Standard_D1 实例。

如果需要增加配额以购买单个订阅 RI，则可以在订阅上[请求增加配额](../../azure-portal/supportability/resource-manager-core-quotas-request.md)。

## <a name="next-steps"></a>后续步骤

有关计费和配额的详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=/azure/billing/TOC.json)。
