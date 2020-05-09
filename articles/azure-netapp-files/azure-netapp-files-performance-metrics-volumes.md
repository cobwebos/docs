---
title: 推荐的性能基准测试-Azure NetApp 文件
description: 了解有关使用 Azure NetApp 文件的卷性能和指标的基准测试建议。
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: a254465d9055fcf1cf10a2a1d6eb82b2afc16f0b
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692267"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Azure NetApp 文件的性能基准测试建议

本文提供了有关使用 Azure NetApp 文件的卷性能和指标的基准测试建议。

## <a name="overview"></a>概述

为了了解 Azure NetApp 文件量的性能特征，你可以使用开源工具[FIO](https://github.com/axboe/fio)来运行一系列基准，以模拟各种工作负荷。 FIO 可以安装在基于 Linux 和 Windows 的操作系统上。  它是一个极佳的工具，可用于获取卷的 IOPS 和吞吐量的快速快照。

### <a name="vm-instance-sizing"></a>VM 实例大小调整

为获得最佳结果，请确保使用适当调整大小的虚拟机（VM）实例以执行测试。 下面的示例使用 Standard_D32s_v3 实例。 有关 VM 实例大小的详细信息，请参阅 Azure 中适用于基于 Windows 的 Vm 的[windows 虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)，以及 azure 中适用于 Linux 的 Vm 的[linux 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

### <a name="azure-netapp-files-volume-sizing"></a>Azure NetApp 文件卷大小调整

确保为预期的性能级别选择了正确的服务级别和卷配额大小。 有关详细信息，请参阅[Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)。

### <a name="virtual-network-vnet-recommendations"></a>虚拟网络（VNet）建议

应在与 Azure NetApp 文件相同的 VNet 中执行基准测试。 下面的示例演示了建议：

![VNet 建议](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>安装 FIO

FIO 适用于 Linux 和 Windows 的二进制格式。 按照[FIO](https://github.com/axboe/fio)中的二进制包部分为你选择的平台安装。

## <a name="fio-examples-for-iops"></a>IOPS 的 FIO 示例 

本部分中的 FIO 示例使用以下设置：
* VM 实例大小： D32s_v3
* 容量池服务级别和大小： Premium/50 TiB
* 卷配额大小： 48 TiB

下面的示例显示了 FIO 随机读取和写入。

### <a name="fio-8k-block-size-100-random-reads"></a>FIO：8k 块大小100% 随机读取

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>输出：显示的68k 读取 IOPS

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO：8k 块大小100% 随机写入

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>输出：显示的73k 写入 IOPS

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>带宽的 FIO 示例

本节中的示例显示了 FIO 顺序读取和写入。

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO：64k 块大小100% 顺序读取

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>输出：显示 11.8 Gb/秒的吞吐量

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO：64k 块大小100% 顺序写入

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>输出：显示 12.2 Gb/秒的吞吐量

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>卷指标

Azure NetApp 文件性能数据可通过 Azure Monitor 计数器提供。 可以通过 Azure 门户获取计数器，并 REST API GET 请求。 

您可以查看以下信息的历史数据：
* 平均读取延迟 
* 平均写入延迟 
* 读取 IOPS （平均值）
* 写入 IOPS （平均值）
* 卷逻辑大小（平均值）
* 卷快照大小（平均值）

### <a name="using-azure-monitor"></a>使用 Azure Monitor 

你可以从 "指标" 页按卷访问 Azure NetApp 文件计数器，如下所示：

![Azure Monitor 度量值](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

你还可以通过转到 "指标" 页，筛选 NetApp 并指定感兴趣的卷计数器，在 Azure Monitor 为 Azure NetApp 文件创建仪表板： 

![Azure 监视器仪表板](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure Monitor API 访问

可以通过使用 REST API 调用来访问 Azure NetApp 文件计数器。 有关容量池和卷的计数器，请参阅[Azure Monitor： netAppAccounts/capacityPools/卷支持的指标](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes)。

以下示例显示了用于查看逻辑卷大小的 GET URL：

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>后续步骤

- [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
- [Linux 性能基准](performance-benchmarks-linux.md)