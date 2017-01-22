---
title: "Site Recovery 的工作原理是什么？ | Microsoft Docs"
description: "本文提供站点恢复体系结构的概述"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/02/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: bd8082c46ee36c70e372208d1bd15337acc558a1
ms.openlocfilehash: eb97f66901efa336942dee56d9a8a62ade1f6842


---
# <a name="how-does-azure-site-recovery-work"></a>Azure Site Recovery 的工作原理

阅读本文，了解 Azure Site Recovery 服务的基础体系结构以及它运行时必需的组件。

组织需要制定 BCDR 策略来确定应用、工作负荷和数据如何在计划和非计划停机期间保持运行和可用，并尽快恢复正常运行情况。 BCDR 策略应保持业务数据的安全性和可恢复性，并确保在发生灾难时工作负荷持续可用。

Site Recovery 是一项 Azure 服务，可以通过协调从本地物理服务器和虚拟机到云 (Azure) 或辅助数据中心的的复制，来为 BCDR 策略提供辅助。 当主要位置发生故障时，你可以故障转移到辅助位置，使应用和工作负荷保持可用。 当主要位置恢复正常时，你可以故障转移回到主要位置。 有关详细信息，请参阅 [什么是 Site Recovery？](site-recovery-overview.md)

本文介绍如何在 [Azure 门户](https://portal.azure.com)中部署。 使用 [Azure 经典门户](https://manage.windowsazure.com/)可以维护现有的 Site Recovery 保管库，但无法创建新保管库。

请在本文末尾发表任何看法。 如有技术问题，请在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上提出。


## <a name="deployment-scenarios"></a>部署方案

部署站点恢复可在许多方案中协调复制：

- **复制 VMware 虚拟机**：可将本地 VMware 虚拟机复制到 Azure 或辅助数据中心。
- **复制物理机**：可将物理机（Windows 或 Linux）复制到 Azure 或辅助数据中心。 复制物理机的过程与复制 VMware VM 的过程基本相同。
- **复制 Hyper-V VM**：可将 Hyper-V VM 复制到 Azure 或辅助 VMM 站点。 如果要将这些 VM 复制到辅助站点，它们必须在 System Center Virtual Machine Manager (VMM) 云中管理。
- **迁移 VM**：除了将本地 VM 和物理服务器复制（复制、故障转移和故障回复）到 Azure 以外，还可以将它们迁移到 Azure VM（复制、故障转移，但无法故障回复）。 下面是可以迁移的项：
    - 迁移本地 Hyper-V VM、VMware VM 和物理服务器上运行的工作负荷，使其在 Azure VM 上运行。
    - 在 Azure 区域之间迁移 [Azure IaaS VM](site-recovery-migrate-azure-to-azure.md)。 目前只有此方案才支持迁移，这意味着，故障回复不受支持。
    - 将 [AWS Windows 实例](site-recovery-migrate-aws-to-azure.md)迁移到 Azure IaaS VM。 目前只有此方案才支持迁移，这意味着，故障回复不受支持。

Site Recovery 将复制受支持 VM 和物理服务器上运行的应用。 有关受支持应用的完整摘要，请参阅 [Azure Site Recovery 可以保护哪些工作负荷？](site-recovery-workload.md)

## <a name="replicate-vmware-vmsphysical-servers-to-azure"></a>将 VMware VM/物理服务器复制到 Azure

### <a name="components"></a>组件

**组件** | **详细信息**
--- | ---
**Azure** | 在 Azure 中，需要创建 Microsoft Azure 帐户、Azure 存储帐户和 Azure 网络。<br/><br/> 存储和网络帐户可以是 Resource Manager 帐户或经典帐户。<br/><br/>  复制的数据存储在存储帐户中；从本地站点故障转移时，将使用复制的数据创建 Azure VM。 创建 Azure VM 后，它们将连接到 Azure 虚拟网络。
**配置服务器** | 在本地设置一台配置服务器，用于协调本地站点与 Azure 之间的通信，以及管理数据复制。
**进程服务器** | 默认在本地配置服务器上安装。<br/><br/> 充当复制网关。 此服务器接收受保护源计算机提供的复制数据，通过缓存、压缩和加密对其进行优化，然后将数据发送到 Azure 存储空间。<br/><br/> 处理用于保护计算机的移动服务的推送安装，并执行 VMware VM 的自动发现。<br/><br/> 随着部署扩大，可以添加作为进程服务器运行的独立专用进程服务器，目的仅在于处理更大的复制流量。
**主目标服务器** | 默认在本地配置服务器上安装。<br/><br/> 处理从 Azure 进行故障回复期间产生的复制数据。 如果故障回复流量很大，可以单独部署一台主目标服务器用于故障回复。
**VMware 服务器** | 将 vCenter 和 vSphere 服务器添加到恢复服务保管库，用于复制 VMware VM。<br/><br/> 如果复制物理服务器，需要构建本地 VMware 基础结构用于故障回复。 无法故障回复到物理服务器。
**复制的计算机** | 必须在要复制的每台计算机上安装移动服务。 可在每台计算机上手动安装，或者通过进程服务器进行推送安装。

**图 1：VMware 到 Azure 的组件**

![组件](./media/site-recovery-components/arch-enhanced.png)

### <a name="replication-process"></a>复制过程

1. 设置部署，包括配置 Azure 组件和恢复服务保管库。 在保管库中指定复制源和目标，设置配置服务器，添加 VMware 服务器，创建复制策略，部署移动服务，启用复制，并运行测试故障转移。
2.  计算机根据复制策略开始复制，初始数据副本将复制到 Azure 存储。
4. 完成初始复制后，开始将增量更改复制到 Azure。 计算机的受跟踪更改保存在 .hrl 文件中。
    - 复制计算机与 HTTPS 443 入站端口上的配置服务器通信，进行复制管理。
    - 复制计算机将复制数据发送到 HTTPS 9443 入站端口（可配置）上的进程服务器。
    - 配置服务器通过 HTTPS 443 出站端口来与 Azure 协调复制管理。
    - 进程服务器从源计算机接收数据、优化和加密数据，然后通过 443 出站端口将其发送到 Azure 存储。
    - 如果启用了多 VM 一致性，复制组中的计算机将通过端口 20004 相互通信。 如果将多台计算机分组到复制组，并且这些组在故障转移时共享崩溃一致且应用一致的恢复点，请使用多 VM 方案。 如果计算机运行相同的工作负荷并需要保持一致，这种做法非常有用。
5. 流量通过 Internet 复制到 Azure 存储公共终结点。 或者，可以使用 Azure ExpressRoute [公共对等互连](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-circuit-peerings#public-peering)。 不支持通过站点到站点 VPN 将流量从本地站点复制到 Azure。

**图 2：VMware 到 Azure 的复制**

![增强版](./media/site-recovery-components/v2a-architecture-henry.png)

### <a name="failover-and-failback-process"></a>故障转移和故障回复过程

1. 运行从本地 VMware VM 和物理服务器到 Azure 的计划外故障转移。 不支持计划内故障转移。
2. 可以故障转移单台计算机，或创建[恢复计划](site-recovery-create-recovery-plans.md)来协调多台计算机的故障转移。
3. 运行故障转移时，将在 Azure 中创建副本 VM。 提交故障转移，开始从副本 Azure VM 访问工作负荷。
4. 当本地主站点再次可用时，便可以故障回复。 设置故障回复基础结构，开始将计算机从辅助站点复制到主站点，然后从辅助站点运行计划外故障转移。 提交此故障转移后，数据将回到本地，此时需要再次启用目标为 Azure 的复制。 [了解详细信息](site-recovery-failback-azure-to-vmware.md)

故障回复有以下几项要求：

- **不支持物理到物理故障回复**：这意味着，如果将物理服务器故障转移到 Azure ，然后想要故障回复，必须故障回复到 VMware VM。 不能故障回复到物理服务器。 需要要故障回复到的 Azure VM，如果未将配置服务器部署为 VMware VM，则需要将单独的主目标服务器设置为 VMware VM。 需要此项，因为主目标服务器进行交互，并连接到 VMware 存储，将磁盘还原到 VMware VM。
- **Azure 中的临时进程服务器**：如果要在故障转移后从 Azure 故障回复，必须将 Azure VM 设置为进程服务器，以处理来自 Azure 的复制。 故障回复完成后，可以删除此 VM。
- **VPN 连接**：若要进行故障回复，需要设置从 Azure 网络到本地站点的 VPN 连接（或 Azure ExpressRoute）。
- **单独的本地主目标服务器**：本地主目标服务器用于处理故障回复。 主目标服务器默认安装在管理服务器上，但如果要故障回复大量数据，应该设置独立的本地主目标服务器。
- **故障回复策略**：若要复制回到本地站点，需要创建故障回复策略。 创建复制策略已自动创建故障回复策略。

**图 3：VMware/物理机故障回复**

![故障回复](./media/site-recovery-components/enhanced-failback.png)


## <a name="replicate-vmware-vmsphysical-servers-to-a-secondary-site"></a>将 VMware VM/物理服务器复制到辅助站点

### <a name="components"></a>组件

**组件** | **详细信息**
--- | ---
**Azure** | 使用 InMage Scout 部署此方案。 若要获取它，需要 Azure 订阅。<br/><br/> 创建恢复服务保管库后，可以下载 InMage Scout 并安装最新的更新，以设置部署。
**进程服务器** | 在主站点中部署进程服务器组件，用于处理缓存、压缩和数据优化。<br/><br/> 它还可以将安装的统一代理推送到你要保护的计算机。
**VMware ESX/ESXi 和 vCenter 服务器** |  需要使用 VMware 基础结构来复制 VMware VM。
**VM/物理服务器** |  在想要保护的 VMware VM 或 Windows/Linux 物理服务器上安装统一代理。<br/><br/> 该代理充当所有组件之间的通信提供程序。
**配置服务器** | 在辅助站点中安装配置服务器，以使用管理网站或 vContinuum 控制台来管理、配置和监视部署。
**vContinuum 服务器** | 安装在配置服务器所在的同一位置。<br/><br/> 它提供一个控制台用于管理和监视受保护的环境。
**主目标服务器（辅助站点）** | 主目标服务器保存复制的数据。 它从进程服务器接收数据，在辅助站点中创建副本地器，并保存数据保留点。<br/><br/> 需要的主目标服务器数目取决于要保护的计算机数目。<br/><br/> 如果希望故障转移到主站点，则主站点上也需要有一个主目标服务器。 在此服务器上安装统一代理。

### <a name="replication-process"></a>复制过程

1. 在每个站点（配置、进程、主目标）中设置组件服务器，并在要复制的计算机上安装统一代理。
2. 在初始复制之后，每台计算机上的代理会将增量复制更改发送到进程服务器。
3. 进程服务器将优化这些数据，并将其传输到辅助站点上的主目标服务器。 配置服务器将管理复制进程。

**图 4：VMware 到 VMware 的复制**

![VMware 到 VMware](./media/site-recovery-components/vmware-to-vmware.png)



## <a name="replicate-hyper-v-vms-to-azure"></a>将 Hyper-V VM 复制到 Azure


### <a name="components"></a>组件

**组件** | **详细信息**
--- | ---
**Azure** | 在 Azure 中，需要创建 Microsoft Azure 帐户、Azure 存储帐户和 Azure 网络。<br/><br/> 存储和网络帐户可以是基于 Resource Manager 的帐户或经典帐户。<br/><br/> 复制的数据存储在存储帐户中；从本地站点故障转移时，将使用复制的数据创建 Azure VM。<br/><br/> 创建 Azure VM 后，它们将连接到 Azure 虚拟网络。
**VMM 服务器** | 如果 Hyper-V 主机位于 VMM 云中，需要设置逻辑和 VM 网络来配置[网络映射](site-recovery-network-mapping.md)。 VM 网络应链接到与云关联的逻辑网络。
**Hyper-V 主机** | 需要一个或多个 Hyper-V 主机服务器。
**Hyper-V VM** | Hyper-V 主机服务器上需要一个或多个 VM。 Hyper-V 主机上运行的提供程序通过 Internet 协调使用 Site Recovery 服务的复制。 代理通过 HTTPS 443 处理数据复制。 来自提供程序和代理的通信都是安全且经过加密的。 Azure 存储空间中的复制数据也已加密。


## <a name="replication-process"></a>复制过程

1. 设置 Azure 组件。 建议在开始部署 Site Recovery 之前设置存储和网络帐户。
2. 为 Site Recovery 创建复制服务保管库，并配置保管库设置，包括：
    - 如果 Hyper-V 主机不是在 VMM 云中管理，请创建 Hyper-V 站点容器，并将 Hyper-V 主机添加到其中。
    - 复制源和目标。 如果 Hyper-V 主机在 VMM 云中管理，则源是 VMM 云。 否则，源是 Hyper-V 站点。
    - 安装 Azure Site Recovery 提供程序和 Microsoft Azure 恢复服务代理。 如果使用 VMM，将在其上安装提供程序，在每台 Hyper-V 主机上安装代理。 如果未使用 VMM，提供程序和代理将安装在每台主机上。
    - 为 Hyper-V 站点或 VMM 云创建复制策略。 策略将应用到站点或云中主机上的所有 VM。
    - 为 Hyper-V VM 启用复制。 初始复制根据复制策略设置进行。
4. 数据更改将受到跟踪，完成初始复制后，开始将增量更改复制到 Azure。 项的受跟踪更改保存在 .hrl 文件中。
5. 运行测试故障转移，确保一切正常运行。

### <a name="failover-and-failback-process"></a>故障转移和故障回复过程

1. 可以运行从本地 Hyper-V VM 到 Azure 的计划内或计划外[故障转移](site-recovery-failover.md)。 如果运行计划的故障转移，源 VM 将关闭以确保不会丢失数据。
2. 可以故障转移单台计算机，或创建[恢复计划](site-recovery-create-recovery-plans.md)来协调多台计算机的故障转移。
4. 运行故障转移后，应会在 Azure 中看到创建的副本 VM。 如果需要，可向 VM 分配公共 IP 地址。
5. 然后提交故障转移，开始从副本 Azure VM 访问工作负荷。
6. 当本地主站点再次可用时，便可以故障回复。 启动从 Azure 到主站点的计划内故障转移。 运行计划内故障转移时，可以选择故障回复到同一 VM 或备用位置，并同步 Azure 与本地之间的更改，确保不会丢失数据。 在本地创建 VM 时，请提交故障转移。

**图 5：Hyper-V 站点到 Azure 的复制**

![组件](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**图 6：VMM 云中 Hyper-V 到 Azure 的复制**

![组件](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)



## <a name="replicate-hyper-v-vms-to-a-secondary-site"></a>将 Hyper-V VM 复制到辅助站点

### <a name="components"></a>组件

**组件** | **详细信息**
--- | ---
**Azure 帐户** | 需要一个 Microsoft Azure 帐户。
**VMM 服务器** | 建议在主站点和辅助站点中各部署一台与 Internet 连接的 VMM 服务器。<br/><br/> 每台服务器应至少包含一个设置了 Hyper-V 功能配置文件的 VMM 私有云。<br/><br/> 在 VMM 服务器上安装 Azure Site Recovery 提供程序。 提供程序通过 Internet 使用 Site Recovery 服务协调和安排复制。 提供程序与 Azure 之间的通信是安全的且经过加密的。
**Hyper-V 服务器** |  主要和辅助 VMM 云中需要一台或多台 Hyper-V 主机服务器。 服务器应已连接到 Internet。<br/><br/> 使用 Kerberos 或证书身份验证通过 LAN 或 VPN 在主要和辅助 Hyper-V 主机服务器之间复制数据。  
**源计算机** | 源 Hyper-V 主机服务器应该至少有一个要复制的 VM。

## <a name="replication-process"></a>复制过程

1. 设置 Azure 帐户。
2. 为 Site Recovery 创建复制服务保管库，并配置保管库设置，包括：

    - 复制源和目标（主站点和辅助站点）。
    - 安装 Azure Site Recovery 提供程序和 Microsoft Azure 恢复服务代理。 在 VMM 服务器上安装提供程序，在每台 Hyper-V 主机上安装代理。
    - 为源 VMM 云创建复制策略。 策略将应用到云中主机上的所有 VM。
    - 为 Hyper-V VM 启用复制。 初始复制根据复制策略设置进行。
4. 数据更改将受到跟踪，完成初始复制后，开始复制增量更改。 项的受跟踪更改保存在 .hrl 文件中。
5. 运行测试故障转移，确保一切正常运行。

**图 7：VMM 到 VMM 的复制**

![本地到本地](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback-process"></a>故障转移和故障回复过程

1. 可以在本地站点之间运行计划内或计划外[故障转移](site-recovery-failover.md)。 如果运行计划的故障转移，源 VM 将关闭以确保不会丢失数据。
2. 可以故障转移单台计算机，或创建[恢复计划](site-recovery-create-recovery-plans.md)来协调多台计算机的故障转移。
4. 如果执行了到辅助站点的计划外故障转移，在故障转移后，将不会为辅助位置中的计算机启用保护或复制。 如果执行了计划内故障转移，在故障转移后，辅助位置中的计算机将受保护。
5. 然后提交故障转移，开始从副本 VM 访问工作负荷。
6. 当主站点再次可用时，启动从辅助站点到主站点的反向复制。 反向复制会使虚拟机进入受保护状态，但辅助数据中心仍是活动位置。
7. 若要使主站点再次成为活动位置，可以启动从辅助站点到主站点的计划内故障转移，然后再次启动反向复制。


### <a name="hyper-v-replication-workflow"></a>Hyper-V 复制工作流

**工作流阶段** | **操作**
--- | ---
1.**启用保护** | 为 Hyper-V VM 启用保护后，将启动“启用保护”作业，检查计算机是否符合先决条件。 该作业将调用两个方法：<br/><br/> [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx)：使用已配置的设置来设置复制。<br/><br/> [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx)：初始化完整 VM 复制。
2.**初始复制** |  创建虚拟机快照并逐个复制虚拟硬盘，直到它们已全部复制到辅助位置。<br/><br/> 完成复制所需的时间取决于 VM 大小和网络带宽以及初始复制方法。<br/><br/> 如果在初始复制期间发生磁盘更改，Hyper-V 副本复制跟踪器将跟踪这些更改，并将其记录在 Hyper-V 复制日志 (.hrl) 中，该文件位于与磁盘相同的文件夹中。<br/><br/> 每个磁盘都有一个关联的 .hrl 文件，该文件将发送到辅助存储。<br/><br/> 当初始复制正在进行时，快照和日志将占用磁盘资源。 初始复制完成后，将删除 VM 快照，并且将同步并合并日志中的增量磁盘更改。
3.**完成保护** | 初始复制完成后，“完成保护”作业将配置网络和其他复制后设置，使虚拟机受到保护。<br/><br/> 如果要复制到 Azure，你可能需要调整虚拟机的设置，使其随时可进行故障转移。<br/><br/> 此时，你可以运行测试故障转移以检查一切是否按预期工作。
4.**复制** | 在完成初始复制后，根据复制设置开始增量同步。<br/><br/> **复制失败**：如果增量复制失败且完整复制因为带宽或时间限制而需要大量开销，将会发生重新同步。 例如，如果 .hrl 文件达到磁盘大小的 50%，系统会将 VM 标记为重新同步。 重新同步通过计算源虚拟机磁盘和目标虚拟机的校验和并只发送增量来最大程度地减小发送的数据量。 重新同步完成后，将会恢复增量复制。 默认情况下，重新同步安排为在非工作时间自动运行，但你可以手动重新同步虚拟机。<br/><br/> **复制错误**：如果发生复制错误，将会进行内置重试。 如果是无法恢复的错误，例如身份验证或授权错误，或者副本计算机处于无效状态，则不会重试。 如果是可恢复的错误，例如网络错误，或磁盘空间/内存不足，则会发生重试。重试的间隔将会递增（依次为 1、2、4、8、10、30 分钟）。
5.**计划内/计划外故障转移** | 如果需要，可以运行计划内或计划外故障转移。<br/><br/> 如果运行计划的故障转移，源 VM 将关闭以确保不会丢失数据。<br/><br/> 副本 VM 在创建后处于待提交状态。 需要提交这些操作才能完成故障转移。<br/><br/> 主站点启动并运行后，可以故障回复到主站点（如果可用）。


**图 8：Hyper-V 工作流**

![工作流](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>后续步骤

[准备部署](site-recovery-best-practices.md)



<!--HONumber=Jan17_HO1-->


