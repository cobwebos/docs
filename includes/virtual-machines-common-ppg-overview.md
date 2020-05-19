---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: fb2eb2d237a1245627bbdb6f4f2eacbb9966a2c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81421835"
---
将 VM 置于单个区域可以减少实例之间的物理距离。 将它们置于单一可用性区域中也会减少它们的物理距离。 但是，随着 Azure 占用空间的增加，单个可用性区域可能会跨多个物理数据中心，这可能导致网络延迟，对应用程序造成影响。 

若要让 VM 尽可能靠近，将延迟尽可能降至最低，应将 VM 部署到一个邻近放置组中。

邻近放置组是一种逻辑分组，用于确保 Azure 计算资源在物理上彼此靠近。 邻近放置组用于要求低延迟的工作负荷。


- 独立 VM 之间的低延迟。
- 单个可用性集或虚拟机规模集中的 VM 之间的低延迟。 
- 独立 VM、多个可用性集或多个规模集中的 VM 之间的低延迟。 单个放置组中可以有多个计算资源，形成一个多层应用程序。 
- 多个使用不同硬件类型的应用程序之间的低延迟。 例如，运行在可用性集中使用 M 系列的后端，以及在单个邻近放置组的规模集的 D 系列实例上的前端。


![邻近放置组的插图](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>使用邻近放置组 

邻近放置组是 Azure 中的一种新资源类型。 在将邻近放置组用于其他资源之前，需要先创建邻近放置组。 创建后，可将其用于虚拟机、可用性集或虚拟机规模集。 创建计算资源时，可以通过提供邻近放置组 ID 来指定邻近放置组。 

还可将现有资源移入邻近放置组。 将资源移入邻近放置组时，应该先停止（解除分配）资产，因为可能需要将资产重新部署到区域中的另一数据中心以满足共置约束。 

对于可用性集和虚拟机规模集，应在资源级别而不是在单个虚拟机上设置邻近放置组。 

邻近放置组是一种共置约束，而不是一种固定机制。 它会与要使用它的第一个资源的部署一起固定到特定的数据中心。 停止（解除分配）或删除使用邻近放置组的所有资源后，邻近放置组将不再固定。 因此，在将一个邻近放置组用于多个 VM 系列时，必须尽可能地提前在模板中指定全部所需的类型，或者按照可以提高成功部署几率的部署顺序来进行。 如果部署失败，请使用已失败的第一个待部署 VM 大小重新开始部署。

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>使用邻近放置组时预期会发生的情况 
邻近放置组提供在同一数据中心内的共置。 但是，因为邻近放置组提供一个附加的部署约束，因此可能会发生分配失败。 在少数用例中，使用邻近放置组时你可能会遇到分配失败：

- 当你请求邻近放置组中的第一个虚拟机时，会自动选择数据中心。 在某些情况下，当请求另一个虚拟机 SKU 时，如果它在该数据中心内不存在，则该请求可能会失败。 在这种情况下，将返回 OverconstrainedAllocationRequest 错误  。 为了避免这种情况，请尝试更改 SKU 的部署顺序，或者使用单个 ARM 模板部署这两个资源。
-   对于你在其中添加和删除 VM 实例的弹性工作负荷，在部署上施加邻近放置组约束可能会导致去满足请求时失败，进而导致 AllocationFailure 错误  。 
- 根据需要停止（解除分配）再启动 VM 是实现弹性的另一种方式。 由于停止（解除分配）VM 后不会保留容量，因此再次启动该 VM 可能会导致 AllocationFailure 错误  。


## <a name="best-practices"></a>最佳做法 
- 若要实现最低的延迟，请将邻近放置组与加速网络一起使用。 有关详细信息，请参阅[创建具有加速网络的 Linux 虚拟机](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)或[创建具有加速网络的 Windows 虚拟机](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
- 在单个模板中部署所有 VM 大小。 为了避免使用不支持所需的全部 VM SKU 和大小的硬件，请将所有应用层包括在单个模板中，以便同时部署它们。
- 如果使用 PowerShell、CLI 或 SDK 来编写部署脚本，则可能会出现分配错误 `OverconstrainedAllocationRequest`。 在这种情况下，应停止/解除分配所有现有的 VM，并更改部署脚本中的顺序，从失败的 VM SKU/大小开始。 
- 重复使用现有的放置组时，如果系统正在删除其中的 VM，请等待删除操作全部完成再向该组添加 VM。
- 如果延迟是首要考虑的事项，请将 VM 置于邻近放置组中，将整个解决方案置于一个可用性区域中。 但是，如果复原能力是首要考虑的事项，请将实例分散到多个可用性区域（单个邻近放置组不能跨多个区域）。
