---
title: Azure 迁移服务器迁移常见问题解答
description: 获取有关使用 Azure 迁移服务器迁移迁移计算机的常见问题的解答。
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: cc78d2087dcaad2922ca6b6d9c090a8decdb6e84
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393798"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure 迁移服务器迁移：常见问题

本文回答了有关 Azure 迁移：服务器迁移工具的常见问题。 如果您有其他问题，请检查以下资源：

- 有关 Azure 迁移的[一般问题](resources-faq.md)
- 有关[Azure 迁移设备的问题](common-questions-appliance.md)
- 有关[发现、评估和依赖项可视化](common-questions-discovery-assessment.md)的问题
- 在[Azure 迁移论坛](https://aka.ms/AzureMigrateForum)中回答问题

## <a name="how-does-agentless-vmware-replication-work"></a>无代理 VMware 复制如何工作？

VMware 的无代理复制方法使用 VMware 快照和 VMware 更改块跟踪 （CBT）。

流程如下：

1. 开始复制时，将计划初始复制周期。 在初始周期中，将获取 VM 的快照。 快照用于复制 VMVMDK（磁盘）。 
2. 初始复制周期完成后，定期计划增量复制周期。
    - 在增量复制期间，将拍摄快照，并复制自上次复制周期以来已更改的数据块。
    - VMware CBT 用于确定自上次周期以来已更改的块。
    - 定期复制周期的频率由 Azure 迁移自动管理，具体取决于有多少其他 VM 和磁盘同时从同一数据存储复制。 在理想情况下，复制最终为每个 VM 每小时收敛一个周期。

迁移时，计算机将安排一个按需复制周期来捕获任何剩余数据。 为了确保零数据丢失和应用程序一致性，您可以选择在迁移期间关闭计算机。

## <a name="why-isnt-resynchronization-exposed"></a>为什么不公开重新同步？

在无代理迁移期间，在每个增量周期中，将写入当前快照和以前拍摄的快照之间的差异。 它总是快照、折叠数据之间的差异。 如果特定扇区在快照之间写入*N*次，则只需要传输最后一个写入，因为我们只对上次同步感兴趣。该过程不同于基于代理的复制，在此期间，我们跟踪和应用每个写入。 在此过程中，每个增量周期都是重新同步。 因此，不会公开重新同步选项。 如果磁盘因故障而未同步，则在下一个周期中修复。 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>改性率如何影响无代理复制？

由于无代理复制在数据中折叠，*因此改动模式*比*改动率*更重要。 当一次又一次地写入文件时，速率没有太大的影响。 但是，写入其他每个扇区的模式会导致下一个周期的高流失。 由于我们最大限度地减少了传输的数据量，因此在计划下一个周期之前，我们允许数据尽可能折叠。  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>复制周期的调度频率如何？

计划下一个复制周期的公式是（以前的周期时间 / 2）或一小时，以较高者为准。

例如，如果 VM 的增量周期需要四个小时，则下一个周期将在两小时内安排，而不是在下一小时内。 初始复制后立即进行该过程不同，此时会立即计划第一个增量周期。

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>无代理复制如何影响 VMware 服务器？

无代理复制会对 VMware vCenter 服务器和 VMware ESXi 主机产生一些性能影响。 由于无代理复制使用快照，因此它在存储时使用 IOPS，因此需要一些 IOPS 存储带宽。 如果您的环境中对存储或 I普具有限制，我们不建议使用无代理复制。

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>是否可以对 UEFI VM 进行无代理迁移，以迁移到 Azure 第 2 代？

不是。 使用 Azure 站点恢复将这些 VM 迁移到第 2 代 Azure VM。 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>迁移时，是否可以将 VM 固定到 Azure 可用性区域？

不是。 Azure 迁移不支持 Azure 可用性区域。

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>Azure 迁移在复制期间使用什么传输协议？

Azure 迁移使用带有 SSL 加密的网络块设备 （NBD） 协议。

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>迁移所需的最低 vCenter 服务器版本是什么？

您必须至少具有 vCenter 服务器 5.5 和 vSphere ESXi 主机版本 5.5。

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>客户是否可以将其 VM 迁移到非托管磁盘？

不是。 Azure 迁移仅支持迁移到托管磁盘（标准硬盘、高级 SSD）。

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>使用无代理迁移可以同时复制多少 VM？

目前，您可以同时迁移每个 vCenter Server 实例的 100 个 VM。 分批迁移 10 个 VM。

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>如何使用 Azure 迁移设备进行无代理 VMware 复制，如何限制复制？  

您可以使用 NetQos 策略进行节流。 例如：

NetQos 策略中要使用的 AppNamePrefix 是"网关Windows服务.exe"。 您可以在 Azure 迁移设备上创建策略，通过创建如下策略来限制来自设备的复制流量：
 
新网调策略 - 名称"节流复制" - AppPathName 匹配条件"网关Windows服务.exe" - 节流操作位秒 1MB

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>何时将计算机迁移到物理服务器？

通过将计算机视为物理服务器来迁移计算机在许多情况下很有用：

- 迁移本地物理服务器时。
- 如果要迁移由 Xen、KVM 等平台虚拟化的 VM。
- 要迁移 Hyper-V 或 VMware VM，如果由于某种原因无法将标准迁移过程用于[Hyper-V](tutorial-migrate-hyper-v.md)，或[VMware](server-migrate-overview.md)迁移。 例如，如果您未运行 VMware vCenter，并且仅使用 ESXi 主机。
- 将当前在私有云中运行的 VM 迁移到 Azure
- 如果要将在公共云（如 Amazon Web 服务 （AWS） 或 Google 云平台 （GCP））中运行的 VM 迁移到 Azure。

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliance"></a>我部署了两个（或更多）设备来发现 vCenter 服务器中的 VM。 但是，当我尝试迁移 VM 时，我只看到与其中一个设备对应的 VM。

虽然这可能是一个很好的用例，我们目前不支持它。 部署两个（或更多）设备以发现同一组 VM 会导致 VM 所有权在两个设备之间不断切换的服务问题。 这是您看到 VM 出现和消失的原因。 在这种情况下，要解决此问题，必须删除一个设备并执行硬刷新。

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>我是否需要 VMware vCenter 来迁移 VMware VM？
要使用基于 VMware 代理或无代理的迁移[迁移 VMware VM，VM](server-migrate-overview.md)所在的 ESXi 主机必须由 vCenter Server 管理。 如果没有 vCenter 服务器，则可以通过将 VMware VM 迁移到物理服务器来迁移 VMware VM。 [了解详细信息](migrate-support-matrix-physical-migration.md)。
 
## <a name="next-steps"></a>后续步骤

阅读[Azure 迁移概述](migrate-services-overview.md)。
