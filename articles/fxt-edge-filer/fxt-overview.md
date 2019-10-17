---
title: Microsoft Azure FXT Edge Filer 概述
description: 介绍 Azure FXT Edge Filer 混合存储缓存（一个用于实现高性能计算的活动存档和文件访问加速器解决方案）
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: acf35015ff7851f7ea2a2527852be2573512e35d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254840"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Azure FXT Edge Filer 混合存储缓存是什么？

Azure FXT Edge Filer 是一种混合存储缓存设备，可提供快速文件访问和活动存档，以执行高性能计算 (HPC) 任务。

它可以使用多个数据源，无论这些数据源是在本地数据中心存储、远程存储还是在云中存储。 Azure FXT Edge Filer 可在不同的存储系统中为数据提供统一的命名空间。

三个或更多 FXT Edge Filer 硬件设备可作为群集文件系统协同工作以提供缓存。 有关购买所需的硬件的详细信息，请与 Microsoft 代表联系。 

若要了解详细信息，请阅读 [Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/) 上的产品信息和数据表。

## <a name="use-cases"></a>用例

Azure FXT Edge Filer 最适合提高如下工作流的生产效率：

* 读取操作密集型文件访问工作流 
* NFSv3 或 SMB2 协议
* 包含 1000 到 100,000 个 CPU 核心的计算场

### <a name="nas-optimization-and-scaling"></a>NAS 的优化和缩放

可以使用 Azure FXT Edge Filer 缓存顺畅地访问现有的 NetApp 和 Dell EMC Isilon NAS 系统。 还可以添加 Azure Blob 或其他云存储来提供可伸缩性，而无需在客户端修改数据访问进程。 

### <a name="wan-caching"></a>WAN 缓存

Azure FXT Edge Filer 可用于支持超级用户快速访问文件，即使这些用户所需的数据存储在别处也是如此。 提供访问权限的同时，将备份和其他数据管理系统保留在集中式的数据中心内。 

### <a name="active-archive-in-azure-blob"></a>Azure Blob 中的活动存档

使用 Azure FXT Edge Filer 作为接入点，将数据中心扩展到云存储中。 

## <a name="features"></a>功能 

提供两种硬件型号。 

| 模型 | DRAM | NVMe SSD | 网络端口 | 
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25.6 TB | 6 x 25Gb/10Gb + 2 x 1Gb |
| FXT 6400 | 768 GB | 12.8 TB | 6 x 25Gb/10Gb + 2 x 1Gb |


## <a name="next-steps"></a>后续步骤

* 请阅读[规格](fxt-specs.md)或[安装教程](fxt-install.md)以继续了解 Azure FXT Edge Filer。
* 在 [Azure FXT Edge Filer 产品页](https://azure.microsoft.com/services/fxt-edge-filer/)上了解如何购买 Azure FXT Edge Filer。