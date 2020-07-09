---
title: 关于使用 Azure Site Recovery 进行 Azure VM 灾难恢复的常见问题解答
description: 本文介绍了关于使用 Azure Site Recovery 进行 Azure VM 灾难恢复的常见问题解答。
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 22848d84896989b1872c55e687c4a5e73da31de8
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86134037"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>常见问题：Azure 到 Azure 的灾难恢复

本文介绍了关于使用 [Azure Site Recovery](site-recovery-overview.md) 将 Azure VM 灾难恢复到另一个 Azure 区域的常见问题解答。

## <a name="general"></a>常规

### <a name="how-is-site-recovery-priced"></a>Site Recovery 如何计费？

请查阅 [VM 的 Azure Site Recovery 定价](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)。

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Azure Site Recovery 的免费层是如何工作的？

每个使用 Azure Site Recovery 保护的实例在其保护期的前 31 天内均享受免费。 在此之后，对每个实例的保护将按 [Azure 虚拟机的 Azure Site Recovery 定价](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)中的费率进行收费。

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>在前 31 天的期限内，会产生其他 Azure 费用吗？

是的。 尽管 Azure Site Recovery 在实例受保护的前 31 天是免费的，但你可能需要支付 Azure 存储、存储交易和数据传输费用。 已恢复的虚拟机还可能会产生 Azure 计算费用。 有关定价的完整详细信息，请访问 [Azure Site Recovery 定价](https://azure.microsoft.com/pricing/details/site-recovery)。

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Azure 虚拟机灾难恢复的最佳做法是什么？

1. [了解 Azure 到 Azure 体系结构](azure-to-azure-architecture.md)
1. [查看支持和不支持的配置](azure-to-azure-support-matrix.md)
1. [为 Azure VM 设置灾难恢复](azure-to-azure-how-to-enable-replication.md)
1. [运行测试故障转移](azure-to-azure-tutorial-dr-drill.md)
1. [故障转移和故障回复到主要区域](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>如何确保目标区域中的容量？

Site Recovery 团队和 Azure 容量管理团队计划了足够的基础结构容量。 如果你启动故障转移，这些团队还会帮助确保受 Site Recovery 保护的 VM 实例将部署到目标区域。

## <a name="replication"></a>复制

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>是否可以复制通过 Azure 磁盘加密启用的 VM？

是的。 Site Recovery 支持对已启用 Azure 磁盘加密的 VM 进行灾难恢复。 如果你启用复制，Azure 会将源区域中所有必需的磁盘加密密钥和机密复制到用户上下文中的目标区域。 如果你没有适当的权限，安全管理员可以使用脚本来复制密钥和机密。

- Site Recovery 支持对运行 Windows 的 Azure VM 使用 Azure 磁盘加密。
- Site Recovery 支持 Azure 磁盘加密版本 0.1，此版本的架构需要 Azure Active Directory (Azure AD)。 Site Recovery 还支持不需要 Azure AD 的版本 1.1。 [详细了解 Azure 磁盘加密的扩展架构](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema)。
  - 对于 Azure 磁盘加密版本 1.1，必须使用包含托管磁盘的 Windows VM。
  - [详细了解](azure-to-azure-how-to-enable-replication-ade-vms.md)如何为加密 VM 启用复制。

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>是否可以从其他资源组中选择自动化帐户？

此功能目前不受门户支持，但你可以通过 Powershell 从不同的资源组中选择自动化帐户。

### <a name="after-specifying-an-automation-account-that-is-in-a-different-resource-group-than-the-vault-am-i-permitted-to-delete-the-runbook-if-there-is-no-other-vault-to-specify"></a>指定与保管库位于不同资源组中的自动化帐户后，如果没有其他保管库要指定，是否允许删除 runbook？

创建的自定义 runbook 是一个工具，如果不需要相同的时间，则可以放心地删除。

### <a name="can-i-replicate-vms-to-another-subscription"></a>是否可将 VM 复制到另一个订阅？

是的，可将 Azure VM 复制到同一 Azure AD 租户中的不同订阅。

通过在复制时选择另一个订阅，配置[跨订阅](https://azure.microsoft.com/blog/cross-subscription-dr)灾难恢复。

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>是否可将区域固定的 Azure VM 复制到另一个区域？

是的，可[将区域固定的 VM 复制到](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region)另一个区域。

### <a name="can-i-exclude-disks"></a>是否可以排除磁盘？

是的，可以在保护时使用 PowerShell 排除磁盘。 有关详细信息，请参阅[如何从复制中排除磁盘](azure-to-azure-exclude-disks.md)。

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>能否向已复制 VM 添加新磁盘，并为这些磁盘启用复制？

能，包含托管磁盘的 Azure VM 支持向已复制 VM 添加新磁盘，并为这些磁盘启用复制。 如果你将新磁盘添加到已启用复制的 Azure VM，VM 的复制运行状况会显示警告。 此警告指明可保护 VM 上的一个或多个磁盘。 可以为添加的磁盘启用复制。

- 如果为添加的磁盘启用保护，此警告就会在初始复制后消失。
- 如果没有为磁盘启用复制，可以消除警告。
- 如果对已添加磁盘且已启用复制的 VM 进行故障转移，则存在复制点。 复制点显示可供恢复的磁盘。

例如，假设 VM 有一个磁盘，你又添加了一个新磁盘。 可能有一个复制点是在你添加磁盘之前创建的。 此复制点显示它由“2 个磁盘中的 1 个”组成。

Site Recovery 不支持从已复制 VM 中“热删除”磁盘。 如果删除 VM 磁盘，则需要先为 VM 禁用复制，再重新启用它。

### <a name="how-often-can-i-replicate-to-azure"></a>可以多久复制到 Azure 一次？

将 Azure VM 复制到另一个 Azure 区域时，复制是持续性的。 有关详细信息，请参阅 [Azure 到 Azure 复制体系结构](./azure-to-azure-architecture.md#replication-process)。

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>是否可以在某个区域中复制虚拟机？ 我需要此功能来迁移 VM。

无法使用 Azure 到 Azure 磁盘恢复解决方案在一个区域内复制 VM。

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>能否将 VM 实例复制到任意 Azure 区域？

使用 Site Recovery，可以在同一个地理群集中的任意两个区域之间复制和恢复 VM。 地理群集的定义考虑到了数据延迟和主权。 有关详细信息，请参阅 Site Recovery 的[区域支持矩阵](./azure-to-azure-support-matrix.md#region-support)。

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery 需要建立 Internet 连接？

否，Site Recovery 不需要连接到 Internet。 但它需要能够访问 Site Recovery URL 和 IP 范围，如 [Azure VM 灾难恢复中的网络](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls)中所述。

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>能否复制对各个层有不同资源组的应用？

能，可以复制应用，且灾难恢复配置也保留在单独的资源组中。

例如，如果应用将每一层的应用、数据库和 Web 放置在不同的资源组中，那么你必须选择[复制向导](./azure-to-azure-how-to-enable-replication.md#enable-replication)三次，才能保护所有层。 Site Recovery 会将这三个层复制到三个不同的资源组中。

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>能否跨资源组移动存储帐户？

否，此方案不受支持。 不过，如果你意外地将存储帐户移到不同的资源组，并删除了原始资源组，则可以新建一个与旧资源组同名的资源组，然后将存储帐户移到此资源组中。

## <a name="replication-policy"></a>复制策略

### <a name="what-is-a-replication-policy"></a>什么是复制策略？

复制策略定义了恢复点的保留历史记录设置。 此策略还定义了应用一致性快照的频率。 默认情况下，Azure Site Recovery 使用以下默认设置创建新的复制策略：

- 恢复点历史记录的保留期为 24 小时。
- 应用一致性快照的频率为 60 分钟。

[详细了解复制设置](./azure-to-azure-tutorial-enable-replication.md#configure-replication-settings)。

### <a name="what-is-a-crash-consistent-recovery-point"></a>什么是崩溃一致性恢复点？

故障一致性恢复点包含，与在快照期间从服务器拔下电源线时一样的磁盘上数据。 故障一致性恢复点不包含在拍摄快照时的任何内存中数据。

目前，大多数应用程序都可以从崩溃一致性快照正常恢复。 对于无数据库的操作系统以及文件服务器、DHCP 服务器、打印服务器等应用程序而言，崩溃一致性恢复点通常已足够。

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>崩溃一致性恢复点生成的频率是多少？

Site Recovery 每隔 5 分钟创建崩溃一致性恢复点。

### <a name="what-is-an-application-consistent-recovery-point"></a>什么是应用程序一致性恢复点？

应用程序一致性恢复点是从应用程序一致性快照创建的。 应用一致性恢复点除了捕获与故障一致性快照相同的数据，还捕获内存中数据以及进程中的所有事务。

由于包含额外内容，因此应用一致性快照涉及最多且耗时最长。 我们建议对数据库操作系统以及 SQL Server 等应用程序使用应用程序一致性恢复点。

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>应用程序一致性恢复点对应用程序性能有何影响？

应用一致性恢复点捕获内存中和进程中的所有数据。 因为恢复点捕获此类数据，所以它们需要 Windows 上的卷影复制服务等框架来让应用处于静止状态。 如果捕获过程频繁发生，当工作负荷已经很忙时，它可能会影响性能。 对于非数据库工作负荷，建议不要对应用一致性恢复点使用低频率。 即使对于数据库工作负荷，1 小时也足够了。

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>应用程序一致性恢复点生成的最低频率是多少？

Site Recovery 可以创建最低频率为 1 小时的应用一致性恢复点。

### <a name="how-are-recovery-points-generated-and-saved"></a>如何生成和保存恢复点？

为了理解 Site Recovery 如何生成恢复点，让我们来看一个复制策略示例。 此复制策略有一个恢复点，它的保留期为 24 小时，应用一致性快照频率为 1 小时。

Site Recovery 每隔 5 分钟创建崩溃一致性恢复点。 你无法更改此频率。 对于过去一个小时，你可以从 12 个故障一致性恢复点和 1 个应用一致性恢复点中进行选择。 随着时间的推移，Site Recovery 会删除过去一个小时之外的所有恢复点，并且每小时只保存 1 个恢复点。

以下屏幕截图演示了该示例。 在屏幕截图中：

- 在过去一个小时内，有一些频率为 5 分钟的恢复点。
- 在过去一个小时之外，Site Recovery 只保留 1 个恢复点。

   ![生成的恢复点列表](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>可以恢复到哪个最早的时间点？

可以使用的最早恢复点是 72 小时。

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>我有保留期为 24 小时的复制策略。 如果某个问题导致 Site Recovery 超过 24 小时无法生成恢复点，将会怎样？ 以前的恢复点是否将丢失？

不会，Site Recovery 将保留以前的所有恢复点。 根据恢复点的保留期，Site Recovery 仅在生成新点时才替换最早的点。 由于此问题，Site Recovery 无法生成任何新的恢复点。 在有新的恢复点之前，所有旧的恢复点都将在到达保留期后保留。

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>在 VM 上启用复制后，如何更改复制策略？

转到“Site Recovery 保管库” > “Site Recovery 基础结构” > “复制策略”。   选择要编辑的策略，然后保存所做的更改。 任何更改也会应用到现有的所有复制。

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>所有恢复点是包含 VM 的完整副本还是差异副本？

生成的第一个恢复点包含完整副本。 任何后续恢复点包含增量更改。

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>增大恢复点保留期是否会增加存储成本？

是，如果你将保留期从 24 小时延长到 72 小时，Site Recovery 会额外保存恢复点 48 小时。 增加的时间会产生存储费用。 例如，一个恢复点可能有 10GB 的增量更改，每月每 GB 的费用为 $0.16。 额外费用为每月 $1.60 × 48。

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>能否在 Linux 服务器中使用应用一致性启用复制？

是。 Linux 操作系统的 Azure Site Recovery 支持应用程序的自定义脚本，以实现应用程序一致性。 在应用程序一致性期间 Azure Site Recovery 移动代理将使用带有 pre 和 post 选项的自定义脚本。 [了解详细信息](./site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

## <a name="multi-vm-consistency"></a>多 VM 一致性

### <a name="what-is-multi-vm-consistency"></a>什么是多 VM 一致性？

多 VM 一致性可以确保恢复点在所有已复制虚拟机之间保持一致。

Site Recovery 提供了“多 VM 一致性”选项，用于创建所有虚拟机的复制组。

如果你对虚拟机进行故障转移，它们会共用故障一致性恢复点和应用一致性恢复点。

请阅读关于如何[启用多 VM 一致性](./azure-to-azure-tutorial-enable-replication.md#enable-replication-for-a-vm)的整个教程。

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>能否对多 VM 一致性复制组中的单个虚拟机进行故障转移？

选择“多 VM 一致性”选项即表明应用依赖于组中的所有虚拟机。 不允许对单个虚拟机进行故障转移。

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>可以在多 VM 一致性复制组中复制多少个虚拟机？

在一个复制组中，可以一同复制 16 个虚拟机。

### <a name="when-should-i-enable-multi-vm-consistency"></a>何时应启用多 VM 一致性？

由于多 VM 一致性占用大量 CPU 资源，启用它可能会影响工作负荷性能。 只有在虚拟机运行相同的工作负荷，且你需要在多个虚拟机之间保持一致性时，才使用多 VM 一致性。 例如，如果一个应用中有两个 SQL Server 实例和两个 Web 服务器，只应为 SQL Server 实例启用多 VM 一致性。

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>能否将已复制 VM 添加到复制组？
可以将 VM 添加到新的复制组，同时启用复制。 还可以将 VM 添加到现有的复制组，同时启用复制。 但是，不能将已在复制的 VM 添加到新的或现有的复制组。
 
## <a name="failover"></a>故障转移


### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>如何确保 Azure VM 的目标区域中的容量？

Site Recovery 团队和 Azure 容量管理团队计划了足够的基础结构容量。 如果你启动故障转移，这些团队还会帮助确保受 Site Recovery 保护的 VM 实例将部署到目标区域。

### <a name="is-failover-automatic"></a>故障转移是自动发生的吗？

故障转移不是自动的。 可以在门户中单击一下来启动故障转移，也可以使用 [PowerShell](azure-to-azure-powershell.md) 来触发故障转移。

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>能否在故障转移后保留公共 IP 地址？

在故障转移后，无法保留生产应用的公共 IP 地址。

在故障转移过程中启动工作负荷时，需要向工作负荷分配 Azure 公共 IP 资源。 Azure 公共 IP 资源必须在目标区域中可用。 可以手动分配 Azure 公共 IP 资源，也可以使用恢复计划来自动分配。 了解如何[在故障转移后设置公共 IP 地址](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan)。

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>能否在故障转移期间保留专用 IP 地址？

能，可以保留专用 IP 地址。 默认情况下，为 Azure VM 启动灾难恢复时，Site Recovery 将根据源资源设置创建目标资源。 对于配置有静态 IP 地址的 Azure 虚拟机，Site Recovery 会尝试为没有使用的目标 VM 预配相同的 IP 地址。
了解如何[在故障转移期间保留 IP 地址](site-recovery-retain-ip-azure-vm-failover.md)。

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>为什么在故障转移后要向服务器分配新的 IP 地址？

故障转移时，Site Recovery 会尽量提供 IP 地址。 如果该地址被另一个虚拟机占用，则 Site Recovery 会将下一个可用 IP 地址设为目标。

详细了解[如何设置虚拟网络的网络映射和 IP 地址](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)。

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>什么是**最新（最低 RPO）** 恢复点？

“最新(最低 RPO)”选项先处理已发送到 Site Recovery 的所有数据。 在处理数据后，服务会在故障转移到 VM 之前为每个 VM 创建一个恢复点。 此选项提供了最低恢复点目标 (RPO)。 在故障转移后创建的 VM 包含自故障转移触发起复制到 Site Recovery 的所有数据。

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>**最新（最低 RPO）** 恢复点是否影响故障转移 RTO？

是的。 因为 Site Recovery 在故障转移之前处理所有挂起的数据，所以此选项的恢复时间目标 (RTO) 比其他选项更高。

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>恢复点中的“最新处理”选项指的是什么？

“最新处理”选项将计划中的所有 VM 故障转移到 Site Recovery 处理的最新恢复点。 若要查看特定 VM 的最新恢复点，请检查 VM 设置中的“最新恢复点”。 此选项提供低的 RTO，因为无需费时处理未经处理的数据。

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>如果主要区域遇到意外故障，会怎么样？

可以在服务中断后触发故障转移。 Site Recovery 不需要从主要区域进行连接，即可执行故障转移。

### <a name="what-is-an-rto-of-a-vm-failover"></a>VM 故障转移的 RTO 是什么？

Site Recovery 的 [RTO SLA 为 2 小时](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)。 不过，在大多数情况下，Site Recovery 在几分钟内即可对虚拟机进行故障转移。 可以通过转到故障转移作业来计算 RTO，此作业显示了启动 VM 所需的时间。 有关恢复计划 RTO，请参考下一部分。

## <a name="recovery-plans"></a>恢复计划

### <a name="what-is-a-recovery-plan"></a>什么是恢复计划？

Site Recovery 中的恢复计划可以协调 VM 的故障转移恢复。 它有助于实现恢复的一致准确性、可重复性和自动化。 恢复计划要满足以下需求：

- 定义一组可以一起故障转移的虚拟机
- 定义虚拟机之间的依赖关系，使应用程序能够适时启动
- 自动完成恢复以及自定义的手动操作，以便除虚拟机故障转移以外，还可以实现其他任务

详细了解[如何创建恢复计划](site-recovery-create-recovery-plans.md)。

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>如何在恢复计划中实现定序？

在恢复计划中，可以创建多个组来实现定序。 每次故障转移一个组。 同一个组中的虚拟机一起进行故障转移，然后进行另一个组的故障转移。 若要了解如何使用恢复计划为应用程序建模，请参阅[关于恢复计划](recovery-plan-overview.md#model-apps)。

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>如何找到恢复计划的 RTO？

若要检查恢复计划的 RTO，请对恢复计划执行测试故障转移，然后转到“Site Recovery 作业”。
在下面的示例中，查看作业“SAPTestRecoveryPla”n。 此作业花费了 8 分钟 59 秒来故障转移所有虚拟机，并执行指定的操作。

![Site Recovery 作业列表](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>是否可将自动化 Runbook 添加到恢复计划？

是的，可将 Azure 自动化 Runbook 集成到恢复计划中。 详细了解[如何添加 Azure 自动化 Runbook](site-recovery-runbook-automation.md)。

## <a name="reprotection-and-failback"></a>重新保护和故障回复

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>我从主要区域故障转移到了灾难恢复区域。 DR 区域中的 VM 是否自动受到保护？

否。 将 Azure VM 从一个区域[故障转移](./azure-to-azure-tutorial-failover-failback.md)到另一个区域后，VM 将在灾难恢复区域中启动，但处于不受保护状态。 若要将 VM 故障回复到主要区域，需要[重新保护](./azure-to-azure-how-to-reprotect.md)次要区域中的 VM。

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>重新保护时，Site Recovery 是否将完整的数据从次要区域复制到主要区域？

这取决于具体的情况。 如果源区域 VM 存在，那么只同步源磁盘与目标磁盘之间的更改。 Site Recovery 将通过比较磁盘来计算差异，然后传输数据。 此过程通常需要几个小时。 若要详细了解重新保护期间所发生的情况，请参阅[在主要区域中重新保护已故障转移的 Azure VM 实例](./azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection)。

### <a name="how-much-time-does-it-take-to-fail-back"></a>故障回复需要多长时间？

在重新保护之后，故障回复所需的时间与从主要区域故障转移到次要区域所需的时间大致相同。

## <a name="capacity"></a><a name="capacity"></a>容量

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>如何确保 Azure VM 的目标区域中的容量？

Site Recovery 团队和 Azure 容量管理团队计划了足够的基础结构容量。 如果你启动故障转移，这些团队还会帮助确保受 Site Recovery 保护的 VM 实例将部署到目标区域。

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery 是否适用于预留实例？

是，可以在灾难恢复区域中购买[预留 Azure VM](https://azure.microsoft.com/pricing/reserved-vm-instances/)，Site Recovery 故障转移操作将使用它们。 不需要任何其他配置。

## <a name="security"></a>安全性

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>复制数据是否会发送到 Site Recovery 服务？

否。Site Recovery 不会截获已复制数据，也不包含 VM 上运行的组件的任何相关信息。 只有协调复制与故障转移所需的元数据将发送到站点恢复服务。

Site Recovery 获得了 ISO 27001:2013、27018、HIPAA 和 DPA 认证。 此服务正在接受 SOC2 和 FedRAMP JAB 评估。

### <a name="does-site-recovery-encrypt-replication"></a>站点恢复是否将复制数据加密？

是，Azure 中的传输中加密和[静态加密](../storage/common/storage-service-encryption.md)均受支持。

## <a name="next-steps"></a>后续步骤

- [查阅 Azure 到 Azure 支持要求](azure-to-azure-support-matrix.md)。
- [设置 Azure 到 Azure 复制](azure-to-azure-tutorial-enable-replication.md)。
- 如果在阅读本文后有任何疑问，请将它们发布在[关于 Azure 恢复服务的 Microsoft Q&A 问题页](/answers/topics/azure-site-recovery.html)上。
