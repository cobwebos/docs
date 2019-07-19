---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 9832ae6010f262322328ec22876a35c89e1bedca
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850322"
---
将 Vm 放置在单个区域可减少实例间的物理距离。 如果将它们放在单个可用性区域中, 还会使它们在物理上保持不变。 但是, 随着 Azure 占用空间的增长, 单个可用性区域可能跨多个物理数据中心, 这可能会导致网络延迟影响应用程序。 

若要尽可能降低 Vm, 请尽可能降低延迟, 然后将它们部署到邻近的放置组。

邻近性放置组是一种逻辑分组, 用于确保 Azure 计算资源的物理位置彼此接近。 邻近性放置组适用于要求低延迟的工作负荷。


- 独立 Vm 之间的延迟较低。
- 单个可用性集或虚拟机规模集中的 Vm 之间的低延迟。 
- 独立 Vm、多个可用性集中的虚拟机或多个规模集之间的低延迟。 单个放置组中可以有多个计算资源, 以便将多层应用程序组合在一起。 
- 使用不同硬件类型的多个应用程序层之间的延迟较低。 例如, 在可用性集中使用 M 系列运行后端, 并在规模集中的单个邻近位置组中的 D 系列实例上运行前端。

> [!IMPORTANT]
> 邻近性放置组目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
> 预览期间, 不会在这些区域中使用邻近位置组:**日本东部**、**澳大利亚东部**和**印度中部**。


## <a name="best-practices"></a>最佳实践 
- 对于最低延迟, 请将邻近的放置组与加速网络一起使用。 有关详细信息, 请参阅[创建具有加速网络的 Linux 虚拟机](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)或[创建具有加速网络的 Windows 虚拟机](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
- 在单个 resource manager 模板中部署所有 VM 大小。 为了避免在不支持所有 VM Sku 和大小所需的硬件上登录, 请将所有应用程序层都包含在单个模板中, 以便将所有应用程序层都同时部署。
- 如果你正在使用 PowerShell、CLI 或 SDK 编写你的部署脚本, 可能会收到分配错误`OverconstrainedAllocationRequest`。 在这种情况下, 应停止/解除分配所有现有 Vm, 并将部署脚本中的序列更改为从失败的 VM SKU/大小开始。 
- 重用从中删除 Vm 的现有放置组时, 请等待删除完成, 然后再向其中添加 Vm。
- 如果延迟是第一项优先级, 请将 Vm 放置在一个可用性区域的邻近组和整个解决方案中。 但如果复原是最高的优先级, 请将实例分散到多个可用性区域 (单个邻近位置组不能跨区域)。