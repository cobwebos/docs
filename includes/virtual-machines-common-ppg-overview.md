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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421835"
---
将 VM 置于单个区域可以减少实例之间的物理距离。 将它们置于单一可用性区域中也会减少它们的物理距离。 但是，随着 Azure 占用空间的增加，单个可用性区域可能会跨多个物理数据中心，这可能导致网络延迟，对应用程序造成影响。 

若要让 VM 尽可能靠近，将延迟尽可能降至最低，应将 VM 部署到一个邻近放置组中。

邻近放置组是一种逻辑分组，用于确保 Azure 计算资源在物理上彼此靠近。 邻近放置组用于要求低延迟的工作负荷。


- 独立 VM 之间的低延迟。
- 单个可用性集或虚拟机规模集中的 VM 之间的低延迟。 
- 独立 VM、多个可用性集或多个规模集中的 VM 之间的低延迟。 单个放置组中可以有多个计算资源，形成一个多层应用程序。 
- 多个使用不同硬件类型的应用程序之间的低延迟。 例如，运行在可用性集中使用 M 系列的后端，以及在单个邻近放置组的规模集的 D 系列实例上的前端。


![接近放置组的图形](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>使用邻近放置组 

邻近放置组是 Azure 中的新资源类型。 您需要先创建一个，然后再将其与其他资源一起使用。 创建后，它可以与虚拟机、可用性集或虚拟机扩展集一起使用。 在创建提供邻近放置组 ID 的计算资源时，可以指定邻近放置组。 

您还可以将现有资源移动到邻近放置组中。 将资源移动到邻近放置组时，应首先停止（取消分配）资产，因为它可能会重新部署到区域中的不同数据中心，从而满足主机代管约束。 

对于可用性集和虚拟机扩展集，应在资源级别而不是单个虚拟机设置接近放置组。 

接近放置组是主机代管约束，而不是固定机制。 使用部署使用它的第一个资源，它将固定到特定的数据中心。 停止（处理位置）或删除使用邻近放置组的所有资源后，将不再固定它。 因此，在使用具有多个 VM 系列的邻近放置组时，请务必尽可能在模板中预先指定所有必需的类型，或者遵循部署顺序，从而提高成功部署的机会。 如果部署失败，请重新启动具有 VM 大小的部署，该大小已作为要部署的第一个大小失败。

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>使用邻近放置组时，应期待什么 
邻近放置组在同一数据中心提供同一位置。 但是，由于邻近放置组表示额外的部署约束，因此可能会出现分配失败。 在使用接近放置组时，您可能会看到分配失败的情况很少：

- 当您请求接近放置组中的第一个虚拟机时，将自动选择数据中心。 在某些情况下，如果另一个虚拟机 SKU 不存在，则对另一个虚拟机 SKU 的第二次请求可能会失败。 在这种情况下，将返回 **"过度约束分配请求"** 错误。 为了避免这种情况，请尝试更改部署 SKU 或使用单个 ARM 模板部署两个资源的顺序。
-   在弹性工作负载（添加和删除 VM 实例）的情况下，对部署具有接近放置组约束可能会导致无法满足请求，从而导致**分配失败错误**。 
- 根据需要停止（去分配）和启动 VM 是实现弹性的另一种方法。 由于停止（取消分配）VM 后不会保留容量，因此再次启动该容量可能会导致**分配失败**错误。


## <a name="best-practices"></a>最佳做法 
- 若要实现最低的延迟，请将邻近放置组与加速网络一起使用。 有关详细信息，请参阅[创建具有加速网络的 Linux 虚拟机](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)或[创建具有加速网络的 Windows 虚拟机](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
- 在单个模板中部署所有 VM 大小。 为了避免使用不支持所需的全部 VM SKU 和大小的硬件，请将所有应用层包括在单个模板中，以便同时部署它们。
- 如果使用 PowerShell、CLI 或 SDK 来编写部署脚本，则可能会出现分配错误 `OverconstrainedAllocationRequest`。 在这种情况下，应停止/解除分配所有现有的 VM，并更改部署脚本中的顺序，从失败的 VM SKU/大小开始。 
- 重复使用现有的放置组时，如果系统正在删除其中的 VM，请等待删除操作全部完成再向该组添加 VM。
- 如果延迟是首要考虑的事项，请将 VM 置于邻近放置组中，将整个解决方案置于一个可用性区域中。 但是，如果复原能力是首要考虑的事项，请将实例分散到多个可用性区域（单个邻近放置组不能跨多个区域）。
