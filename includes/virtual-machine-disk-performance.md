---
title: include 文件
description: include 文件
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 66380e0bab0a83d086ebebb5e595908cecd11643
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663733"
---
本文介绍如何在将 Azure 虚拟机和 Azure 磁盘结合使用时，澄清磁盘性能和其工作原理。 还介绍了如何诊断磁盘 IO 的瓶颈，以及可以进行哪些更改以优化性能。

## <a name="how-does-disk-performance-work"></a>磁盘性能如何工作？
Azure 虚拟机根据虚拟机类型和大小获得 IOPS 和吞吐量性能限制。 可以附加到虚拟机的 OS 磁盘和数据磁盘具有自己的 IOPs 和吞吐量限制。 当在虚拟机上运行的应用程序请求的 IOPS 或吞吐量比为虚拟机或附加磁盘分配的更多 IOPS 或吞吐量时，应用程序的性能会受到限制。 发生这种情况时，应用程序将体验到性能不佳的情况，并可能导致某些负面后果，如延迟增加。 让我们通过几个示例来巩固这一点。 要使这些示例易于理解，只需查看 IOPs，但同样的逻辑也适用于吞吐量。

## <a name="disk-io-capping"></a>磁盘 IO 上限
设置：
- Standard_D8s_v3 
    - 未缓存 IOPS：12800
- E30 OS 磁盘
    - IOPS：500 
- 2 E30 数据磁盘
    - IOPS：500

![磁盘级别上限](media/vm-disk-performance/disk-level-throttling.jpg)

虚拟机上运行的应用程序向虚拟机发出要求 10000 IOPs 的请求。 VM 允许所有这些，因为 Standard_D8s_v3 虚拟机最多可执行 12800 IOPs。 然后，这些 10000 IOPs 请求会划分为三个不同磁盘的三个不同请求。 1000 IOPs 请求操作系统磁盘，4500 IOPs 请求每个数据磁盘。 由于附加的所有磁盘都是 E30 磁盘，只能处理 500 IOPs，因此它们每个都有 500 IOPs。 然后，附加的磁盘将应用程序的性能上限，并且只能处理 1500 IOPs。 如果使用了更好的磁盘（如高级 SSD P30 磁盘），则它可能在 10000 IOPS 的情况下正常工作。

## <a name="virtual-machine-io-capping"></a>虚拟机 IO 上限
设置：
- Standard_D8s_v3 
    - 未缓存 IOPS：12800
- P30 OS 磁盘
    - IOPS：5000 
- 2 P30 数据磁盘 
    - IOPS：5000

![虚拟机级别上限](media/vm-disk-performance/vm-level-throttling.jpg)

虚拟机上运行的应用程序发出请求，要求 15000 IOPs。 遗憾的是，仅将 Standard_D8s_v3 虚拟机设置为处理 12800 IOPs。 从此，应用程序将受到虚拟机限制的限制，然后必须分配分配的 12800 IOPs。 然后，请求的这些 12800 IOPs 将分成三个不同磁盘的三个不同请求。 4267 IOPs 请求操作系统磁盘，4266 IOPs 请求每个数据磁盘。 由于附加的所有磁盘都是 P30 磁盘，这些磁盘可以处理 5000 IOPs，它们会回复其请求的数量。