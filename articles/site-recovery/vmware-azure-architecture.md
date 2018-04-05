---
title: 使用 Azure Site Recovery 执行 VMware 到 Azure 的复制的体系结构 | Microsoft Docs
description: 本文概述了使用 Azure Site Recovery 将本地 VMware VM 复制到 Azure 时使用的组件和体系结构
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/19/2018
ms.author: raynew
ms.openlocfilehash: c1aa89f14edab7d0e560c20d6bc48480aff1631f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="vmware-to-azure-replication-architecture"></a>VMware 到 Azure 复制体系结构

本文介绍了使用 [Azure Site Recovery](site-recovery-overview.md) 在本地 VMware 站点与 Azure 之间对 VMware 虚拟机 (VM) 进行复制、故障转移和恢复时使用的体系结构和过程。


## <a name="architectural-components"></a>体系结构组件

下面的表和图提供了用于将 VMware 复制到 Azure 的组件的概要视图。

**组件** | **要求** | **详细信息**
--- | --- | ---
**Azure** | Azure 订阅、Azure 存储帐户和 Azure 网络。 | 从本地 VM 复制的数据存储在存储帐户中。 运行从本地到 Azure 的故障转移时，将使用复制的数据创建 Azure VM。 创建 Azure VM 后，它们将连接到 Azure 虚拟网络。
**配置服务器计算机** | 单个本地计算机。 建议将其作为可通过下载的 OVF 模板部署的 VMware VM 来运行。<br/><br/> 计算机运行所有本地 Site Recovery 组件，包括配置服务器、进程服务器和主目标服务器。 | **配置服务器：**在本地和 Azure 之间协调通信并管理数据复制。<br/><br/> **进程服务器**：默认情况下安装在配置服务器上。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将数据发送到 Azure 存储。 进程服务器还会将 Azure Site Recovery 移动服务安装在要复制的 VM 上，并在本地计算机上执行自动发现。 随着部署扩大，可以另外添加单独的进程服务器来处理更大的复制流量。<br/><br/> **主目标服务器**：默认情况下安装在配置服务器上。 它处理从 Azure 进行故障回复期间产生的复制数据。 对于大型部署，可以另外添加一个单独的主目标服务器用于故障回复。
**VMware 服务器** | VMware VM 在本地 vSphere ESXi 服务器上托管。 我们建议使用 vCenter 服务器管理主机。 | 在 Site Recovery 部署期间，将 VMware 服务器添加到恢复服务保管库。
**复制的计算机** | 移动服务将安装在复制的每个 VMware VM 上。 | 建议允许从进程服务器自动安装。 另外，也可以手动安装此服务，或者使用诸如 System Center Configuration Manager 的自动部署方法。

**VMware 到 Azure 体系结构**

![组件](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="configuration-steps"></a>配置步骤

设置 VMware 到 Azure 的灾难恢复或迁移的大致步骤如下：

1. **设置 Azure 组件**。 需要一个拥有适当权限的 Azure 帐户、Azure 存储帐户、Azure 虚拟网络和恢复服务保管库。 [了解详细信息](tutorial-prepare-azure.md)。
2. **本地设置**。 这包括：在 VMware 服务器上设置一个帐户，使 Site Recovery 能够自动发现要复制的 VM；设置一个可用于在要复制的 VM 上安装移动服务组件的帐户；验证 VMware 服务器和 VM 是否符合先决条件。 （可选）也可以准备在故障转移后连接到这些 Azure VM。 运行到 Azure 的故障转移时，Site Recovery 会将 VM 数据复制到 Azure 存储帐户，并使用这些数据创建 Azure VM。 [了解详细信息](vmware-azure-tutorial-prepare-on-premises.md)。
3. **设置复制**。 选择要复制到的位置。 通过设置单个本地 VMware VM（配置服务器）来配置源复制环境，该 VM 运行所需的所有本地 Site Recovery 组件。 设置后，将配置服务器注册到恢复服务保管库中。 然后，选择目标设置。 [了解详细信息](vmware-azure-tutorial.md)。
4. **创建复制策略**。 创建一个复制策略用于指定复制的发生方式。 
    - **RPO 阈值**：此监视设置指明如果在指定的时间内未发生复制，则发出警报（和可选的电子邮件）。 例如，如果 RPO 阈值设置为 30 分钟，而某个问题导致 30 分钟内未发生复制，则会生成一个事件。 此设置不影响复制。 复制将持续进行，并且每隔几分钟会创建恢复点
    - **保留期**：恢复点保留期指定应在 Azure 中保留恢复点多长时间。 对于高级存储，可以指定介于 0 和 24 小时之间的值；对于标准存储，最长可以指定 72 小时。 如果设置大于零的值，则可以故障转移到最新恢复点或存储的某个恢复点。 保留期限过后，将清除恢复点。
    - **崩溃一致的快照**：默认情况下，Site Recovery 每隔几分钟就会创建崩溃一致的快照，并使用这些快照创建恢复点。 如果所有相关数据组件的写入顺序一致（如同它们是在创建恢复点的那一刻创建的），则恢复点就是崩溃一致的。 为便于理解，请想像一下发生断电或类似事件后，电脑硬盘上的数据状态。 如果应用程序设计为从崩溃恢复时保持数据一致，则崩溃一致的恢复点足以满足需求。
    - **应用一致的快照**：如果此值不为零，则 VM 上运行的移动服务会尝试生成文件系统一致的快照和恢复点。 完成初始复制后，将创建第一个快照。 然后，按指定的频率创建快照。 如果正在运行的应用程序除了写入顺序一致，还完成了所有操作，并已将其缓存区刷新到磁盘（应用程序静止），则恢复点是应用程序一致的。 建议对 SQL、Oracle 和 Exchange 等数据库应用程序使用应用一致的恢复点。 如果崩溃一致的快照足以满足需求，则此值可设置为 0。  
    - **多 VM 一致性**：可以选择性地创建复制组。 然后，在启用复制时，可将 VM 收集到该组中。 复制组中的 VM 将一起复制，并在故障转移时共享崩溃一致和应用一致的恢复点。 应慎重使用此选项，因为这需要跨多个计算机收集快照，因此可能会影响工作负荷的性能。 仅当 VM 运行相同的工作负荷且需要保持一致，而 VM 的数据变动率类似时，才执行此操作。 最多可将 8 个 VM 添加到一个组。 
5. **启用 VM 复制**。 最后，为本地 VMware VM 启用复制。 如果创建了一个帐户用于安装移动服务，并指定 Site Recovery 应执行推送安装，则会在启用复制的每个 VM 上安装移动服务。 [了解详细信息](vmware-azure-tutorial.md#enable-replication)。 如果出于 VM 一致性目的创建了复制组，可将 VM 添加到该组。
6. **测试故障转移**。 完成所有设置后，可以执行测试故障转移，检查 VM 是否按预期方式故障转移到 Azure。 [了解详细信息](tutorial-dr-drill-azure.md)。
7. **故障转移**。 如果只需将 VM 迁移到 Azure，可以运行完整故障转移。 若要设置灾难恢复，可以根据需要运行完整故障转移。 对于完整灾难恢复，在故障转移到 Azure 之后，可以故障回复到本地站点（如果可用）。 [了解详细信息](vmware-azure-tutorial-failover-failback.md)。

## <a name="replication-process"></a>复制过程

1. 为 VM 启用复制后，将会根据复制策略开始复制。 
2. 流量通过 Internet 复制到 Azure 存储公共终结点。 或者，可以结合使用 Azure ExpressRoute 和[公共对等互连](../expressroute/expressroute-circuit-peerings.md#azure-public-peering)。 不支持通过站点到站点虚拟专用网络 (VPN) 将流量从本地站点复制到 Azure。
3. VM 数据的初始副本将复制到 Azure 存储。
4. 完成初始复制后，开始将增量更改复制到 Azure。 计算机的受跟踪更改保存在 .hrl 文件中。
5. 通信按如下方式发生：

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

    - 第 1 阶段：重新保护 Azure VM，以便它们可以从 Azure 复制回本地 VMware VM。
    -  第 2 阶段：运行到本地站点的故障转移。
    - 第 3 阶段：在工作负荷进行故障回复后，为本地 VM 重新启用复制。
    
 
**从 Azure 进行 VMware 故障回复**

![故障回复](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>后续步骤

按照[此教程](vmware-azure-tutorial.md)启用 VMware 到 Azure 的复制。
