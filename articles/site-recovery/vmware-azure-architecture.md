---
title: 使用 Azure Site Recovery 执行 VMware VM 灾难恢复的体系结构
description: 本文概述了使用 Azure Site Recovery 设置本地 VMware VM 到 Azure 的灾难恢复时使用的组件和体系结构
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 45baee286fede0ab16da62b7c2e84008d58690b1
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91626490"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>VMware 到 Azure 的灾难恢复体系结构

本文介绍使用 [Azure Site Recovery](site-recovery-overview.md) 服务在本地 VMware 站点和 Azure 之间部署 VMware 虚拟机 (VM) 的灾难恢复复制、故障转移和恢复时使用的体系结构和过程。


## <a name="architectural-components"></a>体系结构组件

下面的表和图提供了用于将 VMware 灾难恢复到 Azure 的组件的概要视图。

**组件** | **要求** | **详细信息**
--- | --- | ---
**Azure** | Azure 订阅、用于缓存的 Azure 存储帐户、托管磁盘和 Azure 网络。 | 从本地 VM 复制的数据存储在 Azure 存储中。 运行从本地到 Azure 的故障转移时，将使用复制的数据创建 Azure VM。 创建 Azure VM 后，它们将连接到 Azure 虚拟网络。
**配置服务器计算机** | 单个本地计算机。 建议将其作为可通过下载的 OVF 模板部署的 VMware VM 来运行。<br/><br/> 计算机运行所有本地 Site Recovery 组件，包括配置服务器、进程服务器和主目标服务器。 | **配置服务器**：在本地和 Azure 之间协调通信并管理数据复制。<br/><br/> **进程服务器**：默认安装在配置服务器上。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将数据发送到 Azure 存储。 进程服务器还会将 Azure Site Recovery 移动服务安装在要复制的 VM 上，并在本地计算机上执行自动发现。 随着部署扩大，可以另外添加单独的进程服务器来处理更大的复制流量。<br/><br/> **主目标服务器**：默认安装在配置服务器上。 它处理从 Azure 进行故障回复期间产生的复制数据。 对于大型部署，可以另外添加一个单独的主目标服务器用于故障回复。
**VMware 服务器** | VMware VM 在本地 vSphere ESXi 服务器上托管。 我们建议使用 vCenter 服务器管理主机。 | 在 Site Recovery 部署期间，将 VMware 服务器添加到恢复服务保管库。
**复制的计算机** | 移动服务将安装在复制的每个 VMware VM 上。 | 建议允许从进程服务器自动安装。 也可以手动安装此服务，或者使用 Configuration Manager 等自动部署方法。

![此图显示 VMware 到 Azure 的复制体系结构关系。](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="set-up-outbound-network-connectivity"></a>设置出站网络连接

若要使 Site Recovery 按预期工作，需修改出站网络连接以允许环境复制。

> [!NOTE]
> Site Recovery 不支持使用身份验证代理来控制网络连接。

### <a name="outbound-connectivity-for-urls"></a>URL 的出站连接

如果使用基于 URL 的防火墙代理来控制出站连接，请允许访问以下 URL：

| **名称**                  | 商用                               | 政府                                 | **说明** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| 存储                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | 允许将数据从 VM 写入源区域中的缓存存储帐户。 |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | 向 Site Recovery 服务 URL 提供授权和身份验证。 |
| 复制               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | 允许 VM 与 Site Recovery 服务进行通信。 |
| 服务总线               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | 允许 VM 写入 Site Recovery 监视和诊断数据。 |

若要针对本地 Azure Site Recovery 基础结构和 Azure 服务之间的通信进行筛选的完整 Url 列表，请参阅 ["先决条件" 一文中的 "网络要求" 部分](vmware-azure-deploy-configuration-server.md#prerequisites)。

## <a name="replication-process"></a>复制过程

1. 为某台 VM 启用复制时，将使用指定的复制策略开始到 Azure 存储的初始复制。 注意以下事项：
    - 对于 VMware VM，复制是在块级别进行的，几乎连续进行，使用的是在 VM 上运行的移动服务代理。
    - 应用的任何复制策略设置：
        - **RPO 阈值**。 此设置不影响复制。 它有助于监视。 如果当前 RPO 超出了你指定的阈值限制，则会引发事件，并且还可能会发送电子邮件。
        - **恢复点保留期**。 此设置指定当发生中断时要回退的时间距离。 高级存储上的最大保留期为 24 小时。 标准存储上的最大保留期为 72 小时。 
        - **应用一致的快照**。 可以每隔 1 到 12 个小时获取一次应用一致的快照，具体取决于应用需求。 快照是标准的 Azure blob 快照。 在 VM 上运行的移动代理根据此设置请求 VSS 快照，并且会将该时间点标记为复制流中的一个应用一致点。

2. 流量通过 Internet 复制到 Azure 存储公共终结点。 或者，可以将 Azure ExpressRoute 用于 [Microsoft 对等互连](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)。 不支持通过站点到站点虚拟专用网络 (VPN) 将流量从本地站点复制到 Azure。
3. 初始复制操作可确保在启用复制时将计算机上的所有数据发送到 Azure。 完成初始复制后，开始将增量更改复制到 Azure。 针对机器跟踪的更改将发送到进程服务器。
4. 通信按如下方式发生：

    - VM 通过 HTTPS 443 入站端口与本地配置服务器通信，进行复制管理。
    - 配置服务器通过 HTTPS 443 出站端口来与 Azure 协调复制。
    - VM 将复制数据发送到 HTTPS 9443 入站端口上的进程服务器（在配置服务器计算机上运行）。 可以修改此端口。
    - 进程服务器接收复制数据、优化和加密数据，然后通过 443 出站端口将其发送到 Azure 存储。
5. 复制数据首先登陆 Azure 中的缓存存储帐户。 处理这些日志，并将数据存储在 Azure 托管磁盘（称为 asr 种子磁盘）中。 将在此磁盘上创建恢复点。

![此图显示 VMware 到 Azure 的复制过程。](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>重新同步过程

1. 有时，在初始复制或传输增量更改期间，源计算机到进程服务器之间或进程服务器到 Azure 之间可能存在网络连接问题。 这两种情况都可能导致暂时无法将数据传输到 Azure。
2. 为避免出现数据完整性问题，并最大限度地降低数据传输成本，Site Recovery 会标记计算机以进行重新同步。
3. 在以下情况下也可能标记计算机以进行重新同步，目的是保持源计算机和存储在 Azure 中的数据之间的一致性
    - 如果计算机强制关机
    - 如果计算机出现了诸如磁盘大小调整之类的配置更改（将磁盘大小从 2 TB 修改为 4 TB）
4. 重新同步仅向 Azure 发送增量数据。 通过计算源计算机和 Azure 中存储的数据之间的数据校验和来最小化本地和 Azure 之间的数据传输。
5. 默认情况下，重新同步安排为在非工作时间自动运行。 如果你不希望等待默认非工作时间的重新同步，可手动重新同步 VM。 为此，请转到 Azure 门户，选择“VM”>“重新同步”。
6. 如果默认重新同步在办公时间之外失败，并且需要手动干预，则会在 Azure 门户中的特定计算机上生成错误。 可以解决错误并手动触发重新同步。
7. 重新同步完成后，将继续复制增量更改。

## <a name="replication-policy"></a>复制策略 

启用 Azure VM 复制时，Site Recovery 默认会使用下表中汇总的默认设置创建新的复制策略。

**策略设置** | **详细信息** | **默认**
--- | --- | ---
**恢复点保留期** | 指定 Site Recovery 保留恢复点的时间长短 | 24 小时
**应用一致性快照频率** | Site Recovery 创建应用一致性快照的频率。 | 每 4 小时

### <a name="managing-replication-policies"></a>管理复制策略

可按如下所述管理和修改默认的复制策略设置：
- 启用复制时可以修改设置。
- 随时可以创建复制策略，并在启用复制时应用该策略。

### <a name="multi-vm-consistency"></a>多 VM 一致性

如果希望 VM 一同复制，并在故障转移时获得共享的崩溃一致性和应用一致性恢复点，可将这些 VM 集中到一个复制组中。 多 VM 一致性会影响工作负荷的性能。仅当 VM 运行的工作负荷需要在所有计算机之间保持一致时，才应该对这些 VM 使用此功能。 



## <a name="snapshots-and-recovery-points"></a>快照和恢复点

恢复点是基于在特定时间点生成的 VM 磁盘快照创建的。 故障转移 VM 时，可以使用恢复点来还原目标位置中的 VM。

故障转移时，我们通常想要确保 VM 在不发生任何数据损坏或丢失的情况下启动，并且 VM 数据可在操作系统以及 VM 上运行的应用中保持一致。 这取决于创建的快照类型。

Site Recovery 按如下所述创建快照：

1. 默认情况下，Site Recovery 创建崩溃一致性数据快照；如果指定了频率，则创建应用一致性快照。
2. 恢复点是基于快照创建的，根据复制策略中的保留期设置进行存储。

### <a name="consistency"></a>一致性

下表解释了不同的一致性类型。

### <a name="crash-consistent"></a>崩溃一致性

**说明** | **详细信息** | 建议
--- | --- | ---
崩溃一致性快照捕获创建快照时磁盘上的数据。 它不包括内存中的任何数据。<br/><br/> 崩溃一致性快照包含在 VM 发生崩溃或者在创建快照的那一刻从服务器上拔下电源线时，磁盘上的等量数据。<br/><br/> 崩溃一致性不能保证操作系统或 VM 上的应用中的数据一致性。 | 默认情况下，Site Recovery 每隔五分钟创建崩溃一致性恢复点。 此设置不可修改。<br/><br/>  | 目前，大多数应用都可以从崩溃一致性恢复点正常恢复。<br/><br/> 对于操作系统以及 DHCP 服务器和打印服务器等应用而言，崩溃一致性恢复点通常已足够。

### <a name="app-consistent"></a>应用一致性

**说明** | **详细信息** | 建议
--- | --- | ---
应用一致性恢复点是基于应用一致性快照创建的。<br/><br/> 应用一致性快照包含崩溃一致性快照中的所有信息，此外加上内存中的数据，以及正在进行的事务中的数据。 | 应用一致性快照使用卷影复制服务 (VSS)：<br/><br/>   1) Azure Site Recovery 使用 "仅复制备份" (VSS_BT_COPY 不更改 Microsoft SQL 事务日志备份时间和序列号的) 方法 </br></br> 2) 启动快照时，VSS 在卷上执行写入时复制 (牛) 操作。<br/><br/>   3) 在执行牛之前，VSS 会通知计算机上的每个应用程序需要将内存驻留数据刷新到磁盘。<br/><br/>   4) VSS 之后，在这种情况下，备份/灾难恢复应用程序 (Site Recovery) 读取快照数据并继续操作。 | 应用一致性快照是按指定的频率创建的。 此频率始终应小于为保留恢复点设置的频率。 例如，如果使用默认设置 24 小时保留恢复点，则应将频率设置为小于 24 小时。<br/><br/>应用一致性快照比崩溃一致性快照更复杂，且完成时间更长。<br/><br/> 应用一致性快照会影响已启用复制的 VM 上运行的应用的性能。 

## <a name="failover-and-failback-process"></a>故障转移和故障回复过程

在设置复制并运行故障恢复演练（测试故障转移）来检查是否一切都按预期工作后，可以根据需要运行故障转移和故障回复。

1. 可以针对一台计算机运行故障转移，或创建恢复计划来同时故障转移多个 VM。 相比单计算机故障转移，恢复计划的优势包括：
    - 可以通过在单个恢复计划中包含整个应用的所有 VM，为应用依赖关系建模。
    - 可以添加脚本、Azure Runbook 和暂停手动操作。
2. 触发初始故障转移之后，可提交它来开始访问 Azure VM 中的工作负荷。
3. 当本地主站点再次可用时，可以准备故障回复。 若要故障回复，需要设置故障回复基础结构，包括：

    * **Azure 中的临时进程服务器**：若要从 Azure 进行故障回复，需要设置用作进程服务器的 Azure VM，以处理从 Azure 进行的复制。 故障回复完成后，可以删除此 VM。
    * **VPN 连接**：若要进行故障回复，需要设置从 Azure 网络到本地站点的 VPN 连接（或 ExpressRoute）。
    * **单独的主目标服务器**：默认情况下，在本地 VMware VM 上与配置服务器一起安装的主目标服务器用于处理故障回复。 如果对大量流量进行故障回复，应创建专用于此目的的独立本地主目标服务器。
    * **故障回复策略**：若要复制回到本地站点，需要创建故障回复策略。 此策略是在创建从本地到 Azure 的复制策略时自动创建的。
4. 所有组件均就位后，故障回复通过三个操作进行：

    - 第 1 阶段：重新保护 Azure VM，以便它们可以从 Azure 复制回本地 VMware VM。
    -  第 2 阶段：运行到本地站点的故障转移。
    - 第 3 阶段：在工作负荷进行故障回复后，为本地 VM 重新启用复制。
    
 

![此图显示从 Azure 进行 VMware 故障回复。](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>后续步骤

根据[此教程](vmware-azure-tutorial.md)启用 VMware 到 Azure 复制。
