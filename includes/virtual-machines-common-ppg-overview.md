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
ms.openlocfilehash: 3215f5952daef053c94432bc8fdef15e1775047a
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171086"
---
将 Vm 放置在单个区域可减少实例间的物理距离。 如果将它们放在单个可用性区域中，还会使它们在物理上保持不变。 但是，随着 Azure 占用空间的增长，单个可用性区域可能跨多个物理数据中心，这可能会导致网络延迟影响应用程序。 

若要尽可能降低 Vm，请尽可能降低延迟，然后将它们部署到邻近的放置组。

邻近性放置组是一种逻辑分组，用于确保 Azure 计算资源的物理位置彼此接近。 邻近性放置组适用于要求低延迟的工作负荷。


- 独立 Vm 之间的延迟较低。
- 单个可用性集或虚拟机规模集中的 Vm 之间的低延迟。 
- 独立 Vm、多个可用性集中的虚拟机或多个规模集之间的低延迟。 单个放置组中可以有多个计算资源，以便将多层应用程序组合在一起。 
- 使用不同硬件类型的多个应用程序层之间的延迟较低。 例如，在可用性集中使用 M 系列运行后端，并在规模集中的单个邻近位置组中的 D 系列实例上运行前端。


![邻近位置组的图形](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>使用邻近位置组 

邻近性放置组是 Azure 中的一种新资源类型。 在将其与其他资源一起使用之前，需要创建一个。 创建后，可以将其与虚拟机、可用性集或虚拟机规模集一起使用。 创建提供邻近感应放置组 ID 的计算资源时，可以指定邻近位置组。 

你还可以将现有资源移到邻近组。 将资源移到邻近组时，应先停止（释放）资产，因为它可能会重新部署到区域中的其他数据中心，因此满足归置约束。 

对于可用性集和虚拟机规模集，应在资源级别（而不是单个虚拟机）设置邻近性放置组。 

邻近性放置组是归置约束，而不是固定机制。 它会固定到特定的数据中心，并部署第一个资源来使用它。 使用邻近位置组的所有资源停止（释放）或删除后，它将不再固定。 因此，在使用具有多个 VM 序列的邻近位置组时，必须尽可能在模板中指定所有所需的类型，或者遵循部署序列（这将提高成功部署的可能性）。 如果你的部署失败，请用 VM 大小重新启动部署，该大小已失败作为第一个要部署的大小。


## <a name="best-practices"></a>最佳做法 
- 对于最低延迟，请将邻近的放置组与加速网络一起使用。 有关详细信息，请参阅[创建具有加速网络的 Linux 虚拟机](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)或[创建具有加速网络的 Windows 虚拟机](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
- 在单个模板中部署所有 VM 大小。 为了避免在不支持所有 VM Sku 和大小所需的硬件上登录，请将所有应用程序层都包含在单个模板中，以便将所有应用程序层都同时部署。
- 如果你正在使用 PowerShell、CLI 或 SDK 编写你的部署脚本，则 `OverconstrainedAllocationRequest`可能会收到分配错误。 在这种情况下，应停止/解除分配所有现有 Vm，并将部署脚本中的序列更改为从失败的 VM SKU/大小开始。 
- 重用从中删除 Vm 的现有放置组时，请等待删除完成，然后再向其中添加 Vm。
- 如果延迟是第一项优先级，请将 Vm 放置在一个可用性区域的邻近组和整个解决方案中。 但如果复原是最高的优先级，请将实例分散到多个可用性区域（单个邻近位置组不能跨区域）。