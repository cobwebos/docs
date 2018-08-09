---
title: 在 Azure 上设计和实现 Oracle 数据库 | Microsoft 文档
description: 在 Azure 环境中设计和实现 Oracle 数据库。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: d4c0bbdfb1afcef33727ba4b5b432c5de79168d4
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495214"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>在 Azure 中设计和实现 Oracle 数据库

## <a name="assumptions"></a>假设

- 计划将 Oracle 数据库从本地迁移到 Azure。
- 了解 Oracle AWR 报表中的各种指标。
- 了解应用程序性能和平台利用率的基线。

## <a name="goals"></a>目标

- 了解如何在 Azure 中优化 Oracle 部署。
- 探索 Azure 环境中用于 Oracle 数据库的性能调整选项。

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>本地实现和 Azure 实现的区别 

以下是将本地应用程序迁移到 Azure 时需要注意的几个要点。 

其中的一个重要区别是，在 Azure 实现中与其他客户端共享虚拟机、磁盘和虚拟网络等资源。 此外，还可按需限制资源。 Azure 更注重从故障中恢复 (MTTR)，而不是关注避免故障 (MTBF)。

下表列出了 Oracle 数据库的本地实现和 Azure 实现之间的一些区别。

> 
> |  | 本地实现 | Azure 实现 |
> | --- | --- | --- |
> | **网络** |LAN/WAN  |SDN（软件定义的网络）|
> | 安全组 |IP/端口限制工具 |[网络安全组 (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **复原能力** |MTBF（平均无故障时间） |MTTR（平均恢复时间）|
> | **计划内维护** |修补/升级|[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)（由 Azure 管理的修补/升级） |
> | **资源** |专用  |与其他客户端共享|
> | **区域** |数据中心 |[区域对](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **存储** |SAN/物理磁盘 |[Azure 托管的存储](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **缩放** |垂直缩放 |横向缩放|


### <a name="requirements"></a>要求

- 确定数据库大小和增长率。
- 确定 IOPS 要求，可根据 Oracle AWR 报表或其他网络监视工具进行估计。

## <a name="configuration-options"></a>配置选项

在 Azure 环境中，可以优化四个潜在区域来提高性能：

- 虚拟机大小
- 网络吞吐量
- 磁盘类型和配置
- 磁盘缓存设置

### <a name="generate-an-awr-report"></a>生成 AWR 报表

如果有现有的 Oracle 数据库并且计划将其迁移到 Azure，则有多个选项可供使用。 可以运行 Oracle AWR 报表来获取指标（IOPS、Mbps、GiB 等）。 然后基于收集的指标选择虚拟机。 或者，也可以联系基础结构团队，获取类似的信息。

可考虑分别在正常工作负荷与峰值工作负荷期间运行 AWR 报表，以便进行比较。 根据这些报表，可基于平均工作负荷或最大工作负荷来调整虚拟机的大小。

下面的示例演示了如何生成 AWR 报表：

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>关键指标

以下是可以从 AWR 报表获得的指标：

- 内核总数
- CPU 时钟速度
- 总内存 (GB)
- CPU 使用率
- 峰值数据传输率
- I/O 更改（读/写）速率
- 恢复日志速率 (MBPs)
- 网络吞吐量
- 网络延迟率（低/高）
- 数据库大小 (GB)
- 通过 SQL*Net 从客户端接收/向客户端发送的字节数

### <a name="virtual-machine-size"></a>虚拟机大小

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1.根据 AWR 报表提供的 CPU、内存和 I/O 使用情况估计虚拟机的大小

用户可能想要了解超时最严重的五大前景事件，这些事件指明了系统的瓶颈所在。

例如，在下图中，日志文件同步在最顶部。 它指示 LGWR 将日志缓冲区写入恢复日志文件前需要等待的数量。 这些结果指示存储区或磁盘的性能需要提高。 此外，此图还显示了 CPU（内核）数和内存大小。

![“AWR 报表”页的屏幕截图](./media/oracle-design/cpu_memory_info.png)

下图显示了读写的 I/O 总数。 在报表生成期间，读取了 59 GB 的数据，写入了 247.3 GB 的数据。

![“AWR 报表”页的屏幕截图](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2.选择虚拟机

从 AWR 报表收集信息后，应根据该信息选择一个虚拟机，其大小应大致可满足需求。 可在[内存优化](../../linux/sizes-memory.md)一文中查看可用的虚拟机列表。

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3.根据 ACU 微调相似虚拟机系列的虚拟机大小

选择虚拟机后，请注意虚拟机的 ACU。 可根据 ACU 值选择不同的虚拟机，以便更好地满足需求。 有关详细信息，请参阅 [Azure 计算单元](https://docs.microsoft.com/azure/virtual-machines/windows/acu)。

![“ACU 单元”页的屏幕截图](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>网络吞吐量

下图显示了吞吐量与 IOPS 之间的关系：

![吞吐量的屏幕截图](./media/oracle-design/throughput.png)

总网络吞吐量的估算以下列信息为基础：
- SQL*Net 流量
- MBps x 服务器数量（出站流，如 Oracle Data Guard）
- 其他因素，如应用程序复制

![SQL*Net 吞吐量的屏幕截图](./media/oracle-design/sqlnet_info.png)

基于网络带宽要求，有多种网关类型可供选择。 例如，Basic、VpnGw 和 Azure ExpressRoute。 有关详细信息，请参阅 [VPN 网关定价](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h)页。

**建议**

- 与本地部署相比，网络延迟更高。 减少网络往返次数可显著提高性能。
- 若要减少网络往返，可在同一虚拟机上合并事务繁多的应用或“聊天式”应用。

### <a name="disk-types-and-configurations"></a>磁盘类型和配置

- 
  *
  *默认 OS 磁盘：这些磁盘类型提供永久性数据和缓存。 它们针对启动时的 OS 访问进行了优化，不适用于事务性或数据仓库（分析）工作负荷。

- 非托管磁盘：使用这些磁盘类型，可管理存储虚拟硬盘 (VHD) 文件（这些文件与虚拟机磁盘相对应）的存储帐户。 VHD 文件作为页 Blob 存储在 Azure 存储帐户中。

- 托管磁盘：由 Azure 管理用于 VM 磁盘的存储帐户。 需要指定所需的磁盘类型（高级或标准）和磁盘大小。 Azure 将创建和管理该磁盘。

- 高级存储磁盘：这些磁盘类型最适用于生产工作负荷。 高级存储支持可附加到特定大小系列虚拟机（例如 DS、DSv2、GS 和 F 系列虚拟机）的虚拟机磁盘。 高级磁盘大小各异，可选择的磁盘大小范围为 32 GB 到 4,096 GB。 每种磁盘大小都有自身的性能规范。 根据应用程序的要求，可将一个或多个磁盘附加到 VM。

从门户创建新托管磁盘时，可选择要使用的磁盘类型对应的“帐户类型”。 请注意，下拉菜单中并未显示全部的可用磁盘。 选择特定虚拟机大小后，菜单将只显示以此虚拟机大小为基础的可用高级存储 SKU。

![“托管磁盘”页的屏幕截图](./media/oracle-design/premium_disk01.png)

有关详细信息，请参阅 [VM 的高性能高级存储与托管磁盘](https://docs.microsoft.com/azure/storage/storage-premium-storage)。

在虚拟机上配置存储后，可能需要在创建数据库前先对磁盘进行负载测试。 了解延迟和吞吐量的 I/O 速率有助于判断 VM 是否支持预计的吞吐量，并实现预期延迟目标。

有多种工具可用于应用程序负载测试，包括 Oracle Orion、Sysbench 和 Fio。

部署 Oracle 数据库后重新运行负载测试。 启动正常工作负荷与峰值工作负荷，结果将显示所在环境的基线。

与根据存储大小调整存储大小相比，根据 IOPS 速率进行调整可能更有用。 例如，如果要求的 IOPS 是 5,000，但是你只需 200 GB，则即使 P30 级别高级磁盘的存储空间大于 200 GB，你可能仍会使用它。

可从 AWR 报表获取 IOPS 速率。 它由恢复日志、物理读取和写入速率确定。

![“AWR 报表”页的屏幕截图](./media/oracle-design/awr_report.png)

例如，恢复大小是每秒 12,200,000 字节，相当于 11.63 MBP。
则 IOPS 为 12,200,000 / 2,358 = 5,174。

在明确 I/O 要求后，可选择最适合的驱动器组合以满足这些要求。

**建议**

- 对于 Data 表空间，使用托管存储或 Oracle ASM 在多个磁盘间分散 I/O 工作负荷。
- 随着读取密集型和写入密集型操作的 I/O 块的大小增大，添加更多数据磁盘。
- 为大型顺序进程增加块大小。
- 使用数据压缩来降低 I/O（针对数据和索引）。
- 将恢复日志、system、temps 和 undo TS 分隔在不同的数据磁盘上。
- 不要将任何应用程序文件放在默认 OS 磁盘 (/dev/sda) 中。 这些磁盘未针对快速 VM 启动时间进行优化，可能无法为应用程序提供良好的性能。

### <a name="disk-cache-settings"></a>磁盘缓存设置

有三个主机缓存选项：

- 只读：缓存所有请求，供将来读取。 将所有写入直接保存到 Azure Blob 存储。

- 读取/写入：这是一种“预读”算法。 将缓存所有读取和写入，供将来读取。 非直写式写入首先会保存在本地缓存中。 对于 SQL Server，写入将保存到 Azure 存储，因为它使用直写。 它还为轻型工作负荷提供最低的磁盘延迟。

- 无（已禁用）：使用此选项可绕过缓存。 所有数据都传输到磁盘，并保存在 Azure 存储中。 此方法可为 I/O 密集型工作负荷提供最高的 I/O 速率。 此外，还需要考虑“事务成本”。

**建议**

要最大限度提高吞吐量，建议对主机缓存启用“无”选项。 请注意，对于高级存储，在使用“只读”或“无”选项来装载文件系统时，必须禁用“屏障”。 通过 UUID 将 /etc/fstab 文件更新到磁盘。

有关详细信息，请参阅 [Linux VM 的高级存储](https://docs.microsoft.com/azure/storage/storage-premium-storage#premium-storage-for-linux-vms)。

![“托管磁盘”页的屏幕截图](./media/oracle-design/premium_disk02.png)

- 对于 OS 磁盘，请使用默认的“读取/写入”缓存。
- 对于 SYSTEM、TEMP 和 UNDO，请使用“无”选项进行缓存。
- 对于 DATA，使用“无”选项进行缓存。 但是，如果数据库为只读或读取密集型数据库，请使用“只读”缓存。

保存数据磁盘设置后，将无法更改主机缓存设置，除非在 OS 级别卸载驱动器，然后在进行更改后重新装载它。


## <a name="security"></a>安全

设置并配置 Azure 环境后，下一步是保护网络的安全。 以下是一些建议：

- NSG 策略：可使用子网或 NIC 定义 NSG。 对于诸如应用程序防火墙等内容，可更轻松地控制子网级别的访问，确保安全性和强制路由。

- Jumpbox：为了提高访问的安全性，管理员不应直接连接到应用程序服务或数据库。 使用 Jumpbox 作为管理员计算机与 Azure 资源之间的媒介。
![“Jumpbox 拓扑”页屏幕截图](./media/oracle-design/jumpbox.png)

    应将管理员计算机的 IP 限制为仅允许访问 jumpbox。 Jumpbox 应有权访问应用程序和数据库。

- 专用网络（子网）：建议将应用程序服务和数据库置于单独的子网上，以便通过 NSG 策略可以更好地进行控制。


## <a name="additional-reading"></a>其他阅读材料

- [配置 Oracle ASM](configure-oracle-asm.md)
- [配置 Oracle Data Guard](configure-oracle-dataguard.md)
- [配置 Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Oracle 备份和恢复](oracle-backup-recovery.md)

## <a name="next-steps"></a>后续步骤

- [教程：创建具有高可用性的 VM](../../linux/create-cli-complete.md)
- [浏览 VM 部署 Azure CLI 示例](../../linux/cli-samples.md)
