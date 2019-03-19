---
title: 使用 Azure Site Recovery 执行 VMware 到 Azure 灾难恢复的体系结构 | Microsoft Docs
description: 本文概述了使用 Azure Site Recovery 设置本地 VMware VM 到 Azure 的灾难恢复时使用的组件和体系结构
author: mayurigupta13
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 3/3/2019
ms.author: mayg
ms.openlocfilehash: 5f9ead8df3d0dfb2695def46087d2ea994b8b75b
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57340425"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>VMware 到 Azure 的灾难恢复体系结构

本文介绍使用 [Azure Site Recovery](site-recovery-overview.md) 服务在本地 VMware 站点和 Azure 之间部署 VMware 虚拟机 (VM) 的灾难恢复复制、故障转移和恢复时使用的体系结构和过程。


## <a name="architectural-components"></a>体系结构组件

下面的表和图提供了用于将 VMware 灾难恢复到 Azure 的组件的概要视图。

组件 | **要求** | **详细信息**
--- | --- | ---
**Azure** | 一个 Azure 订阅、 缓存、 托管磁盘和 Azure 网络的 Azure 存储帐户。 | 从本地 Vm 复制的数据存储在 Azure 存储。 运行从本地到 Azure 的故障转移时，将使用复制的数据创建 Azure VM。 创建 Azure VM 后，它们将连接到 Azure 虚拟网络。
**配置服务器计算机** | 单个本地计算机。 建议将其作为可通过下载的 OVF 模板部署的 VMware VM 来运行。<br/><br/> 计算机运行所有本地 Site Recovery 组件，包括配置服务器、进程服务器和主目标服务器。 | **配置服务器**：在本地和 Azure 之间协调通信并管理数据复制。<br/><br/> **进程服务器**：默认安装在配置服务器上。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将数据发送到 Azure 存储。 进程服务器还会将 Azure Site Recovery 移动服务安装在要复制的 VM 上，并在本地计算机上执行自动发现。 随着部署扩大，可以另外添加单独的进程服务器来处理更大的复制流量。<br/><br/> **主目标服务器**：默认安装在配置服务器上。 它处理从 Azure 进行故障回复期间产生的复制数据。 对于大型部署，可以另外添加一个单独的主目标服务器用于故障回复。
**VMware 服务器** | VMware VM 在本地 vSphere ESXi 服务器上托管。 我们建议使用 vCenter 服务器管理主机。 | 在 Site Recovery 部署期间，将 VMware 服务器添加到恢复服务保管库。
复制的计算机 | 移动服务将安装在复制的每个 VMware VM 上。 | 建议允许从进程服务器自动安装。 另外，也可以手动安装此服务，或者使用诸如 System Center Configuration Manager 的自动部署方法。

**VMware 到 Azure 体系结构**

![组件](./media/vmware-azure-architecture/arch-enhanced.png)



## <a name="replication-process"></a>复制过程

1. 启用 VM 复制时，对 Azure 存储的初始复制将开始（使用指定复制策略）。 注意以下事项：
    - VMware VM 复制是块级的几近不间断行为，使用 VM 上运行的移动服务代理。
    - 应用任何复制策略设置：
        - **RPO 阈值**。 此设置不影响复制。 它有助于监视。 如果当前 RPO 超过指定的阈值限制，将引发事件并可能发送电子邮件。
        - **恢复点保留期**。 此设置指定在发生中断时想回退的时间长度。 高级存储上的最大保留期为 24 小时。 标准存储上为 72 小时。 
        - **应用一致的快照**。 获取应用一致的快照的频率可能为每 1 到 12 个小时，具体取决于应用需求。 快照是标准的 Azure Blob 快照。 VM 上运行的移动代理在复制流中请求对应此设置的 VSS 快照和作为应用程序一致点的时间点书签。

2. 流量通过 Internet 复制到 Azure 存储公共终结点。 或者，可以结合使用 Azure ExpressRoute 和[公共对等互连](../expressroute/expressroute-circuit-peerings.md#publicpeering)。 不支持通过站点到站点虚拟专用网络 (VPN) 将流量从本地站点复制到 Azure。
3. 完成初始复制后，开始将增量更改复制到 Azure。 对虚拟机的跟踪更改将发送到进程服务器。
4. 通信按如下方式发生：

    - VM 通过 HTTPS 443 入站端口与本地配置服务器通信，进行复制管理。
    - 配置服务器通过 HTTPS 443 出站端口来与 Azure 协调复制。
    - VM 将复制数据发送到 HTTPS 9443 入站端口上的进程服务器（在配置服务器计算机上运行）。 可以修改此端口。
    - 进程服务器接收复制数据、优化和加密数据，然后通过 443 出站端口将其发送到 Azure 存储。




**VMware 到 Azure 的复制过程**

![复制过程](./media/vmware-azure-architecture/v2a-architecture-henry.png)

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

    - 阶段 1：重新保护 Azure VM，以便它们可以从 Azure 复制回本地 VMware VM。
    -  阶段 2：运行到本地站点的故障转移。
    - 阶段 3：在工作负荷进行故障回复后，为本地 VM 重新启用复制。
    
 
**从 Azure 进行 VMware 故障回复**

![故障回复](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>后续步骤

按照[此教程](vmware-azure-tutorial.md)启用 VMware 到 Azure 的复制。
