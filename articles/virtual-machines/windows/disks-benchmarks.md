---
title: 在 Azure 磁盘存储上对应用程序进行基准测试
description: 了解在 Azure 上对应用程序进行基准测试的过程。
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: d8bf90f4ecea992b3212200905cd9204e4079b1b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88683957"
---
# <a name="benchmarking-a-disk"></a>磁盘基准测试

基准测试是指模拟应用程序的不同工作负荷，针对每个工作负荷来测量应用程序性能这样一个过程。 通过[为实现高性能而设计](../premium-storage-performance.md)一文中描述的步骤，你已经收集了应用程序性能要求。 通过在托管应用程序的 VM 上运行基准测试工具，可以确定应用程序在高级存储中能够达到的性能级别。 在本文中，我们提供了如何对预配了 Azure 高级存储磁盘的标准 DS14 VM 进行基准测试的示例。

我们使用了常见的基准测试工具 Iometer 和 FIO，分别适用于 Windows 和 Linux。 这些工具会生成多个线程，这些线程模拟类似生产的工作负荷，并测量系统性能。 使用这些工具还可以配置各种参数（例如块大小和队列深度），应用程序的这些参数通常无法更改。 这样便可以更灵活地在针对不同类型的应用程序工作负荷预配了高级磁盘的高规格 VM 上实现最大性能。 若要详细了解每种基准测试工具，请参阅 [Iometer](http://www.iometer.org/) 和 [FIO](http://freecode.com/projects/fio)。

要按以下示例进行操作，请创建一个标准 DS14 VM，然后将 11 个高级存储磁盘连接到 VM。 在这 11 个磁盘中，将 10 个磁盘的主机缓存配置为“无”，并将它们条带化到名为 NoCacheWrites 的卷中。 将剩余磁盘上的主机缓存配置为“ReadOnly”，在该磁盘上创建名为 CacheReads 的卷。 进行这样的设置以后，便可以看到标准 DS14 VM 展现出最大的读写性能。 有关创建具有高级 SSD 的 DS14 VM 的详细步骤，请转至[为实现高性能而设计](../premium-storage-performance.md)。

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>后续步骤

继续阅读有关[针对高性能进行设计](../premium-storage-performance.md)的文章。

在该文中，你将为原型创建一个类似于现有应用程序的清单。 使用各种能够用来模拟工作负荷并衡量原型应用程序性能的基准测试工具。 这样做可以确定哪些磁盘产品可以满足或超过你的应用程序性能要求。 然后，就可以将相同的准则实施到生产型应用程序中。