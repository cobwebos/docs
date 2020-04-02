---
title: 有关 Azure 站点恢复的 Azure VM 灾难恢复的常见问题
description: 本文回答了使用 Azure 站点恢复时有关 Azure VM 灾难恢复的常见问题。
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 7d3bcc32dc8f1412a5adbc175a5f8618628bce83
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547890"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>常见问题：Azure 到 Azure 灾难恢复

本文回答了有关 Azure VM 灾难恢复到另一个 Azure 区域的常见问题，供您使用[Azure 站点恢复](site-recovery-overview.md)时。

## <a name="general"></a>常规

### <a name="how-is-site-recovery-priced"></a>Site Recovery 如何计费？

查看[VM 的 Azure 站点恢复定价](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)。

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Azure Site Recovery 的免费层是如何工作的？

每个使用 Azure Site Recovery 保护的实例在其保护期的前 31 天内均享受免费。 在此期间之后，每个实例的保护以[Azure 虚拟机的 Azure 站点恢复定价中的](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)速率进行。

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>在前 31 天的期限内，会产生其他 Azure 费用吗？

是的。 即使 Azure 站点恢复在受保护实例的前 31 天是免费的，也可能对 Azure 存储、存储事务和数据传输产生费用。 恢复的虚拟机也可能产生 Azure 计算费用。 在[Azure 站点恢复定价](https://azure.microsoft.com/pricing/details/site-recovery)中获取有关定价的完整详细信息。

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Azure 虚拟机灾难恢复的最佳做法是什么？

1. [了解 Azure 到 Azure 体系结构](azure-to-azure-architecture.md)
1. [查看支持和不支持的配置](azure-to-azure-support-matrix.md)
1. [为 Azure VM 设置灾难恢复](azure-to-azure-how-to-enable-replication.md)
1. [运行测试故障转移](azure-to-azure-tutorial-dr-drill.md)
1. [故障转移和故障回复到主要区域](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>如何确保目标区域的容量？

站点恢复团队和 Azure 容量管理团队计划有足够的基础结构容量。 启动故障转移时，团队还有助于确保受站点恢复保护的 VM 实例将部署到目标区域。

## <a name="replication"></a>复制

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>是否可以复制通过 Azure 磁盘加密启用的 VM？

是的。 站点恢复支持启用 Azure 磁盘加密的 VM 的灾难恢复。 启用复制时，Azure 会将所有必需的磁盘加密密钥和机密从源区域复制到用户上下文中的目标区域。 如果您没有适当的权限，您的安全管理员可以使用脚本复制密钥和机密。

- 站点恢复支持为运行 Windows 的 Azure VM 进行 Azure 磁盘加密。
- 站点恢复支持 Azure 磁盘加密版本 0.1，该版本具有需要 Azure 活动目录 （Azure AD） 的架构。 站点恢复还支持版本 1.1，它不需要 Azure AD。 [了解有关 Azure 磁盘加密扩展架构的更多详细信息](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema)。
  - 对于 Azure 磁盘加密版本 1.1，必须使用具有托管磁盘的 Windows VM。
  - [详细了解](azure-to-azure-how-to-enable-replication-ade-vms.md)如何为加密 VM 启用复制。

### <a name="can-i-replicate-vms-to-another-subscription"></a>是否可将 VM 复制到另一个订阅？

是的，可将 Azure VM 复制到同一 Azure AD 租户中的不同订阅。

通过在复制时选择另一个订阅来配置[跨订阅](https://azure.microsoft.com/blog/cross-subscription-dr)的灾难恢复。

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>是否可将区域固定的 Azure VM 复制到另一个区域？

是的，可[将区域固定的 VM 复制到](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region)另一个区域。

### <a name="can-i-exclude-disks"></a>是否可以排除磁盘？

是的，可以在保护时使用 PowerShell 排除磁盘。 有关详细信息，请参阅[如何从复制中排除磁盘](azure-to-azure-exclude-disks.md)。

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>是否可以向复制的 VM 添加新磁盘并为这些磁盘启用复制？

可以，对于具有托管磁盘的 Azure VM，支持向复制的 VM 添加新磁盘并为它们启用复制。 将新磁盘添加到已启用复制的 Azure VM 时，VM 的复制运行状况将显示警告。 该警告指出 VM 上的一个或多个磁盘可供保护。 可以为添加的磁盘启用复制。

- 如果为添加的磁盘启用保护，此警告会在初始复制后消失。
- 如果不为磁盘启用复制，则可以关闭该警告。
- 如果对已启用了添加的磁盘和复制的 VM 进行故障转移，则存在复制点。 复制点将显示可用于恢复的磁盘。

例如，假设 VM 具有单个磁盘，并且添加新磁盘。 在添加磁盘之前，可能创建了复制点。 此复制点将显示它由"2 个磁盘中的 1 个"组成。

Site Recovery 不支持将磁盘从复制的 VM 中“热删除”。 如果删除某个 VM 磁盘，则需先禁用该 VM 的复制，然后为其重新启用复制。

### <a name="how-often-can-i-replicate-to-azure"></a>可以多久复制到 Azure 一次？

将 Azure VM 复制到另一个 Azure 区域时，复制是持续性的。 有关详细信息，请参阅 [Azure 到 Azure 复制体系结构](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process)。

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>是否可以在某个区域中复制虚拟机？ 我需要此功能来迁移 VM。

不能使用 Azure 到 Azure 磁盘恢复解决方案在区域内复制 VM。

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>是否可以将 VM 实例复制到任何 Azure 区域？

通过使用站点恢复，可以在同一地理群集中的任何两个区域之间复制和恢复 VM。 地理群集的定义考虑到了数据延迟和主权。 有关详细信息，请参阅 Site Recovery 的[区域支持矩阵](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)。

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery 需要建立 Internet 连接？

否，站点恢复不需要互联网连接。 但它确实需要访问站点恢复 URL 和 IP 范围，如[Azure VM 灾难恢复中的网络中](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-urls)所述。

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>是否可以复制具有独立层的单独资源组的应用程序？

可以，您可以复制应用程序，并将灾难恢复配置保存在单独的资源组中。

例如，如果应用程序在单独的资源组中具有每个层的应用程序、数据库和 Web，则必须选择[复制向导](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication)三次以保护所有层。 站点恢复将复制这三个层到三个不同的资源组。

## <a name="replication-policy"></a>复制策略

### <a name="what-is-a-replication-policy"></a>什么是复制策略？

复制策略定义恢复点的保留历史记录的设置。 该策略还定义了应用一致的快照的频率。 默认情况下，Azure Site Recovery 使用以下默认设置创建新的复制策略：

- 恢复点历史记录的保留期为 24 小时。
- 应用一致性快照的频率为 60 分钟。

[了解有关复制设置 的更多内容](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings)。

### <a name="what-is-a-crash-consistent-recovery-point"></a>什么是崩溃一致性恢复点？

崩溃一致的恢复点具有磁盘上的数据，就像您在快照期间从服务器拔出电源线一样。 崩溃一致的恢复点不包括拍摄快照时内存中的任何内容。

目前，大多数应用程序都可以从崩溃一致性快照正常恢复。 对于无数据库的操作系统以及文件服务器、DHCP 服务器、打印服务器等应用程序而言，崩溃一致性恢复点通常已足够。

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>崩溃一致性恢复点生成的频率是多少？

Site Recovery 每隔 5 分钟创建崩溃一致性恢复点。

### <a name="what-is-an-application-consistent-recovery-point"></a>什么是应用程序一致性恢复点？

应用程序一致性恢复点是从应用程序一致性快照创建的。 应用程序一致性恢复点捕获与崩溃一致性快照相同的数据，同时捕获内存中的数据和正在处理的所有事务。

由于其额外的内容，应用程序一致的快照参与最多，使用时间最长。 我们建议对数据库操作系统以及 SQL Server 等应用程序使用应用程序一致性恢复点。

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>应用程序一致性恢复点对应用程序性能有何影响？

应用程序一致的恢复点捕获内存中和进程中的所有数据。 由于恢复点捕获该数据，因此它们需要 Windows 上的卷卷卷影复制服务等框架来静默应用程序。 如果捕获过程频繁，则当工作负载已繁忙时，可能会影响性能。 我们不建议对非数据库工作负载的应用一致性恢复点使用低频。 即使对于数据库工作负载，1 小时也就足够了。

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>应用程序一致性恢复点生成的最低频率是多少？

站点恢复可以创建一个应用程序一致的恢复点，最小频率为 1 小时。

### <a name="how-are-recovery-points-generated-and-saved"></a>如何生成和保存恢复点？

要了解站点恢复如何生成恢复点，让我们看一个复制策略的示例。 此复制策略具有具有 24 小时保留窗口和 1 小时应用一致频率快照的恢复点。

Site Recovery 每隔 5 分钟创建崩溃一致性恢复点。 您无法更改此频率。 对于最后一小时，您可以从 12 个崩溃一致性点和 1 个应用一致性点中进行选择。 随着时间的推移，站点恢复会修剪过去一小时之后的所有恢复点，每小时仅保存 1 个恢复点。

以下屏幕截图演示了该示例。 在屏幕截图中：

- 在过去一小时内，有频率为 5 分钟的恢复点。
- 超过过去一小时，站点恢复仅保留 1 个恢复点。

   ![生成的恢复点列表](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>可以恢复到哪个最早的时间点？

可以使用的最早恢复点是 72 小时。

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>我有 24 小时的复制策略。 如果问题阻止站点恢复生成恢复点超过 24 小时，会发生什么情况？ 以前的恢复点是否将丢失？

不会，Site Recovery 将保留以前的所有恢复点。 根据恢复点的保留窗口，站点恢复仅在生成新点时才替换最旧的点。 由于此问题，站点恢复无法生成任何新的恢复点。 在有新的恢复点之前，所有旧点都将在到达保留窗口后保留。

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>在 VM 上启用复制后，如何更改复制策略？

转到**站点恢复保管库** > **站点恢复基础结构** > **复制策略**。 选择要编辑的策略，并保存更改。 任何更改也会应用到现有的所有复制。

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>所有恢复点是包含 VM 的完整副本还是差异副本？

生成的第一个恢复点包含完整副本。 任何后续恢复点包含增量更改。

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>增大恢复点保留期是否会增加存储成本？

是的，如果将保留期从 24 小时增加到 72 小时，站点恢复将额外保存恢复点 48 小时。 增加的时间会产生存储费用。 例如，单个恢复点可能具有 10 GB 的增量更改，每 GB 成本为每月 0.16 美元。 额外费用为每月1.60美元~48美元。

## <a name="multi-vm-consistency"></a>多 VM 一致性

### <a name="what-is-multi-vm-consistency"></a>什么是多 VM 一致性？

多 VM 一致性可确保恢复点在所有复制的虚拟机之间保持一致。

站点恢复提供**多 VM 一致性**选项，该选项创建所有计算机的复制组。

当您故障转移虚拟机时，虚拟机将具有共享崩溃一致性和应用一致的恢复点。

完成本教程[以启用多 VM 一致性](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm)。

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>是否可以在多 VM 一致性复制组中对单个虚拟机进行故障转移？

选择 **"多 VM 一致性**"选项时，您指出应用程序依赖于组中的所有虚拟机。 不允许单个虚拟机故障转移。

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>我可以作为多 VM 一致性复制组的一部分复制多少虚拟机？

在一个复制组中，可以一同复制 16 个虚拟机。

### <a name="when-should-i-enable-multi-vm-consistency"></a>何时应启用多 VM 一致性？

由于多 VM 一致性占用 CPU，因此启用它可能会影响工作负载性能。 仅当计算机运行相同的工作负载并且需要跨多台计算机的一致性时，才使用多 VM 一致性。 例如，如果应用程序中有两个 SQL Server 实例和两个 Web 服务器，则仅应具有 SQL Server 实例的多 VM 一致性。

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>是否可以将已复制的 VM 添加到复制组？

在启用复制时，可以将 VM 添加到新的复制组。 您还可以在启用复制的同时将 VM 添加到现有复制组。 但是，您不能将已复制的 VM 添加到新的复制组或现有复制组。

## <a name="failover"></a>故障转移

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>如何确保 Azure VM 的目标区域中的容量？

站点恢复团队和 Azure 容量管理团队计划有足够的基础结构容量。 启动故障转移时，团队还有助于确保受站点恢复保护的 VM 实例将部署到目标区域。

### <a name="is-failover-automatic"></a>故障转移是自动发生的吗？

故障转移不是自动的。 您可以在门户中单击一下启动故障转移，也可以使用[PowerShell](azure-to-azure-powershell.md)触发故障转移。

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>故障转移后，我是否可以保留公共 IP 地址？

故障转移后，无法保留生产应用程序的公共 IP 地址。

在故障转移过程中启动工作负荷时，需要为工作负荷分配 Azure 公共 IP 资源。 Azure 公共 IP 资源必须在目标区域中可用。 您可以手动分配 Azure 公共 IP 资源，也可以使用恢复计划自动分配它。 了解如何[设置故障转移后的公共 IP 地址](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan)。

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>我可以在故障转移期间保留专用 IP 地址吗？

是的，可以保留专用 IP 地址。 默认情况下，为 Azure VM 启动灾难恢复时，Site Recovery 将根据源资源设置创建目标资源。 对于配置了静态 IP 地址的 Azure 虚拟机，站点恢复尝试为目标 VM 预配相同的 IP 地址（如果该地址未使用）。
了解如何[在故障转移期间保留 IP 地址](site-recovery-retain-ip-azure-vm-failover.md)。

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>故障转移后，为什么服务器分配了新的 IP 地址？

故障转移时，Site Recovery 会尽量提供 IP 地址。 如果该地址被另一个虚拟机占用，则 Site Recovery 会将下一个可用 IP 地址设为目标。

了解有关[为虚拟网络设置网络映射和 IP 寻址](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)的更多信息。

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>什么是**最新（最低 RPO）** 恢复点？

**"最新（最低 RPO）"** 选项首先处理已发送到站点恢复的所有数据。 服务处理数据后，它会为每个 VM 创建一个恢复点，然后再故障转移到 VM。 此选项提供最低的恢复点目标 (RPO)。 故障转移后创建的 VM 将从触发故障转移时将所有数据复制到站点恢复。

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>**最新（最低 RPO）** 恢复点是否影响故障转移 RTO？

是的。 站点恢复在故障通过之前处理所有挂起的数据，因此此选项比其他选项具有更高的恢复时间目标 （RTO）。

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>恢复点中的“最新处理”选项指的是什么？****

**"最新处理**"选项在计划中的所有 VM 上失败，到站点恢复处理的最新恢复点。 若要查看特定 VM 的最新恢复点，请检查 VM 设置中的“最新恢复点”。**** 此选项提供低的 RTO，因为无需费时处理未经处理的数据。

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>如果主要区域的服务意外中断，会出现什么情况？

可以在服务中断后触发故障转移。 站点恢复不需要来自主区域的连接即可执行故障转移。

### <a name="what-is-an-rto-of-a-vm-failover"></a>什么是 VM 故障转移的 RTO？

站点恢复具有[2 小时的 RTO SLA。](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) 但是，大多数时候，站点恢复在几分钟内通过虚拟机失败。 您可以通过访问故障转移作业来计算 RTO，该作业显示启动 VM 所花的时间。 有关恢复计划 RTO，请参阅下一节。

## <a name="recovery-plans"></a>恢复计划

### <a name="what-is-a-recovery-plan"></a>什么是恢复计划？

Site Recovery 中的恢复计划可以协调 VM 的故障转移恢复。 它有助于实现恢复的一致准确性、可重复性和自动化。 恢复计划满足以下需求：

- 定义一组可以一起故障转移的虚拟机
- 定义虚拟机之间的依赖关系，使应用程序能够适时启动
- 自动完成恢复以及自定义的手动操作，以便除虚拟机故障转移以外，还可以实现其他任务

[了解有关创建恢复计划的更多详细信息](site-recovery-create-recovery-plans.md)。

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>如何在恢复计划中实现定序？

在恢复计划中，可以创建多个组来实现定序。 每次故障转移一个组。 属于同一组的虚拟机一起故障转移，后跟另一组。 若要了解如何使用恢复计划为应用程序建模，请参阅[关于恢复计划](recovery-plan-overview.md#model-apps)。

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>如何找到恢复计划的 RTO？

若要检查恢复计划的 RTO，请对恢复计划执行测试故障转移，然后转到“Site Recovery 作业”。****
在下面的示例中，请参阅作业**SAPTest 恢复计划**。 作业需要 8 分 59 秒才能故障接管所有虚拟机并执行指定的操作。

![Site Recovery 作业列表](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>是否可将自动化 Runbook 添加到恢复计划？

是的，可将 Azure 自动化 Runbook 集成到恢复计划中。 了解有关添加[Azure 自动化运行簿](site-recovery-runbook-automation.md)的更多详细信息。

## <a name="reprotection-and-failback"></a>重新保护和故障回复

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>我从主区域故障转移到灾难恢复区域。 DR 区域中的 VM 是自动保护的吗？

不是。 将 Azure VM 从一个区域[故障转移](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback)到另一个区域后，VM 将在灾难恢复区域中启动，但处于不受保护状态。 若要将 VM 故障回复到主要区域，需要[重新保护](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect)次要区域中的 VM。

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>重新保护时，Site Recovery 是否将完整的数据从次要区域复制到主要区域？

这取决于具体的情况。 如果源区域 VM 存在，则仅同步源磁盘和目标磁盘之间的更改。 Site Recovery 将通过比较磁盘来计算差异，然后传输数据。 此过程通常需要几个小时。 有关重新保护期间发生的情况的详细信息，请参阅[将 Azure VM 实例故障重新保护到主区域](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection)。

### <a name="how-much-time-does-it-take-to-fail-back"></a>故障回复需要多长时间？

重新保护后，故障恢复所需的时间大约与从主区域故障转移到辅助区域所需的时间大致相同。

## <a name="capacity"></a><a name="capacity"></a>容量

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>如何确保 Azure VM 的目标区域中的容量？

站点恢复团队和 Azure 容量管理团队计划有足够的基础结构容量。 启动故障转移时，团队还有助于确保受站点恢复保护的 VM 实例将部署到目标区域。

### <a name="does-site-recovery-work-with-reserved-instances"></a>站点恢复是否与保留实例一起工作？

可以，您可以在灾难恢复区域购买保留的[Azure VM，](https://azure.microsoft.com/pricing/reserved-vm-instances/)站点恢复故障转移操作将使用它们。 不需要任何其他配置。

## <a name="security"></a>安全性

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>复制数据是否会发送到 Site Recovery 服务？

否，站点恢复不会截获复制的数据，并且没有关于 VM 上运行的内容的任何信息。 只有协调复制与故障转移所需的元数据将发送到站点恢复服务。

站点恢复是 ISO 27001：2013、27018、HIPAA 和 DPA 认证。 该服务正在进行SOC2和FedRAMP JAB评估。

### <a name="does-site-recovery-encrypt-replication"></a>站点恢复是否将复制数据加密？

是的，支持在 Azure[中进行传输中的加密和静态加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)。

## <a name="next-steps"></a>后续步骤

- [查看 Azure 到 Azure 支持要求](azure-to-azure-support-matrix.md)。
- [设置 Azure 到 Azure 复制](azure-to-azure-tutorial-enable-replication.md)。
- 如果在阅读本文后有任何问题，请在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)上发布问题。
