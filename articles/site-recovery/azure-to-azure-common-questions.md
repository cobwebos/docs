---
title: 常见问题 - 使用 Azure Site Recovery 进行 Azure 到 Azure 的灾难恢复 | Microsoft Docs
description: 本文汇总了使用 Azure Site Recovery 设置将 Azure VM 灾难恢复到另一个 Azure 区域时出现的常见问题
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 6fe7152e43640a809ab9f4de39b1c6b599975a20
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969941"
---
# <a name="common-questions---azure-to-azure-replication"></a>常见问题 - Azure 到 Azure 的复制

本文提供将 Azure VM 的灾难恢复部署到另一个 Azure 区域时可能遇到的常见问题的解答。 如果在阅读本文后有任何问题，请在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)上发布问题。


## <a name="general"></a>常规
### <a name="how-is-site-recovery-priced"></a>Site Recovery 如何计费？
请查看 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)。

### <a name="how-to-configure-site-recovery-on-azure-vms-what-are-the-best-practices"></a>如何在 Azure VM 上配置 Site Recovery。 最佳做法是什么？
1. [了解 Azure 到 Azure 体系结构](azure-to-azure-architecture.md)
2. [查看支持和不支持的配置](azure-to-azure-support-matrix.md)
3. [为 Azure VM 设置灾难恢复](azure-to-azure-how-to-enable-replication.md)
4. [运行测试故障转移](azure-to-azure-tutorial-dr-drill.md)
5. [故障转移和故障回复到主要区域](azure-to-azure-tutorial-failover-failback.md)

## <a name="replication"></a>复制

### <a name="can-i-replicate-azure-disk-encryption-enabled-vms"></a>是否可以复制已启用 Azure 磁盘加密的 VM？
是的，可以复制此类 VM。 请参阅[此文](azure-to-azure-how-to-enable-replication-ade-vms.md)，为已启用 Azure 磁盘加密 (ADE) 的 VM 启用复制。 请注意，Azure Site Recovery 目前仅支持运行 Windows OS 且已使用 Azure AD 应用启用加密的 Azure VM。

### <a name="can-i-replicate-vms-to-another-subscription"></a>是否可将 VM 复制到另一个订阅？
是的，可将 Azure VM 复制到同一 Azure Active Directory 租户中的不同订阅。
配置[跨订阅](https://azure.microsoft.com/blog/cross-subscription-dr)的灾难恢复非常简单。 可以在复制时选择另一个订阅。

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>是否可将区域固定的 Azure VM 复制到另一个区域。
是的，可[将区域固定的 VM 复制到](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region)另一个区域。

### <a name="can-i-exclude-disks"></a>是否可以排除磁盘？

是的，可以在保护时使用 PowerShell 排除磁盘。 请参阅 [PowerShell 指南](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine)来排除磁盘

###<a name="how-often-can-i-replicate-to-azure"></a>可以多久复制到 Azure 一次？
将 Azure VM 复制到另一个 Azure 区域时，复制是持续性的。 请查看 [Azure 到 Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process) 复制体系结构了解详细信息。

### <a name="can-i-replicate-virtual-machines-within-a-same-region-i-need-this-to-migrate-vms"></a>是否可以在同一区域中复制虚拟机？ 我需要使用此方法来迁移 VM。
无法使用 Azure 到 Azure 的灾难恢复解决方案在同一区域中复制 VM。

### <a name="can-i-replicate-vms-to-any-azure-region"></a>是否可将 VM 复制到任意 Azure 区域？
使用 Site Recovery，可以在同一地理群集中的任意两个区域之间复制和恢复 VM。 在定义地理群集时请注意数据延迟和主权。 有关详细信息，请参阅 Site Recovery 的[区域支持矩阵](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)。

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery 需要建立 Internet 连接？

否，Site Recovery 不需要建立 Internet 连接，但需要访问 Site Recovery URL 和 IP 范围，如[此文](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)中所述

## <a name="replication-policy"></a>复制策略

### <a name="what-is-a-replication-policy"></a>什么是复制策略？
定义恢复点保留期历史记录和应用一致性快照频率的设置。 默认情况下，Azure Site Recovery 会使用恢复点保留期为“24 小时”、应用一致性快照频率为“60 分钟”的默认设置创建新的复制策略。

### <a name="what-is-crash-consistent-recovery-point"></a>什么是崩溃一致性恢复点？
崩溃一致性恢复点表示在 VM 发生崩溃或者创建快照期间从服务器上拔下电源线时磁盘上的数据。 它不包括创建快照时内存中存在的任何数据。 目前，大多数应用程序都可以从崩溃一致性快照正常恢复。 对于无数据库的操作系统以及文件服务器、DHCP 服务器、打印服务器等应用程序而言，崩溃一致性恢复点通常已足够。

### <a name="what-is-application-consistent-recovery-point"></a>什么是应用程序一致性恢复点？ 
应用程序一致性恢复点是从应用一致性快照创建的，这些快照捕获的数据与崩溃一致性快照相同，此外还会加上内存中的数据，以及所有正在进行的事务。 由于包含额外的内容，应用程序一致性快照涉及的操作最多，且执行时间最长。 建议对数据库操作系统以及 SQL 等应用程序使用应用程序一致性恢复点。

### <a name="how-are-recovery-points-generated-and-saved"></a>如何生成和保存恢复点？
若要了解 Site Recovery 如何生成恢复点，让我们查看一个复制策略的示例，其中，恢复点保留期为 24 小时，应用一致性快照的频率为 1 小时。
Site Recovery 每隔 5 分钟创建崩溃一致性恢复点，用户无法控制和更改此频率。 因此，在过去一小时，用户可以从 12 个崩溃一致性恢复点和 1 个应用一致性恢复点中进行选择。 一段时间后，Site Recovery 将删除 1 小时以前的所有恢复点，每小时只保存 1 个恢复点。
以下屏幕截图对此做了演示：


1. 在过去 1 小时内，以 5 分钟的频率创建了恢复点。
2. 对于超出 1 小时的期限，我们可以看到，每小时只保留了 1·个恢复点。

  ![恢复点生成](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>可以恢复到哪个最早的时间点？
可以使用的最早恢复点是 72 小时。

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-there-is-no-recovery-points-generation-due-to-some-issue-for-more-than-24-hours-does-my-previous-recovery-points-will-be-pruned"></a>如果我的复制策略是创建 24 小时的恢复点，但出于某种问题，有 24 小时以上未生成恢复点，将会发生什么情况？ 以前的恢复点是否被删除？
不会，在这种情况下，Site Recovery 将保留以前的所有恢复点。 

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>在 VM 上启用复制后，如何更改复制策略？ 
转到“Site Recovery 保管库”>“Site Recovery 基础结构”>“复制策略”。 选择要编辑的策略并保存所做的更改。 任何更改也会应用到现有的所有复制。 

### <a name="are-all-the-recovery-points-complete-copy-of-the-vm-or-differential"></a>所有恢复点是包含 VM 的完整副本还是差异副本？
在初始复制期间，生成的第一个恢复点将包含完整副本，任何后续恢复点将包含增量更改。

### <a name="does-increasing-recovery-points-retention-windows-increases-the-storage-cost"></a>增大恢复点保留期是否会增加存储成本？
是的，如果将保留期从 24 小时增大到 72 小时，则 Site Recovery 将保存额外 48 小时的恢复点，这会产生存储费用。 例如，如果单个恢复点包含 10 GB 的增量更改，每 GB 费用为 0.16 美元/月，则每月会产生 1.6 美元 * 48 的额外费用。

## <a name="failover"></a>故障转移

### <a name="is-failover-automatic"></a>故障转移是自动发生的吗？

故障转移不是自动的。 可以在门户中单击一下鼠标来启动故障转移，或者使用 Site Recovery [PowerShell](azure-to-azure-powershell.md) 来触发故障转移。 

### <a name="can-i-retain-public-ip-address-after-failover"></a>是否可以在故障转移后保留公共 IP 地址？

生产应用程序的公共 IP 地址不能在故障转移中保留。 作为故障转移过程的一部分启动的工作负载必须向其分配在目标区域可用的 Azure 公共 IP 资源。 此步骤可手动完成或使用恢复计划自动执行。 请参阅[此文](https://docs.microsoft.com/azure/site-recovery/concepts-public-ip-address-with-site-recovery#public-ip-address-assignment-using-recovery-plan)了解如何使用恢复计划来分配公共 IP 地址。  

### <a name="can-i-retain-private-ip-address-during-failover"></a>在故障转移期间是否可以保留专用 IP 地址？
是的，可以保留专用 IP 地址。 默认情况下，为 Azure VM 启动灾难恢复时，Site Recovery 将根据源资源设置创建目标资源。 对于配置有静态 IP 地址的 Azure VM，Site Recovery 将尝试对目标 VM 预配相同的 IP 地址（如果未占用）。 请参阅[此文](site-recovery-retain-ip-azure-vm-failover.md)，了解如何在不同的条件下保留专用 IP 地址。

### <a name="after-failover-the-server-does-not-have-the-same-ip-address-as-the-source-vm-why-is-it-assigned-with-a-new-ip-address"></a>故障转移后，服务器的 IP 地址与源 VM 不同。 为何要为服务器分配新的 IP 地址？

故障转移时，Site Recovery 会尽最大努力提供 IP 地址。 如果该 IP 地址被其他某个虚拟机占用，则将下一个可用 IP 地址设为目标。 有关 Site Recovery 如何处理寻址的完整介绍，请[查看此文](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-network-mapping#set-up-ip-addressing-for-target-vms)。

### <a name="what-does-latestlowest-rpo-recovery-points-means"></a>最新（最低 RPO）恢复点指的是什么？
此选项首先处理已发送到 Site Recovery 服务的所有数据，为每台 VM 创建恢复点，然后将 VM 故障转移到该恢复点。 此选项提供最低的 RPO（恢复点目标），因为故障转移后创建的 VM 具有触发故障转移时复制到 Site Recovery 的所有数据。

### <a name="does-latest-lowest-rpo-recovery-points-have-impact-on-failover-rto"></a>最新（最低 RPO）恢复点是否影响故障转移 RTO？
是的，由于 Site Recovery 在故障转移之前需要处理所有挂起的数据，此选项的 RTO 比其他选项更高。

### <a name="what-does-latest-processed-option-in-recovery-points-mean"></a>恢复点中的“最新处理”选项指的是什么？
此选项将计划中的所有 VM 故障转移到由 Site Recovery 处理的最新恢复点。 若要查看特定 VM 的最新恢复点，请检查 VM 设置中的“最新恢复点”。 此选项提供低 RTO（恢复时间目标），因为无需费时处理未经处理的数据。

### <a name="if-im-replicating-between-two-azure-regions-what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>如果我在两个 Azure 区域之间进行复制，当我的主要区域发生意外的服务中断时，会出现什么情况？
可以在服务中断后触发故障转移。 Site Recovery 不需要从主要区域建立连接即可执行故障转移。

## <a name="recovery-plan"></a>恢复计划

### <a name="what-is-a-recovery-plan-"></a>什么是恢复计划？
Site Recovery 中的恢复计划可以协调 VM 的故障转移恢复。 它有助于实现恢复的一致准确性、可重复性和自动化。 恢复计划可为用户解决以下需求：

- 定义一组可以一起故障转移的虚拟机。
- 定义虚拟机之间的依赖关系，使应用程序能够适时启动。
- 自动完成恢复以及自定义的手动操作，以便除虚拟机故障转移以外，还可以实现其他任务。

[详细了解](site-recovery-create-recovery-plans.md)恢复计划。

### <a name="how-does-sequencing-is-achieved-in-a-recovery-plan"></a>如何在恢复计划中实现定序？

在恢复计划中，可以创建多个组来实现定序。 每次故障转移一个组，即，同一个组中的 VM 将一起故障转移，然后再故障转移另一个组中的 VM。 查看如何[使用恢复计划为应用程序建模](https://review.docs.microsoft.com/azure/site-recovery/recovery-plan-overview?branch=pr-en-us-61681#model-apps)。 

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>如何找到恢复计划的 RTO？
若要检查恢复计划的 RTO，请对恢复计划进行测试故障转移，然后转到“Site Recovery 作业”。
在以下示例中，SAP 测试恢复计划花费了 8 分 59 秒来故障转移所有虚拟机和执行指定的所有操作。

  ![com-error](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>是否可将自动化 Runbook 添加到恢复计划？
是的，可将 Azure 自动化 Runbook 集成到恢复计划中。 [了解详细信息](site-recovery-runbook-automation.md)

## <a name="reprotect-and-failback"></a>重新保护和故障回复 

### <a name="after-doing-a-failover-from-primary-region-to-disaster-recovery-region-does-vms-in-a-dr-region-gets-protected-automatically"></a>从主要区域故障转移到灾难恢复区域后，灾难恢复区域中的 VM 是否自动受到保护？
不会，将 Azure VM 从一个区域[故障转移](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback)到另一个区域后，VM 将在灾难恢复区域中启动，但处于不受保护状态。 若要将 VM 故障回复到主要区域，需要[重新保护](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect)次要区域中的 VM。

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-secondary-region-to-primary-region"></a>重新保护时，Site Recovery 是否将完整的数据从次要区域复制到主要区域？
这取决于具体的情况，例如，如果源区域 VM 存在，则只会同步源磁盘与目标磁盘之间的更改。 将通过比较两个磁盘来计算差异，然后传输这些差异。 这通常需要几个小时才能完成。 请参阅[此文]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection)，详细了解重新保护期间发生的情况。

### <a name="how-much-time-does-it-take-to-failback"></a>故障回复需要多长时间？
完成重新保护后，故障回复所需的时间通常类似于从主要区域故障转移到次要区域所需的时间。 

## <a name="security"></a>安全
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>复制数据是否会发送到 Site Recovery 服务？
否。Site Recovery 不会拦截复制的数据，也不包含虚拟机上运行的组件的任何相关信息。 只有协调复制与故障转移所需的元数据将发送到站点恢复服务。  
站点恢复已通过 ISO 27001:2013、27018、HIPAA、DPA 认证，目前正在接受 SOC2 和 FedRAMP JAB 评估。

### <a name="does-site-recovery-encrypt-replication"></a>站点恢复是否将复制数据加密？
是的，传输中加密和 [Azure 中加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)均受支持。

## <a name="next-steps"></a>后续步骤
* [查看](azure-to-azure-support-matrix.md)支持要求。
* [设置](azure-to-azure-tutorial-enable-replication.md) Azure 到 Azure 的复制。
