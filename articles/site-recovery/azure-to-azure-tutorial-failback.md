---
title: 使用 Azure Site Recovery 服务对复制到 Azure 次要区域的 Azure VM 进行故障回复，以实现灾难恢复。
description: 了解如何使用 Azure Site Recovery 服务对 Azure VM 进行故障回复。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a3b67e9b0dc41eeb14000400912892fbf29acfe2
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399495"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>在 Azure 区域之间对 Azure VM 进行故障回复

[Azure Site Recovery](site-recovery-overview.md) 服务可管理和协调本地计算机和 Azure 虚拟机 (VM) 的复制、故障转移和故障回复，因而有利于灾难恢复策略。

本教程介绍如何故障回复单个 Azure VM。 故障转移后，须在主要区域可用时故障回复到主要区域。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> 
> * 对次要区域中的 VM 进行故障回复。
> * 重新保护主 VM，以便它复制回次要区域。
> 
> [!NOTE]
> 
> 本教程可帮助你在只需进行极少量的自定义操作的情况下，将多个 VM 故障转移到目标区域，然后故障回复到源区域。 有关更深入的说明，请查看 [Azure VM 的操作指南](https://docs.microsoft.com/azure/virtual-machines/windows/)。

## <a name="before-you-start"></a>开始之前

* 确保 VM 的状态为“故障转移已提交”  。
* 检查主要区域是否可用，并且你是否能够在其中创建和访问新资源。
* 请确保启用重新保护。

## <a name="fail-back-to-the-primary-region"></a>故障回复到主要区域

重新保护 VM 后，可根据需要故障回复到主要区域。

1. 在保管库中选择“复制的项”，然后选择已重新保护的 VM  。

    ![故障回复到主要区域](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. 选择“测试故障转移”，以执行恢复到主要区域的测试故障转移  。
4. 选择用于测试故障转移的恢复点和虚拟网络，然后选择“确定”  。 可以查看主要区域中创建的测试 VM。
5. 成功完成测试故障转移后，选择“清理测试故障转移”，以清理在源区域中为测试故障转移创建的资源  。
6. 在“复制的项”中选择 VM，然后选择“故障转移”   。
7. 在“故障转移”中，选择要故障转移到的恢复点  ：
    - **最新(默认设置)** ：处理 Site Recovery 服务中的所有数据，并提供最低的恢复点目标 (RPO)。
    - **最新处理**：将 VM 还原到由 Site Recovery 处理的最新恢复点。
    - **自定义**：故障转移到特定的恢复点。 此选项可用于执行测试故障转移。

8. 如果希望 Site Recovery 在触发故障转移之前尝试关闭源 VM，请选择“在开始故障转移前关闭计算机”  。 即使关机失败，故障转移也仍会继续。 请注意，在故障转移后，Site Recovery 不会清理源。
9. 在“作业”页上跟踪故障转移进度。 
10. 故障转移完成后，请登录到 VM 来对它进行验证。 可根据需要更改恢复点。
11. 验证故障转移后，选择“提交故障转移”  。 提交操作会删除所有可用的恢复点。 “更改恢复点”选项不再可用。
12. VM 应显示为已故障转移并已故障回复。

    ![主要和次要区域的 VM](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> 灾难恢复 VM 将保留为关闭/已解除分配状态。 这是设计使然，因为 Site Recovery 会保存 VM 信息。以后从主要区域故障转移到次要区域时，这些信息可能很有用。 你不需要为解除分配的 VM 付费，因此它们应保持原样。

## <a name="next-steps"></a>后续步骤

[详细了解](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection)重新保护工作流。
