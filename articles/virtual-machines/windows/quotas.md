---
title: Azure 的 vCPU 配额
description: 了解 Azure 虚拟机的 vCPU 配额。
author: cynthn
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: b00d84e1c2dc8f104046fe24360d79e6952dfacc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88950283"
---
# <a name="check-vcpu-quotas-using-azure-powershell"></a>使用 Azure PowerShell 检查 vCPU 配额

虚拟机和虚拟机规模集的 vCPU 配额已根据每个区域中的每个订阅划分成两层。 第一层是区域的 vCPU 总数，第二层是各种 VM 大小系列核心（如 D 系列 vCPU）。 每当部署新 VM 时，VM 的 vCPU 数不能超过 VM 大小系列的 vCPU 配额或区域 vCPU 配额总数。 如果超过了上述任一配额，将不允许部署 VM。 此外，区域中的虚拟机总数也有一个配额。 可以在 [Azure 门户](https://portal.azure.com)的“订阅”页的“用量 + 配额”部分中查看其中每项配额的详细信息，或者，可以使用 PowerShell 查询这些值。 

> [!NOTE]
> 配额基于所使用的核心总数（已分配和已解除分配）进行计算。 如果需要额外核心，则[请求增加配额](../../azure-portal/supportability/resource-manager-core-quotas-request.md)或删除不再需要的 VM。 
 
## <a name="check-usage"></a>检查使用情况

可以使用 [Get-AzVMUsage](/powershell/module/az.compute/get-azvmusage) cmdlet 来检查配额的使用情况。

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

输出与此类似：

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count
```


## <a name="reserved-vm-instances"></a>虚拟机预留实例
虚拟机预留实例（其范围限定为单个订阅而不具有 VM 大小灵活性）将为 vCPU 配额添加新的方面。 这些值描述一定能够部署在订阅中的所述大小的实例数。 它们在配额系统中用作占位符，确保预留该配额，以便能够在订阅中部署虚拟机预留实例。 例如，如果特定订阅包含 10 个 Standard_D1 虚拟机预留实例，则 Standard_D1 虚拟机预留实例的用量限制将是 10。 这会导致 Azure 确保总区域 vCPU 配额中始终至少有 10 个 vCPU 可用于 Standard_D1 实例，并且标准 D 系列 vCPU 配额中始终至少有 10 个 vCPU 可用于 Standard_D1 实例。

如果需要增加配额来购买单个订阅 RI，则可以在订阅上[请求增加配额](../../azure-portal/supportability/resource-manager-core-quotas-request.md)。

## <a name="next-steps"></a>后续步骤

有关计费和配额的详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=/azure/billing/TOC.json)。
