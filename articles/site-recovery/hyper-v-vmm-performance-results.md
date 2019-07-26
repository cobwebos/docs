---
title: 通过 Azure Site Recovery 将 VMM 云中的 Hyper-V VM 复制到辅助站点的测试结果 | Microsoft Docs
description: 本文介绍了使用 Azure Site Recovery 在 VMM 云中将 Hyper-V VM 复制到辅助站点时的性能测试。
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: a7413b2dcb24a42092eb2af9816b1d29a8306e19
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377225"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>从 Hyper-V 复制到辅助站点的测试结果


本文提供了在 System Center Virtual Machine Manager (VMM) 云中将 Hyper-V VM 复制到辅助数据中心时的性能测试的结果。

## <a name="test-goals"></a>测试目标

测试目标是观察在稳定状态复制期间 Site Recovery 的性能如何。

- 当 VM 已完成初始复制并且在同步增量更改时会发生稳定状态复制。
- 使用稳定状态对性能进行测量非常重要，因为除非发生意外中断，这是大多数 VM 保持的状态。
- 测试部署包括两个本地站点，每个站点中有一台 VMM 服务器。 此测试部署是典型的总公司/分公司部署，总公司作为主站点，分公司作为辅助站点或恢复站点。

## <a name="what-we-did"></a>我们的操作

下面是我们在测试过程中执行的操作：

1. 使用 VMM 模板创建的 VM。
2. 启动 VM 并捕获 12 个小时内的基准性能指标。
3. 在主 VMM 服务器和恢复 VMM 服务器上创建云。
4. 在 Site Recovery 中配置复制，包括源云和恢复云之间的映射。
5. 为 VM 启用保护并让它们完成初始复制。
6. 等待几个小时以便系统达到稳定状态。
7. 捕获 12 个小时内的性能指标，所有 VM 在那 12 个小时内都保持在预期的复制状态。
8. 测量基准性能指标与复制性能指标之间的增量。


## <a name="primary-server-performance"></a>主服务器性能

* Hyper-V 副本（由 Site Recovery 使用）以异步方式跟踪对日志文件的更改，从而最大限度地降低了主服务器上的存储开销。
* Hyper-V 副本利用自我维护的内存缓存最大程度地降低用于跟踪的 IOPS 开销。 它将向 VHDX 进行的写入存储在内存中，并在将日志发送到恢复站点之前将它们刷新到日志文件中。 如果写入数达到了预先确定的限制，也会发生磁盘刷新。
* 下图显示了复制的稳定状态 IOPS 开销。 我们可以看到由复制导致的 IOPS 开销大约为 5%，非常低。

  ![主服务器结果](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V 副本使用主服务器上的内存来优化磁盘性能。 如下图中所示，主群集中所有服务器上的内存开销都很低。 所显示的内存开销是复制使用的内存占 Hyper-V 服务器上安装的总内存的百分比。

![主服务器结果](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V 副本具有最小的 CPU 开销。 如图中所示，复制的开销范围是 2-3%。

![主服务器结果](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>辅助服务器性能

Hyper-V 副本使用恢复服务器上的少量内存来使存储操作数量最优化。 图中汇总了恢复服务器上的内存使用率。 所显示的内存开销是复制使用的内存占 Hyper-V 服务器上安装的总内存的百分比。

![辅助服务器结果](./media/hyper-v-vmm-performance-results/IC744916.png)

恢复站点上的 I/O 操作数量是主站点上的写入操作数量的一个函数。 让我们在与主站点上的总 I/O 操作量和写入操作量进行比较的情况下看一下恢复站点上的总 I/O 操作量。 图中显示，恢复站点上的总 IOPS 大约是

* 主站点上的写入 IOPS 的 1.5 倍。
* 主站点上的总 IOPS 的 37%。

![辅助服务器结果](./media/hyper-v-vmm-performance-results/IC744917.png)

![辅助服务器结果](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>对网络利用率的影响

在现有的每秒 5 GB 带宽中，在主节点与恢复节点（启用了压缩功能）之间平均每秒使用了 275 MB 网络带宽。

![网络利用率结果](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>对 VM 性能的影响

一个重要的注意事项是复制对在虚拟机上运行的生产工作负荷的影响。 如果主站点针对复制进行了充分的设置，则不应当对工作负荷产生任何影响。 Hyper-V 副本的轻量跟踪机制可以确保在虚拟机中运行的工作负荷在稳定状态复制期间不受影响。 下图中对此进行了解释。

此图显示了在启用复制之前和之后运行各种工作负荷的虚拟机执行的 IOPS。 可以看到两者之间没有差别。

![副本影响结果](./media/hyper-v-vmm-performance-results/IC744920.png)

下图显示了在启用复制之前和之后运行各种工作负荷的虚拟机的吞吐量。 可以看到复制没有明显影响。

![副本影响结果](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>结束语

我们的结果清晰地表明，与 Hyper-V 副本配合使用的 Site Recovery 可以针对大型群集以最小的开销很好地进行扩展。 Site Recovery 提供了简单的部署、复制、管理和监视。 Hyper-V 副本为成功进行复制扩展提供了必要的基础结构。 

## <a name="test-environment-details"></a>测试环境详细信息

### <a name="primary-site"></a>主站点

* 主站点具有一个群集，其中包含运行着 470 个虚拟机的五台 Hyper-V 服务器。
* 各个 VM 运行不同的工作负荷，并且都启用了 Site Recovery 保护。
* 群集节点的存储是由一个 iSCSI SAN 提供的。 型号 - Hitachi HUS130。
* 每台群集服务器具有四个网卡 (NIC)，每个为 1 Gbps。
* 两个网卡连接到一个 iSCSI 专用网络，另外两个网卡连接到一个外部企业网络。 其中一个外部网络保留给群集通信使用。

![主要硬件要求](./media/hyper-v-vmm-performance-results/IC744922.png)

| 服务器 | RAM | 型号 | 处理器 | 处理器数目 | NIC | 软件 |
| --- | --- | --- | --- | --- | --- | --- |
| 在群集中的 Hyper-V 服务器： <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-128ESTLAB-HOST25 包含256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20 GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V 角色 |
| VMM 服务器 |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>辅助站点

* 辅助站点具有一个六节点的故障转移群集。
* 群集节点的存储是由一个 iSCSI SAN 提供的。 型号 - Hitachi HUS130。

![主要硬件规格](./media/hyper-v-vmm-performance-results/IC744923.png)

| 服务器 | RAM | 型号 | 处理器 | 处理器数目 | NIC | 软件 |
| --- | --- | --- | --- | --- | --- | --- |
| 在群集中的 Hyper-V 服务器： <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2.30 GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V 角色 |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V 角色 |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V 角色 |
| VMM 服务器 |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>服务器工作负载

* 针对测试用途，我们选取了企业客户方案中常用的工作负载。
* 我们使用 [IOMeter](http://www.iometer.org) 与表中汇总的工作负载特性进行模拟。
* 所有 IOMeter 配置文件都设置为写入随机字节以模拟工作负载的最坏情况写入模式。

| 工作负载 | I/O 大小 (KB) | 访问百分比 | 读取百分比 | 未完成的 I/O | I/O 模式 |
| --- | --- | --- | --- | --- | --- |
| 文件服务器 |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |全部 100% 随机 |
| SQL Server (卷 1)<br />SQL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% 随机<br />100% 的顺序 |
| Exchange |32 |100% |67% |8 |100% 随机 |
| 工作站/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |两者都 100% 随机 |
| Web 文件服务器 |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |全部 75% 随机 |

### <a name="vm-configuration"></a>VM 配置

* 主群集上有 470 个 VM。
* 所有 VM 都带有 VHDX 磁盘。
* VM 运行表中汇总的工作负荷。 所有虚拟机都是使用 VMM 模板创建的。

| 工作负荷 | # VM | 最小 RAM (GB) | 最大 RAM (GB) | 每个 VM 的逻辑磁盘大小 (GB) | 最大 IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| 文件服务器 |50 |1 |2 |552 |22 |
| VDI |149 |0\.5 |1 |80 |6 |
| Web 服务器 |149 |0\.5 |1 |80 |6 |
| 总计 |470 | | |96.83 TB |4108 |

### <a name="site-recovery-settings"></a>Site Recovery 设置

* 针对本地到本地保护配置了 Site Recovery
* VMM 服务器配置有四个云，包含 Hyper-V 群集服务器及其 VM。

| 主 VMM 云 | 受保护的 VM | 复制频率 | 额外的恢复点 |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 分钟 |无 |
| PrimaryCloudRpo30s |47 |30 秒 |无 |
| PrimaryCloudRpo30sArp1 |47 |30 秒 |1 |
| PrimaryCloudRpo5m |235 |5 分钟 |None |

### <a name="performance-metrics"></a>性能指标

此表汇总了在部署中测量到的性能指标和计数器。

| 指标 | 计数器 |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Available memory |\Memory\Available MBytes |
| IOPS |\PhysicalDisk(_Total)\Disk Transfers/sec |
| VM read (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Read Operations/Sec |
| VM write (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Write Operations/S |
| VM read throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Read Bytes/sec |
| VM write throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Write Bytes/sec |

## Next steps

[Set up replication](hyper-v-vmm-disaster-recovery.md)\Processor(_Total)\` 处理器时间ation of Hyper-V VMs in VMM clouds to a secondary site with Azure Site Recovery | Microsoft Docs
description: This article provides information about performance testing for replication of Hyper-V VMs in VMM clouds to a secondary site using Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi

---
# Test results for Hyper-V replication to a secondary site


This article provides the results of performance testing when replicating Hyper-V VMs in System Center Virtual Machine Manager (VMM) clouds, to a secondary datacenter.

## Test goals

The goal of testing was to examine how Site Recovery performs during steady state replication.

- Steady state replication occurs when VMs have completed initial replication, and are synchronizing delta changes.
- It’s important to measure performance using steady state, because it’s the state in which most VMs remain, unless unexpected outages occur.
- The test deployment consisted of two on-premises sites, with a VMM server in each site. This test deployment is typical of a head office/branch office deployment, with head office acting as the primary site, and the branch office as the secondary or recovery site.

## What we did

Here's what we did in the test pass:

1. Created VMs using VMM templates.
2. Started VMs, and captured baseline performance metrics over 12 hours.
3. Created clouds on the primary and recovery VMM servers.
4. Configured replication in Site Recovery, including mapping between source and recovery clouds.
5. Enabled protection for VMs, and allowed them to complete initial replication.
6. Waited a couple of hours for system stabilization.
7. Captured performance metrics over 12 hours, where all VMs remained in an expected replication state for those 12 hours.
8. Measured the delta between the baseline performance metrics, and the replication performance metrics.


## Primary server performance

* Hyper-V Replica (used by Site Recovery) asynchronously tracks changes to a log file, with minimum storage overhead on the primary server.
* Hyper-V Replica utilizes self-maintained memory cache to minimize IOPS overhead for tracking. It stores writes to the VHDX in memory, and flushes them into the log file before the time that the log is sent to the recovery site. A disk flush also happens if the writes hit a predetermined limit.
* The graph below shows the steady state IOPS overhead for replication. We can see that the IOPS overhead due to replication is around 5%, which is quite low.

  ![Primary results](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V Replica uses memory on the primary server, to optimize disk performance. As shown in the following graph, memory overhead on all servers in the primary cluster is marginal. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Primary results](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V Replica has minimum CPU overhead. As shown in the graph, replication overhead is in the range of 2-3%.

![Primary results](./media/hyper-v-vmm-performance-results/IC744915.png)

## Secondary server performance

Hyper-V Replica uses a small amount of memory on the recovery server, to optimize the number of storage operations. The graph summarizes the memory usage on the recovery server. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744916.png)

The amount of I/O operations on the recovery site is a function of the number of write operations on the primary site. Let’s look at the total I/O operations on the recovery site in comparison with the total I/O operations and write operations on the primary site. The graphs show that the total IOPS on the recovery site is

* Around 1.5 times the write IOPS on the primary.
* Around 37% of the total IOPS on the primary site.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744917.png)

![Secondary results](./media/hyper-v-vmm-performance-results/IC744918.png)

## Effect on network utilization

An average of 275 Mb per second of network bandwidth was used between the primary and recovery nodes (with compression enabled), against an existing bandwidth of 5 Gb per second.

![Results network utilization](./media/hyper-v-vmm-performance-results/IC744919.png)

## Effect on VM performance

An important consideration is the impact of replication on production workloads running on the virtual machines. If the primary site is adequately provisioned for replication, there shouldn’t be any impact on the workloads. Hyper-V Replica’s lightweight tracking mechanism ensures that workloads running in the virtual machines are not impacted during steady-state replication. This is illustrated in the following graphs.

This graph shows IOPS performed by virtual machines running different workloads, before and after replication was enabled. You can observe that there is no difference between the two.

![Replica effect results](./media/hyper-v-vmm-performance-results/IC744920.png)

The following graph shows the throughput of virtual machines running different workloads, before and after replication was enabled. You can observe that replication has no significant impact.

![Results replica effects](./media/hyper-v-vmm-performance-results/IC744921.png)

## Conclusion

The results clearly show that Site Recovery, coupled with Hyper-V Replica, scales well with minimum overhead for a large cluster. Site Recovery provides simple deployment, replication, management and monitoring. Hyper-V Replica provides the necessary infrastructure for successful replication scaling. 

## Test environment details

### Primary site

* The primary site has a cluster containing five Hyper-V servers, running 470 virtual machines.
* The VMs run different workloads, and all have Site Recovery protection enabled.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.
* Each cluster server has four network cards (NICs) of one Gbps each.
* Two of the network cards are connected to an iSCSI private network, and two are connected to an external enterprise network. One of the external networks is reserved for cluster communications only.

![Primary hardware requirements](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 has 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Secondary site

* The secondary site has a six-node failover cluster.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.

![Primary hardware specification](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2.30GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Server workloads

* For test purposes we picked workloads commonly used in enterprise customer scenarios.
* We use [IOMeter](http://www.iometer.org) with the workload characteristic summarized in the table for simulation.
* All IOMeter profiles are set to write random bytes to simulate worst-case write patterns for workloads.

| Workload | I/O size (KB) | % Access | %Read | Outstanding I/Os | I/O pattern |
| --- | --- | --- | --- | --- | --- |
| File Server |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |All 100% random |
| SQL Server (volume 1)<br />SQL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% random<br />100% sequential |
| Exchange |32 |100% |67% |8 |100% random |
| Workstation/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Both 100% random |
| Web File Server |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |All 75% random |

### VM configuration

* 470 VMs on the primary cluster.
* All VMs with VHDX disk.
* VMs running workloads summarized in the table. All were created with VMM templates.

| Workload | # VMs | Minimum RAM (GB) | Maximum RAM (GB) | Logical disk size (GB) per VM | Maximum IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| File Server |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Web server |149 |.5 |1 |80 |6 |
| TOTAL |470 | | |96.83 TB |4108 |

### Site Recovery settings

* Site Recovery was configured for on-premises to on-premises protection
* The VMM server has four clouds configured, containing the Hyper-V cluster servers and their VMs.

| Primary VMM cloud | Protected VMs | Replication frequency | Additional recovery points |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 mins |None |
| PrimaryCloudRpo30s |47 |30 secs |None |
| PrimaryCloudRpo30sArp1 |47 |30 secs |1 |
| PrimaryCloudRpo5m |235 |5 mins |None |

### Performance metrics

The table summarizes the performance metrics and counters that were measured in the deployment.

| Metric | Counter |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| 可用内存 |\Memory\Available MBytes |
| IOPS |\PhysicalDisk(_Total)\Disk Transfers/sec |
| VM 读取 (IOPS) 操作数/秒 |\Hyper-V Virtual Storage Device(\<VHD>)\Read Operations/Sec |
| VM 写入 (IOPS) 操作数/秒 |\Hyper-V 虚拟存储设备 (\<VHD >) \Write Operations/秒 |
| VM 读取吞吐量 |\Hyper-V Virtual Storage Device(\<VHD>)\Read Bytes/sec |
| VM 写入吞吐量 |\Hyper-V Virtual Storage Device(\<VHD>)\Write Bytes/sec |

## <a name="next-steps"></a>后续步骤

[设置复制](hyper-v-vmm-disaster-recovery.md)
