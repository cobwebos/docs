---
title: 基准测试的卷性能和指标使用 Azure NetApp 文件 |Microsoft Docs
description: 提供了基准测试的卷性能和指标使用 Azure NetApp 文件的建议。
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
ms.date: 06/26/2019
ms.author: b-juche
ms.openlocfilehash: 12ae9e313655924f11799152b5e58b77776c135c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478805"
---
# <a name="benchmark-testing-for-volume-performance-and-metrics-using-azure-netapp-files"></a>使用 Azure NetApp 文件时的卷性能和指标的基准测试

本文提供了基准测试的卷性能和指标使用 Azure NetApp 文件的建议。

## <a name="overview"></a>概述

若要了解 Azure NetApp 文件卷的性能特征，可以使用开放源代码工具[FIO](https://github.com/axboe/fio)运行一系列基准来模拟各种工作负荷。 可以在这两个 Linux 上安装 FIO 和 Windows 基于操作系统。  它是一种极好的工具，用于快速获取快照的 IOPS 和吞吐量的卷。

### <a name="vm-instance-sizing"></a>VM 实例大小调整

为获得最佳结果，请确保将相应地调整大小以执行测试的虚拟机 (VM) 实例。 以下示例使用 Standard_D32s_v3 实例。 有关 VM 实例大小的详细信息，请参阅[大小的 Windows Azure 中的虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)对于基于 Windows 的 Vm，并[在 Azure 中 Linux 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)适用于基于 Linux 的 Vm。

### <a name="azure-netapp-files-volume-sizing"></a>Azure 的 NetApp 文件卷大小调整

请确保选择正确的服务级别和卷配额大小的预期的性能级别。 请参阅[Azure NetApp 文件服务级别](azure-netapp-files-service-levels.md)有关详细信息。

### <a name="virtual-network-vnet-recommendations"></a>虚拟网络 (VNet) 的建议

您应该执行基准测试与 Azure NetApp 文件相同的 VNet 中。 下面的示例演示了建议：

![VNet 的建议](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>FIO 的安装

FIO 现已推出适用于 Linux 和 Windows 的二进制格式。 请按照中的二进制程序包部分[FIO](https://github.com/axboe/fio)安装适用于所选的平台。

## <a name="fio-examples-for-iops"></a>IOPS 的 FIO 示例 

在本部分中的 FIO 示例使用以下设置：
* VM 实例大小：D32s_v3
* 容量池的服务级别和大小：高级 / 50 TiB
* 卷的配额大小：48 TiB

以下示例演示 FIO 随机读取和写入。

### <a name="fio-8k-block-size-100-random-reads"></a>FIO:8k 块大小 100%随机读取

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>输出：68 k 读取 IOPS 显示

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO:8k 块大小 100%随机写入

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>输出：73 k 写入 IOPS 显示

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>带宽的 FIO 示例

此部分显示 FIO 顺序中的示例读取和写入。

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO:64k 块大小 100%顺序读取

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>输出：11.8 显示千兆位/秒吞吐量

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO:64k 块大小 100%顺序写入

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>输出：12.2 显示千兆位/秒吞吐量

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>卷指标

可通过 Azure Monitor 计数器 azure 的 NetApp 文件性能数据。 通过 Azure 门户和 REST API GET 请求提供了计数器。 

您可以查看历史数据的以下信息：
* 平均读取延迟 
* 平均写入延迟 
* 读取 IOPS （平均）
* 写入 IOPS （平均）
* 卷的逻辑大小 （平均）
* 卷快照的大小 （平均）

### <a name="using-azure-monitor"></a>使用 Azure Monitor 

可以从指标页中，访问 Azure NetApp 文件计数器基于每个卷，如下所示：

![Azure Monitor 指标](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

您还可以创建仪表板在 Azure Monitor 中为 Azure NetApp 文件通过转到指标页、 NetApp、 筛选和指定感兴趣的卷计数器： 

![Azure 监视器仪表板](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure 监视器 API 访问权限

可以使用 REST API 调用来访问 Azure NetApp 文件计数器。 请参阅[支持的 Azure Monitor 指标：Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes)容量池和卷的计数器。

下面的示例演示获取 URL 用于查看逻辑卷的大小：

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>后续步骤

- [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
- [Azure NetApp 文件的性能基准](azure-netapp-files-performance-benchmarks.md)