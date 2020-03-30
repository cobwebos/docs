---
title: 推荐的性能基准测试 - Azure NetApp 文件
description: 使用 Azure NetApp 文件了解卷性能和指标的基准测试建议。
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: 8f354152c23dd7ad0413f27585d724f8070ca003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551516"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Azure NetApp 文件的性能基准测试建议

本文提供使用 Azure NetApp 文件提供的卷性能和指标的基准测试建议。

## <a name="overview"></a>概述

要了解 Azure NetApp 文件卷的性能特征，可以使用开源工具[FIO](https://github.com/axboe/fio)运行一系列基准测试来模拟各种工作负载。 FIO 可以安装在 Linux 和基于 Windows 的操作系统上。  它是获取卷 IOPS 和吞吐量的快速快照的绝佳工具。

### <a name="vm-instance-sizing"></a>VM 实例大小调整

为获得最佳结果，请确保使用的虚拟机 （VM） 实例的大小适合执行测试。 以下示例使用Standard_D32s_v3实例。 有关 VM 实例大小的详细信息，请参阅 Azure 中基于 Windows 的 VM 的[Windows 虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)，以及 Azure 中基于 Linux 的 VM 的[Linux 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

### <a name="azure-netapp-files-volume-sizing"></a>Azure NetApp 文件卷大小调整

确保为预期性能级别选择正确的服务级别和卷配额大小。 有关详细信息[，请参阅 Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)。

### <a name="virtual-network-vnet-recommendations"></a>虚拟网络 （VNet） 建议

应在与 Azure NetApp 文件相同的 VNet 中执行基准测试。 下面的示例演示了以下建议：

![VNet 建议](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>国际职能指令手册的安装

FIO 在 Linux 和 Windows 中都提供二进制格式。 按照[FIO](https://github.com/axboe/fio)中的二进制包部分进行安装，以适合您的平台。

## <a name="fio-examples-for-iops"></a>IOPS 的 FIO 示例 

本节中的 FIO 示例使用以下设置：
* VM 实例大小：D32s_v3
* 容量池服务级别和大小：高级 / 50 TiB
* 卷配额大小： 48 TiB

以下示例显示了 FIO 随机读取和写入。

### <a name="fio-8k-block-size-100-random-reads"></a>FIO： 8k 块大小 100% 随机读取

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>输出： 显示 68k 读取 IOPS

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO： 8k 块大小 100% 随机写入

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>输出：显示 73k 写入 IOPS

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>带宽 FIO 示例

本节中的示例显示了 FIO 顺序读取和写入。

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO： 64k 块大小 100% 顺序读取

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>输出：显示 11.8 Gbit/s 吞吐量

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO：64k 块大小 100% 顺序写入

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>输出：显示 12.2 Gbit/s 吞吐量

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>卷指标

Azure NetApp 文件性能数据可通过 Azure 监视器计数器获得。 计数器可通过 Azure 门户和 REST API GET 请求提供。 

您可以查看历史数据以获取有关以下信息：
* 平均读取延迟 
* 平均写入延迟 
* 阅读 IOPS（平均）
* 写入 IOPS（平均）
* 卷逻辑大小（平均值）
* 卷快照大小（平均值）

### <a name="using-azure-monitor"></a>使用 Azure Monitor 

您可以通过"指标"页按量访问 Azure NetApp 文件计数器，如下所示：

![Azure 监视器指标](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

还可以通过访问"指标"页、筛选 NetApp 并指定感兴趣的卷计数器，在 Azure 监视器监视器中为 Azure NetApp 文件创建仪表板： 

![Azure 监视器仪表板](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure 监视器 API 访问

您可以使用 REST API 调用访问 Azure NetApp 文件计数器。 请参阅 Azure 监视器支持指标：适用于容量池和卷的计数器的[Microsoft.NetApp/NetApp/NetApp 帐户/容量池/卷](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes)。

下面的示例显示了用于查看逻辑卷大小的 GET URL：

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>后续步骤

- [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
- [Azure NetApp 文件的性能基准](azure-netapp-files-performance-benchmarks.md)