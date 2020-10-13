---
title: Azure NetApp 文件单一卷上的 Oracle 数据库性能 |Microsoft Docs
description: 介绍 Oracle 数据库上的 Azure NetApp 文件单一卷的性能测试结果。
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
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: c6cdf2f6dada0aa4dea2f70f18237b7ee39e3ea1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91571283"
---
# <a name="oracle-database-performance-on-azure-netapp-files-single-volumes"></a>Azure NetApp 文件单卷上的 Oracle 数据库性能

本文介绍有关云中 Oracle 的以下主题。 对于数据库管理员、云架构师或存储架构师，这些主题可能会特别感兴趣：   

* 当你 (OLTP) 工作负荷 (大多数随机 i/o) 或联机分析处理 (OLAP) 工作负荷 (大部分顺序 i/o) 时，性能会如下所示：   
* 常规 Linux 内核 NFS (kNFS) 客户端和 Oracle 自己的直接 NFS 客户端之间的性能差异是多少？
* 就带宽而言，单个 Azure NetApp 文件的性能是否足够？

## <a name="testing-environment-and-components"></a>测试环境和组件

下图说明了用于测试的环境。 为了保持一致性和简易性，使用了 Ansible 的行动手册来部署测试平台的所有元素。

![Oracle 测试环境](../media/azure-netapp-files/performance-oracle-test-environment.png)  

### <a name="virtual-machine-configuration"></a>虚拟机配置

这些测试为虚拟机使用了以下设置：
* 操作系统：   
    RedHat Enterprise Linux 7.8 (wle-ora01) 
* 实例类型：   
    测试中使用了两个模型– D32s_v3 和 D64s_v3
* 网络接口计数：   
    一个 (1) 置于子网3中  
* 盘   
    Oracle 二进制文件和操作系统放置在单个高级磁盘中

### <a name="azure-netapp-files-configuration"></a><a name="anf_config"></a>Azure NetApp 文件配置
这些测试使用了以下 Azure NetApp 文件配置：   

* 容量池大小：  
    已配置池的各种大小： 4 TiB、8 TiB、16 TiB、32 TiB 
* 服务级别：  
    超高 (128 MiB/s，每 1 TiB 分配的卷容量) 
* 卷:  
    已评估一个和两个卷测试

### <a name="workload-generator"></a>工作负荷生成器 

测试使用的工作负荷生成了 SLOB 2.5.4.2。 SLOB (较为愚蠢的 Oracle 基准测试) 在 Oracle 空间中提供了一个已知的工作负荷生成器，旨在使用 SGA 缓冲的物理 i/o 工作负载对 i/o 子系统进行压力和测试。  

SLOB 2.5.4.2 不支持可插接式数据库 (PDB) 。 因此，已将更改添加到 `setup.sh` 和脚本， `runit.sh` 以向其添加 PDB 支持。  

以下各节介绍了测试中使用的 SLOB 变量。

#### <a name="workload-80-select-20-update--random-io--slobconf-variables"></a>工作负荷80% 选择，20% 更新 |随机 i/o – `slob.conf` 变量   

`UPDATE_PCT=20`   
`SCAN_PCT=0`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

#### <a name="workload-100-select--sequential-io--slobconf-variables"></a>工作负荷100% 选择 |顺序 i/o – `slob.conf` 变量

`UPDATE_PCT=0`   
`SCAN_PCT=100`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

### <a name="database"></a>数据库

用于测试的 Oracle 版本 Oracle Database Enterprise Edition 19.3.0.0。

Oracle 参数如下所示：  
* `sga_max_size`: 4096M
* `sga_target`：4096
* `db_writer_processes`：12
* `awr_pdb_autoflush_enabled`： true
* `filesystemio_options`: SETALL
* `log_buffer`：134217728

已为 SLOB 数据库创建了 PDB。

下图显示了一个名为 PERFIO 且大小为 600 GB (20 个数据文件的表空间，每个) 创建为托管四个 SLOB 用户架构。 每个用户架构的大小为 125 GB。

![Oracle 数据库](../media/azure-netapp-files/performance-oracle-tablespace.png)  

## <a name="performance-metrics"></a>性能指标

目标是报告应用程序遇到的 IO 性能。 因此，本文中的所有关系图使用 Oracle 数据库通过其自动工作负荷存储库报告的指标 (AWR) 报表。 图表中使用的指标如下：   

* **平均 IO 请求数/秒**   
    对应于负载配置节中平均读取 IO 请求数/秒和平均写入 IO 请求数的总和
* **平均 IO MB/秒**   
    对应于负载配置文件部分的平均读取 IO MB/秒和平均写入 IO MB/秒的总和
* **平均读取延迟**   
    对应于 Oracle 等待事件 "数据库文件顺序读取" 的平均延迟（微秒）
* **线程数/架构**   
    对应于每个用户架构的 SLOB 线程数

## <a name="performance-measurement-results"></a>性能测量结果  

本部分介绍性能度量的结果。

### <a name="linux-knfs-client-vs-oracle-direct-nfs"></a>Linux kNFS 客户端和 Oracle 直接 NFS

此方案在 Azure VM Standard_D32s_v3 上运行， (Intel 2673 v4 @ 2.30 GHz) 。 工作负荷为75% 选择和25% 更新，大部分为随机 i/o，数据库缓冲区命中 ~ 7.5%。 

如下图所示，Oracle DNFS 客户端比常规 Linux kNFS 客户端提供的吞吐量高达 2.8 x：  

![与 Oracle 直接 NFS 相比，Linux kNFS 客户端](../media/azure-netapp-files/performance-oracle-kfns-compared-dnfs.png)  

下图显示了读取操作的滞后时间曲线。 在这种情况下，kNFS 客户端的瓶颈是在客户端与 NFS 服务器之间建立的单个 NFS TCP 套接字连接 (Azure NetApp 文件量) 。  

![与 Oracle 直接 NFS 滞后时间曲线相比，Linux kNFS 客户端](../media/azure-netapp-files/performance-oracle-latency-curve.png)  

由于能够创建数百个 TCP 套接字连接，DNFS 客户端能够每秒推送更多 IO 请求，因此利用了并行度。 如 [Azure NetApp 文件配置](#anf_config)中所述，分配的容量的每个额外 TiB 都允许额外的 128MiB/秒带宽。 DNFS 表面的吞吐量为 1 GiB/秒，这是 8 TiB 容量选择施加的限制。 如果有更多的容量，则会有更多的吞吐量。

吞吐量只是一个注意事项。 另一个考虑因素是延迟，这会对用户体验产生重大影响。 如下图所示，kNFS 与 DNFS 相比，延迟增加可能会更快。 

![与 Oracle 直接 NFS 读取延迟相比，Linux kNFS 客户端](../media/azure-netapp-files/performance-oracle-read-latency.png)  

直方图提供对数据库延迟的出色了解。 下图提供了一个完整的视图，从记录的 "数据库文件顺序读取" 的角度来看，同时在最高并发数据点上使用 DNFS (32 线程/架构) 。 如下图所示，47% 的所有读取操作都在512微秒和1000微秒之间进行，而所有读取操作的90% 在延迟时间低于 2 ms。

![与 Oracle 直接 NFS 直方图相比，Linux kNFS 客户端](../media/azure-netapp-files/performance-oracle-histogram-read-latency.png)  

总之，在改善 NFS 上的 Oracle 数据库实例的性能时，DNFS 是必需的。

### <a name="single-volume-performance-limits"></a>单个卷性能限制

本部分介绍带有随机 i/o 和顺序 i/o 的单个卷的性能限制。 

#### <a name="random-io"></a>随机 i/o

与 8 TB 的 Azure NetApp 文件性能配额相比，DNFS 能够消耗更多的带宽。 通过将 Azure NetApp 文件卷容量增加到16个 TiB，这是一种即时更改，从 1024 MiB/s 增加到 2048 MiB/s 的卷带宽量。 

下图显示了80% 选择和20% 更新工作负荷的配置，以及数据库缓冲区命中率为8%。 SLOB 可以为每秒 200000 NFS i/o 请求驱动器。 考虑到每个操作的大小为 8-KiB，所测试的系统可以提供 ~ 200000 IO 请求数/秒或 1600 MiB/秒。
 
![Oracle DNFS 吞吐量](../media/azure-netapp-files/performance-oracle-dnfs-throughput.png)  

以下读取滞后时间曲线关系图显示，随着读取吞吐量的增加，延迟将在 1 ms 行下顺畅地增长，并按 ~ 165000 的平均读取 IO 请求数（约1.3 毫秒）到达曲线的 knee。  对于 Azure 云中几乎任何其他技术，此值是 i/o 速率无法完成的令人难以置信的延迟值。 

![Oracle DNFS 滞后时间曲线](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

#### <a name="sequential-io"></a>顺序 i/o  

如下面的关系图中所示，并非所有 i/o 都是随机的，考虑 RMAN 备份或全表扫描，例如，工作负荷需要尽可能多的带宽。  使用前面所述的相同配置，但将卷调整到了 32 TiB，下图显示单个 Oracle DB 实例可驱动超过 3900 MB/秒的吞吐量，非常接近于 Azure NetApp 文件量的性能配额 32 TB (128 MB/秒 * 32 = 4096 MB/s) 。

![Oracle DNFS i/o](../media/azure-netapp-files/performance-oracle-dnfs-io.png)  

总而言之，Azure NetApp 文件可帮助你将 Oracle 数据库带入云中。 它在数据库要求时提供性能。 你可以随时动态且无中断地调整卷配额。

## <a name="next-steps"></a>后续步骤

- [Azure NetApp 文件的性能基准测试建议](azure-netapp-files-performance-metrics-volumes.md)
- [Linux 性能基准](performance-benchmarks-linux.md)