---
title: 有关 Azure Migrate 服务器迁移的常见问题
description: 获取有关使用 Azure Migrate 服务器迁移迁移计算机的常见问题的解答。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 0cfe23b4e544040fc3ab69796988ca34b1bdcdbf
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744322"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate Server 迁移：常见问题

本文解答了有关 Azure Migrate： Server 迁移工具的常见问题。 如果你有其他问题，请查看以下资源：

- 有关 Azure Migrate 的[一般问题](resources-faq.md)
- 关于[Azure Migrate 设备](common-questions-appliance.md)的问题
- 有关[发现、评估和依赖项可视化](common-questions-discovery-assessment.md)的问题
- 在[Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)中获取问题答案

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>哪些地理区域支持 Azure Migrate 迁移？

查看[公有](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。

## <a name="how-does-agentless-vmware-replication-work"></a>无代理 VMware 复制的工作原理是什么？

VMware 的无代理复制方法使用 VMware 快照和 VMware 更改的阻止跟踪（CBT）。

流程如下：

1. 启动复制时，将安排初始复制循环。 在初始循环中，会创建 VM 的快照。 快照用于复制 Vm Vmdk （磁盘）。 
2. 初始复制周期结束后，将定期计划增量复制循环。
    - 在增量复制过程中，将会创建快照，并复制自上次复制循环以来发生更改的数据块。
    - VMware CBT 用于确定自上次循环后已更改的块。
    - 定期复制循环的频率由 Azure Migrate 自动管理，具体取决于同时从同一个数据存储复制的其他 Vm 和磁盘的数量。 在理想情况下，对于每个 VM，复制最终聚合为每小时一个周期。

在迁移时，将为计算机安排按需复制循环来捕获所有剩余数据。 若要确保零数据丢失和应用程序一致性，可以选择在迁移过程中关闭计算机。

## <a name="why-isnt-resynchronization-exposed"></a>为什么未公开同步？

在无代理迁移期间，在每个增量循环中，将写入当前快照和之前拍摄的快照之间的差异。 这始终是快照之间的差异，并折叠中的数据。 如果在两个快照之间写入了*N*次特定的扇区，则只需要传输最后一个写入，因为我们只对上次同步感兴趣。此过程不同于基于代理的复制，在此过程中，我们将跟踪并应用每个写入操作。 在此过程中，每个增量周期都是重新同步。 因此，没有公开任何同步选项。 如果磁盘由于故障而不同步，则会在下一个周期中进行修复。 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>改动速度如何影响无代理复制？

由于无代理复制会在数据中折叠，因此*改动模式*比*改动速度*更重要。 再次编写文件时，速率不会产生很大的影响。 但是，每个其他扇区的写入模式在下一个周期中会导致较高的变动。 由于我们最大限度地减少了传输的数据量，因此，我们允许数据在计划下一个周期之前折叠尽可能多。  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>复制循环计划的频率如何？

计划下一个复制周期的公式为（上一个周期时间/2）或一小时，以较大者为准。

例如，如果某个 VM 的增量周期使用四个小时，则将在两个小时内计划下一个周期，而不是在下一个小时。 此过程在初始复制之后（在立即计划第一个增量周期时）是不同的。

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>无代理复制如何影响 VMware 服务器？

无代理复制会导致对 VMware vCenter 服务器和 VMware ESXi 主机的性能影响。 由于无代理复制使用快照，因此它将消耗存储空间，因此需要某些 IOPS 存储带宽。 如果对环境中的存储或 IOPs 有限制，我们不建议使用无代理复制。

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>是否可以进行 UEFI Vm 到 Azure 第2代的无代理迁移？

否。 使用 Azure Site Recovery 将这些虚拟机迁移到第2代 Azure Vm。 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>迁移时，是否可以将 Vm 固定到 Azure 可用性区域？

否。 Azure Migrate 迁移不支持 Azure 可用性区域。

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>在复制过程中 Azure Migrate 使用哪种传输协议？

Azure Migrate 使用带有 TLS 加密的网络块设备（NBD）协议。

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>迁移需要的最低 vCenter Server 版本是什么？

至少必须有 vCenter Server 5.5 和 vSphere ESXi 主机版本5.5。

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>客户是否可以将其虚拟机迁移到非托管磁盘？

否。 Azure Migrate 仅支持对托管磁盘（标准 HDD、高级 SSD）的迁移。

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>使用无代理迁移，可以一次复制多少个 Vm？

目前，可以同时迁移每个 vCenter Server 的 100 Vm。 成批迁移10个虚拟机。

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>使用 Azure Migrate 设备进行无代理 VMware 复制的如何实现限制复制？  

可以使用 New-netqospolicy 进行限制。 例如：

要在 New-netqospolicy 中使用的 AppNamePrefix 为 "GatewayWindowsService"。 可以通过创建如下策略，在 Azure Migrate 设备上创建策略，以限制设备的复制流量：
 
New-netqospolicy-Name "ThrottleReplication"-AppPathNameMatchCondition "GatewayWindowsService"-ThrottleRateActionBitsPerSecond 1MB

## <a name="can-i-migrate-vms-that-are-already-being-replicated-to-azure"></a>能否迁移已复制到 Azure 的 Vm？ 

如果已通过其他方式将 Vm 复制到 Azure，则不能将这些计算机迁移为 Vm，Azure Migrate 服务器迁移。 作为一种解决方法，你可以将 Vm 视为物理服务器，并根据[支持的物理服务器迁移](migrate-support-matrix-physical-migration.md)将其迁移。

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>何时将计算机作为物理服务器迁移？

通过将计算机视为物理服务器来迁移计算机在许多情况下很有用：

- 正在迁移本地物理服务器。
- 如果迁移的是虚拟化的 Vm，请使用诸如 Xen、KVM 等平台。
- 若要迁移 Hyper-v 或 VMware Vm，如果出于某种原因，你无法使用适用于[hyper-v](tutorial-migrate-hyper-v.md)的标准迁移过程或[vmware](server-migrate-overview.md)迁移。 例如，如果你未运行 VMware vCenter，且仅使用 ESXi 主机。
- 将当前在私有云中运行的虚拟机迁移到 Azure
- 如果要将在公有云中运行的 Vm （例如 Amazon Web Services （AWS）或 Google Cloud Platform （GCP））迁移到 Azure。

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliance"></a>我部署了两个（或多个）设备来发现我的 vCenter Server 中的 Vm。 但是，当我尝试迁移 Vm 时，只会看到与某个设备相关的 Vm。

虽然这可能是一个很好的用例，但目前不支持这种情况。 部署两个（或多个）设备以发现同一组 Vm 会导致服务问题，其中 VM 所有权在两个设备之间保持切换。 这就是显示 Vm 出现和消失的原因。 在这种情况下，若要解决此问题，你必须删除一个设备并执行硬刷新。

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>是否需要 VMware vCenter 才能迁移 VMware Vm？
若要使用基于 VMware 代理或无代理迁移迁移[Vmware vm](server-migrate-overview.md) ，则必须通过 VCenter Server 管理 vm 所在的 ESXi 主机。 如果没有 vCenter Server，则可以通过将 VMware Vm 作为物理服务器进行迁移来将其迁移。 [了解详细信息](migrate-support-matrix-physical-migration.md)。
 
## <a name="next-steps"></a>后续步骤

阅读[Azure Migrate 概述](migrate-services-overview.md)。
