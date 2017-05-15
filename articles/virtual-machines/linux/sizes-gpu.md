---
title: "Azure Linux VM 大小 - GPU | Microsoft Docs"
description: "列出 Azure 中适用于 Linux 虚拟机的各种 GPU 优化大小。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: e155891ff8dc736e2f7de1b95f07ff7b2d5d4e1b
ms.openlocfilehash: e0cd2a14b4102797024925a72783ecaf56919aec
ms.contentlocale: zh-cn
ms.lasthandoff: 05/02/2017


---

# <a name="gpu-linux-vm-sizes"></a>GPU Linux VM 大小

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

## <a name="supported-operating-systems"></a>支持的操作系统

有关支持的操作系统和驱动程序要求，请参阅[适用于 Linux 的 N 系列驱动程序设置](n-series-driver-setup.md)。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* 我们不建议在 Ubuntu NC VM 上安装 X server 或使用 nouveau 驱动程序的其他系统。 在安装 NVIDIA GPU 驱动程序之前，需要禁用 nouveau 驱动程序。  

## <a name="other-sizes"></a>其他大小
- [常规用途](sizes-general.md)
- [计算优化](sizes-compute.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [高性能计算](sizes-hpc.md)

## <a name="next-steps"></a>后续步骤
了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助你跨 Azure SKU 比较计算性能的详细信息。
