---
title: "使用 Azure Site Recovery 为 Azure VM 复制运行测试故障转移 | Microsoft Docs"
description: "总结了使用 Azure Site Recovery 服务针对到另一 Azure 区域的 Azure VM 复制运行测试故障转移所需的步骤。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: e15c1b0c-5d75-4fdf-acb0-e61def9e9339
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 8babb0d016729f318442af93596d206c38d91206
ms.contentlocale: zh-cn
ms.lasthandoff: 08/02/2017

---

# <a name="step-6-run-a-test-failover-for-azure-vm-replication"></a>步骤 6：针对 Azure VM 复制运行测试故障转移

为 Azure 虚拟机 (VM) 启用复制后，根据本文中的步骤使用 Azure 门户中的 [Azure Site Recovery](site-recovery-overview.md) 服务运行从一个 Azure 区域到另一个区域的测试故障转移。

- 完成本文后，你应当已经验证了测试故障转移，至少一个 Azure VM 可以故障转移到你的辅助 Azure 区域。 
- 请将任何评论发布到本文底部，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提问。

>[!NOTE]
>
> Azure VM 复制目前处于预览版。


## <a name="before-you-start"></a>开始之前

- 在运行测试故障转移之前，我们建议你验证 VM 属性，并进行任何所需的更改。 可以在“复制的项”中查看 VM 属性。 “概要”边栏选项卡显示有关计算机设置和状态的信息。
- 建议你使用单独的 Azure VM 网络进行测试故障转移，而不是使用为生产故障转移设置的网络（默认或自定义）。
- 测试故障转移将 Azure VM（及其存储）故障转移到辅助 Azure 区域。 它不复制所依赖的任何应用或资源。 如果在故障转移后的 VM 上运行的应用依赖于其他资源，例如 Active Directory 或 DNS，则你还需要复制这些资源（如果它们在辅助区域中尚不可用）。 [了解详细信息](site-recovery-test-failover-to-azure.md#prepare-active-directory-and-dns)
- 如果你希望在从本地站点进行故障转移后访问复制的 VM，则需要[为连接到这些 VM 做准备](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)。

## <a name="run-a-test-failover"></a>运行测试故障转移

1. 在“设置” > “复制的项”中，单击“VM”>“+测试故障转移”。 

2. 在“测试故障转移”中，选择要用于故障转移的恢复点：

    - **最新处理**：将 VM 故障转移到由 Site Recovery 处理的最新恢复点。 将显示时间戳。 使用此选项时，不会花费时间处理数据，因此它提供了低 RTO（恢复时间目标）。
    - **最新的应用一致**：此选项将所有 VM 故障转移到最新的应用一致恢复点。 将显示时间戳。 
    - **自定义**：选择任何恢复点。
 
3. 选择辅助区域中的 Azure VM 在故障转移后要连接到的目标 Azure 虚拟网络。
4. 若要启动故障转移，请单击“确定”。 若要跟踪进度，请单击 VM 以打开其属性。 或者，可以在保管库名称 >“设置” > “作业” > “Site Recovery 作业”中单击“测试故障转移”作业。
5. 故障转移完成后，副本 Azure VM 会显示在 Azure 门户 >“虚拟机”中。 请确保 VM 的大小适当、已连接到相应的网络，并且正在运行。
6. 若要删除在测试故障转移期间创建的 VM，请单击复制项或恢复计划上的“清理测试故障转移”。 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。 

[详细了解](site-recovery-test-failover-to-azure.md)测试故障转移。

## <a name="next-steps"></a>后续步骤

在测试完故障转移后，本演练完成。 现在，请了解在生产环境中运行故障转移：

- [详细了解](site-recovery-failover.md)不同类型的故障转移，以及如何运行它们。
- 详细了解[使用恢复计划](site-recovery-create-recovery-plans.md)对多个 VM 进行故障转移。
- 详细了解如何[使用恢复计划](site-recovery-create-recovery-plans.md)。
- 详细了解如何在故障转移后[重新保护 Azure VM](site-recovery-how-to-reprotect.md)。


