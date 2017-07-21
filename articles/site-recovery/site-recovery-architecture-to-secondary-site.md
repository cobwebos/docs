---
title: "如何使用 Azure Site Recovery 将本地计算机复制到辅助本地站点？ | Microsoft Docs"
description: "本文概述通过 Azure Site Recovery 服务将本地 VM 和物理服务器复制到辅助站点所用的组件和体系结构。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/29/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: fca95c63964b955db7ddfbe53250702cc8af122e
ms.contentlocale: zh-cn
ms.lasthandoff: 06/15/2017


---
# <a name="how-does-on-premises-machine-replication-to-a-secondary-site-work-in-site-recovery"></a>如何使用 Site Recovery 将本地计算机复制到辅助站点？

本文介绍使用 [Azure Site Recovery](site-recovery-overview.md) 服务将本地虚拟机和物理服务器复制到 Azure 时涉及的组件和进程。

可以将以下内容复制到辅助本地站点：
- 本地 Hyper-V VM：在 System Center Virtual Machine Manager (VMM) 云中托管的 Hyper-V 群集和独立主机上的 Hyper-V VM。
- 本地 VMware VM 和 Windows/Linux 物理服务器。 在此方案中，由 Scout 管理复制。

请将任何评论发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="replicate-hyper-v-vms-to-a-secondary-on-premises-site"></a>将 Hyper-V VM 复制到辅助本地站点


### <a name="architectural-components"></a>体系结构组件

将 Hyper-V VM 复制到辅助站点时需要提供：

**组件** | **位置** | **详细信息**
--- | --- | ---
**Azure** | 需要一个 Microsoft 帐户。 |
**VMM 服务器** | 我们建议在主站点和辅助站点中各一个 VMM 服务器 | 每个 VMM 服务器都应连接到 Internet。<br/><br/> 每台服务器应至少包含一个设置了 Hyper-V 功能配置文件的 VMM 私有云。<br/><br/> 在 VMM 服务器上安装 Azure Site Recovery 提供程序。 提供程序通过 Internet 使用 Site Recovery 服务协调和安排复制。 提供程序与 Azure 之间的通信是安全的且经过加密的。
**Hyper-V 服务器** |  主要和辅助 VMM 云中需要一台或多台 Hyper-V 主机服务器。<br/><br/> 服务器应已连接到 Internet。<br/><br/> 使用 Kerberos 或证书身份验证通过 LAN 或 VPN 在主要和辅助 Hyper-V 主机服务器之间复制数据。  
**Hyper-V VM** | 位于源 Hyper-V 主机服务器上。 | 源主机服务器应该至少有一个要复制的 VM。

### <a name="replication-process"></a>复制过程

1. 设置 Azure 帐户。
2. 为 Site Recovery 创建复制服务保管库，并配置保管库设置，包括：

    - 复制源和目标（主站点和辅助站点）。
    - 安装 Azure Site Recovery 提供程序和 Microsoft Azure 恢复服务代理。 在 VMM 服务器上安装提供程序，在每台 Hyper-V 主机上安装代理。
    - 为源 VMM 云创建复制策略。 策略将应用到云中主机上的所有 VM。
    - 为 Hyper-V VM 启用复制。 初始复制根据复制策略设置进行。
4. 数据更改将受到跟踪，完成初始复制后，开始复制增量更改。 项的受跟踪更改保存在 .hrl 文件中。
5. 运行测试故障转移，确保一切正常运行。

图 1：VMM 到 VMM 的复制

![本地到本地](./media/site-recovery-components/arch-onprem-onprem.png)

### <a name="failover-and-failback-process"></a>故障转移和故障回复过程

1. 可以在本地站点之间运行计划内或计划外[故障转移](site-recovery-failover.md)。 如果运行计划的故障转移，源 VM 将关闭以确保不会丢失数据。
2. 可以故障转移单台计算机，或创建[恢复计划](site-recovery-create-recovery-plans.md)来协调多台计算机的故障转移。
4. 如果执行了到辅助站点的计划外故障转移，在故障转移后，将不会为辅助位置中的计算机启用保护或复制。 如果执行了计划内故障转移，在故障转移后，辅助位置中的计算机将受保护。
5. 然后提交故障转移，开始从副本 VM 访问工作负荷。
6. 当主站点再次可用时，启动从辅助站点到主站点的反向复制。 反向复制会使虚拟机进入受保护状态，但辅助数据中心仍是活动位置。
7. 若要使主站点再次成为活动位置，可以启动从辅助站点到主站点的计划内故障转移，然后再次启动反向复制。




## <a name="replicate-vmware-vmsphysical-servers-to-a-secondary-site"></a>将 VMware VM/物理服务器复制到辅助站点

使用 InMage Scout 的以下体系结构组件将 VMware VM 或物理服务器复制到辅助站点：


### <a name="architectural-components"></a>体系结构组件

**组件** | **位置** | **详细信息**
--- | --- | ---
**Azure** | InMage Scout。 | 若要获取 InMage Scout，需要 Azure 订阅。<br/><br/> 创建恢复服务保管库后，可以下载 InMage Scout 并安装最新的更新，以设置部署。
**进程服务器** | 位于主站点 | 可以部署进程服务器来处理缓存、压缩和数据优化操作。<br/><br/> 它还可以将安装的统一代理推送到你要保护的计算机。
**配置服务器** | 位于辅助站点 | 配置服务器可以通过管理网站或 vContinuum 控制台管理、配置和监视部署。
**vContinuum 服务器** | 可选。 安装在配置服务器所在的同一位置。 | 它提供一个控制台用于管理和监视受保护的环境。
**主目标服务器** | 位于辅助站点 | 主目标服务器保存复制的数据。 它从进程服务器接收数据，在辅助站点中创建副本地器，并保存数据保留点。<br/><br/> 需要的主目标服务器数目取决于要保护的计算机数目。<br/><br/> 如果希望故障转移到主站点，则主站点上也需要有一个主目标服务器。 在此服务器上安装统一代理。
**VMware ESX/ESXi 和 vCenter 服务器** |  VMs 托管在 ESX/ESXi 主机上。 主机通过 vCenter 服务器进行托管 | 需要使用 VMware 基础结构来复制 VMware VM。
**VM/物理服务器** |  安装在要复制的 VMware VM 和物理服务器上的统一代理。 | 该代理充当所有组件之间的通信提供程序。


### <a name="replication-process"></a>复制过程

1. 在每个站点（配置、进程、主目标）中设置组件服务器，并在要复制的计算机上安装统一代理。
2. 在初始复制之后，每台计算机上的代理会将增量复制更改发送到进程服务器。
3. 进程服务器将优化这些数据，并将其传输到辅助站点上的主目标服务器。 配置服务器将管理复制进程。

图 2：VMware 到 VMware 的复制

![VMware 到 VMware](./media/site-recovery-components/vmware-to-vmware.png)


## <a name="next-steps"></a>后续步骤

查看[支持矩阵](site-recovery-support-matrix-to-sec-site.md)

