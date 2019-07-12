---
title: Azure Migrate 体系结构 |Microsoft Docs
description: 概述 Azure Migrate 服务。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: 16fbf8d3523ac2ab36447aa51a93a0bb5a9fad54
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811422"
---
# <a name="azure-migrate-architecture-and-processes"></a>Azure Migrate 体系结构和过程

[Azure Migrate](migrate-overview.md)提供一个中心工具，可帮助你发现、 评估和将应用程序、 基础结构和工作负荷迁移到 Microsoft Azure。 在中心包括 Azure 迁移工具和第三方独立软件供应商 (ISV) 产品/服务。 

 这篇文章概述 Azure Migrate Server 评估和 Azure 迁移服务器迁移的评估和迁移体系结构和进程。

## <a name="assessment-with-azure-migrate-server-assessment"></a>使用 Azure 的评估迁移 Server 评估

Azure Migrate Server 评估可以评估迁移到 Azure 的 VMware Vm 和 HYPER-V 虚拟机。 评估的工作方式如下：

1. **准备用于评估**:在本地，你设置 Azure Migrate 使用轻型 Azure Migrate 设备的本地 VMware VM 或 HYPER-V VM 和注册设备。
2. **发现 Vm**:Azure Migrate 设备运行，以发现本地 Vm。 
    - 不需在已发现 Vm 上安装。
    - VM 元数据包括有关核心、 内存、 磁盘、 磁盘大小和网络适配器的信息。
    - 性能数据包括有关 CPU 和内存使用情况，磁盘 IOPS、 磁盘吞吐量 (MBps) 和网络输出 (MBps) 的信息
- **收集并评估计算机**:发现完成后，在 Azure 门户收集发现 Vm 成组通常包含你想要一起迁移的 Vm。 在组上运行评估。


## <a name="vmware-assessment"></a>VMware 评估 

在关系图汇总了 VMware VM 评估如何使用 Azure Migrate Server 评估工作。

![评估体系结构 VMware](./media/migrate-architecture/sas-architecture-vmware.png)

过程如下：

1. **部署 Azure Migrate 设备**:

    a. 从 Azure 门户下载 (OVA) 模板。

    b. 将它导入 vCenter 服务器计算机，以创建 VM。

    c. 连接到此 VM，配置基本设置，并将其注册到 Azure Migrate。

2. **启动发现**:

    a. 连接到收集器应用程序在启动发现的设备上运行。

    b. 设备持续发送元数据和性能数据从 Vm 到 Azure。

    - 设备始终连接到 Azure Migrate 服务。
    - 捕获在持续发现过程中的环境更改。 例如在发现范围内，添加 Vm 或添加 VM 磁盘或 Nic。

3. **评估计算机**:

    a. 发现完成后，在 Azure 门户你收集有关发现的 Vm 分组。  一组通常包含你想要一起迁移的 Vm。

    b. 运行每个组的评估。

4. **查看评估**: 

    a. 评估完成后，请在门户中查看它。

    b. 以做进一步分析，下载 Excel 格式的评估。



### <a name="vmware-ports"></a>VMware 端口
对于 VMware，azure Migrate 使用以下连接端口：

**源** | **目标** | 端口  | **详细信息**
--- | --- | --- | ---
Azure Migrate 设备 | Azure Migrate 服务 | Target-TCP 443 | 将元数据和性能数据发送到 Azure Migrate。
Azure Migrate 设备 | vCenter Server | Target-TCP 443 | 连接到 vCenter 服务器的元数据和性能数据。 443 是默认值，但可以通过 vCenter 侦听不同端口上进行修改。 
RDP 客户端 | Azure Migrate 设备 | Target-TCP3389 | 从设备与触发器发现 RDP 连接。

### <a name="collected-vmware-metadata"></a>收集的 VMware 元数据

设备将从 Vm 的元数据和与性能相关的数据发送到 Azure。

**Action** | **详细信息**
--- | ---
**收集的元数据** | vCenter VM 名称<br/> vCenter 的 VM 路径 （主机/群集文件夹）<br/> IP 和 MAC 地址<br/> 操作系统<br/> 核心/磁盘/Nic 数<br/> 内存和磁盘大小。
**收集的性能数据** | CPU/内存使用情况<br/> 每个磁盘数据 （磁盘读取/写入吞吐量; 每秒磁盘读取/写入）<br/> NIC 的数据 （网络出网络中）。<br/><br/> 设备在连接到 vCenter 服务器后不断收集性能数据。 不会收集历史数据。

## <a name="hyper-v-assessment"></a>HYPER-V 评估

关系图概述了如何 HYPER-V 评估工作发挥最大功效 Azure Migrate Server 评估。

![评估体系结构的 HYPER-V](./media/migrate-architecture/sas-architecture-hyper-v.png)

过程如下：

1. **创建 Azure Migrate 设备**:

    a. 从 Azure 门户下载的压缩格式中的 VM。

    b. 将它导入的 HYPER-V 主机。

    c. 连接到设备的 VM。 配置基本设置，并将其注册到 Azure Migrate。

2. **启动发现**:

    a. 连接到 Azure Migrate 设备启动发现。 不需在已发现 Vm 上安装。

    b. 设备不断将 VM 元数据和性能数据发送到 Azure Migrate。

    - 设备始终连接到 Azure Migrate 服务 （使用 CIM 会话）。
    - 捕获在持续发现过程中的环境更改。 例如在发现范围内，添加 Vm 或添加 VM 磁盘或 Nic。


3. **评估计算机**:

    a. 发现完成后，在 Azure 门户你收集有关发现的 Vm 分组。  一组通常包含你想要一起迁移的 Vm。

    b. 运行每个组的评估。

4. **查看评估**:

    a. 评估完成后，请在门户中查看它。

    b. 以做进一步分析，下载 Excel 格式的评估。

### <a name="hyper-v-ports"></a>HYPER-V 端口

Azure Migrate 服务为 Hyper-v： 使用以下连接端口

**源** | **目标** | 端口  | **详细信息**
--- | --- | --- | ---
Azure Migrate 设备 | Azure Migrate 服务 | Target-TCP 443 | 将元数据和性能数据发送到 Azure Migrate。
Azure Migrate 设备 | HYPER-V 主机/群集 | Target-Default WinRM ports-HTTP:5985; HTTPS:5986 | 连接到主机的元数据和性能数据。 用于连接的 CIM 会话
RDP 客户端 | Azure Migrate 设备 | Target-TCP3389 | 从设备与触发器发现 RDP 连接。

## <a name="collected-hyper-v-vm-metadata"></a>收集的 HYPER-V VM 元数据

设备将从 Vm 的元数据和与性能相关的数据发送到 Azure。


**Action** | **详细信息**
--- | ---
**收集的元数据** | VM 名称<br/> VM 路径 （主机/群集文件夹）<br/> IP 和 MAC 地址<br/> 操作系统<br/> 核心/磁盘/Nic 数<br/> 内存和磁盘大小<br/> 磁盘 IOPS、 磁盘吞吐量 (MBps)、 网络输出 (MBps)
**收集的性能数据** |  CPU/内存使用情况<br/> 每个磁盘数据 （磁盘读取/写入吞吐量; 每秒磁盘读取/写入）<br/> NIC 的数据 （网络出网络中）。<br/><br/> 设备连接到 HYPER-V 主机后不断收集性能数据。 不会收集历史数据。




## <a name="migration-with-azure-migrate-server-migration"></a>使用 Azure 的迁移迁移服务器迁移

使用 Azure 迁移服务器迁移，可以将以下迁移到 Azure:

- 本地 VMware VM
- 在本地 HYPER-V Vm
- 在本地物理服务器
- AWS Windows VM 实例
- GCP Windows VM 实例

迁移过程会稍有不同，具体取决于您要进行迁移。


## <a name="migrate-vmware-vms"></a>将 VMware Vm 迁移

Azure 迁移服务器迁移的迁移的本地 VMware Vm 提供两种方法：

- **无代理复制**:将 Vm 迁移而无需在其上安装任何内容。
- **基于代理的复制**。 复制在 VM 上安装代理。

尽管无代理复制是从部署角度来看更容易，但它当前具有许多限制。 [了解详细信息](server-migrate-overview.md)有关这些限制。
 

### <a name="agent-based-migration"></a>基于代理的迁移

基于代理的 VMware Vm 迁移需要多个要部署的组件，表中进行了总结。

组件  | **要求** | **详细信息**
--- | --- | ---
**配置服务器计算机** | 单个本地 VMware VM 从已下载的 OVF 模板部署。<br/><br/> 计算机运行所有本地 Site Recovery 组件，包括配置服务器和进程服务器。 | **配置服务器**：在本地和 Azure 之间协调通信并管理数据复制。<br/><br/> **进程服务器**：默认安装在配置服务器上。 它接收复制数据;通过缓存、 压缩和加密; 对它进行优化并将其发送到 Azure。 进程服务器还在 Vm 上，安装 Azure Site Recovery 移动服务，并执行本地计算机上的自动发现。
**移动服务** | 必须在复制的每个 VMware VM 上安装移动服务。 | 建议允许从进程服务器自动安装。 另外，也可以手动安装此服务，或者使用诸如 System Center Configuration Manager 的自动部署方法。





### <a name="agent-based-replication-process"></a>基于代理的复制过程

![复制过程](./media/migrate-architecture/v2a-architecture-henry.png)

1. 如果启用了 VM 的复制，开始初始复制到 Azure。 
    - 复制是块级别、 几乎持续不断，使用移动服务代理在 VM 上运行。
    - 应用复制策略设置：
        - **RPO 阈值**。 此设置不影响复制。 它有助于监视。 如果当前 RPO 超过指定的阈值限制，将引发事件并可能发送电子邮件。
        - **恢复点保留期**。 此设置指定在发生中断时想回退的时间长度。 高级存储上的最大保留期为 24 小时。 标准存储上为 72 小时。 
        - **应用一致的快照**。 获取应用一致的快照的频率可能为每 1 到 12 个小时，具体取决于应用需求。 快照是标准的 Azure Blob 快照。 VM 上运行的移动代理在复制流中请求对应此设置的 VSS 快照和作为应用程序一致点的时间点书签。

2. 流量通过 Internet 复制到 Azure 存储公共终结点。

    - 或者，可以使用与 Azure ExpressRoute [Microsoft 对等互连](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)。
    - 不支持通过站点到站点虚拟专用网络 (VPN) 将流量从本地站点复制到 Azure。
3. 完成初始复制后，开始将增量更改复制到 Azure。 对虚拟机的跟踪更改将发送到进程服务器。
2. 通信按如下方式发生：

    - VM 通过 HTTPS 443 入站端口与本地配置服务器通信，进行复制管理。
    - 配置服务器通过 HTTPS 443 出站端口来与 Azure 协调复制。
    - VM 将复制数据发送到 HTTPS 9443 入站端口上的进程服务器（在配置服务器计算机上运行）。 可以修改此端口。
    - 进程服务器接收复制数据、优化和加密数据，然后通过 443 出站端口将其发送到 Azure 存储。
5. 复制数据首先登陆 Azure 中的缓存存储帐户。 这些日志进行处理和数据存储在 Azure 中托管的磁盘 （Azure Site Recovery 种子磁盘）。 将在此磁盘上创建恢复点。







## <a name="next-steps"></a>后续步骤

- [查看常见问题解答](resources-faq.md)（关于 Azure Migrate）。
- 有关从社区的帮助，请访问[Azure 迁移 MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureMigrate&filter=alltypes&sort=lastpostdesc)或[Stack Overflow](https://stackoverflow.com/search?q=azure+migrate)。

