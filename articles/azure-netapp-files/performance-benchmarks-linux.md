---
title: 适用于 Linux 的 Azure NetApp 文件性能基准 |Microsoft Docs
description: 介绍 Azure NetApp 文件为 Linux 提供的性能基准。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: b-juche
ms.openlocfilehash: b763a734866dd5fed5bf0500d4d52b9324c92a79
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614585"
---
# <a name="azure-netapp-files-performance-benchmarks-for-linux"></a>适用于 Linux 的 Azure NetApp 文件性能基准

本文介绍了适用于 Linux 的 Azure NetApp 文件的性能基准。

## <a name="linux-scale-out"></a>Linux 横向扩展

本部分介绍 Linux 工作负荷吞吐量和工作负荷 IOPS 的性能基准。

### <a name="linux-workload-throughput"></a>Linux 工作负荷吞吐量  

下图表示 64-kibibyte （KiB）顺序工作负荷和 1 TiB 工作集。 它显示单个 Azure NetApp 文件卷可在 ~ 1600 MiB/s 纯顺序写入和 ~ 4500 MiB/s 纯顺序读取之间进行处理。  

此图说明了一次从纯读取到纯写，每次减少10%。 它演示了使用不同的读/写比率（100%：0%、90%：10%、80%：20%，等等）时可以预期的情况。

![Linux 工作负荷吞吐量](../media/azure-netapp-files/performance-benchmarks-linux-workload-throughput.png)  

### <a name="linux-workload-iops"></a>Linux 工作负荷 IOPS  

下图表示 kibibyte （KiB）随机工作负荷和 1 TiB 工作集。 此图显示 Azure NetApp 文件卷可在 ~ 130000 纯随机写入和 ~ 460000 纯随机读取之间进行处理。  

此图说明了一次从纯读取到纯写，每次减少10%。 它演示了使用不同的读/写比率（100%：0%、90%：10%、80%：20%，等等）时可以预期的情况。

![Linux 工作负荷 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-workload-iops.png)  

## <a name="linux-scale-up"></a>Linux 向上扩展  

Linux 5.3 内核启用了单一客户端横向扩展网络（NFS）`nconnect`。 此部分中的关系图显示了 NFSv3 的客户端装载选项的验证测试结果。 此功能在 SUSE 上提供（从 SLES12SP4 开始）和 Ubuntu （从19.10 版开始）。 它在概念上类似于 SMB 多通道和 Oracle 直接 NFS。

此关系图将的优点`nconnect`与非连接的已装入卷进行比较。 在图形中，FIO 从 west2 Azure 区域中的单个 D32s_v3 实例生成了工作负荷。

### <a name="linux-read-throughput"></a>Linux 读取吞吐量  

以下关系图显示了顺序读取 ~ 3500 MiB/s，大约`nconnect`为 2.3 x 非`nconnect`。

![Linux 读取吞吐量](../media/azure-netapp-files/performance-benchmarks-linux-read-throughput.png)  

### <a name="linux-write-throughput"></a>Linux 写入吞吐量  

下图显示了顺序写入。 它们表明`nconnect`对于顺序写入并没有明显的好处。 1500 MiB/s 大致大致为序列写入量的上限和 D32s_v3 实例出口限制。

![Linux 写入吞吐量](../media/azure-netapp-files/performance-benchmarks-linux-write-throughput.png)  

### <a name="linux-read-iops"></a>Linux 读取 IOPS  

以下关系图显示了200000的读取 IOPS 的随机读取`nconnect`，大约为3倍`nconnect`非。

![Linux 读取 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-read-iops.png)  

### <a name="linux-write-iops"></a>Linux 写入 IOPS  

以下关系图显示了大约135000写入 IOPS 的随机写入`nconnect`，其大致为 3`nconnect`倍。

![Linux 写入 IOPS](../media/azure-netapp-files/performance-benchmarks-linux-write-iops.png)  

## <a name="next-steps"></a>后续步骤

- [Azure NetApp 文件：充分利用云存储](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf?hsCtaTracking=f2f560e9-9d13-4814-852d-cfc9bf736c6a%7C764e9d9c-9e6b-4549-97ec-af930247f22f)
