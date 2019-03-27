---
title: 使用 Azure Site Recovery 服务对复制到 Azure 次要区域的 Azure IaaS VM 进行故障回复，以便实现灾难恢复。
description: 了解如何使用 Azure Site Recovery 服务对 Azure VM 进行故障回复。
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: sideeksh
ms.custom: mvc
ms.openlocfilehash: d8721f313907f0e0519dca52f5565853f1c44110
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58089687"
---
# <a name="fail-back-azure-vms-between-azure-regions"></a>在 Azure 区域之间对 Azure VM 进行故障回复

[Azure Site Recovery](site-recovery-overview.md) 服务可管理和协调本地计算机和 Azure 虚拟机 (VM) 的复制、故障转移和故障回复，因而有利于灾难恢复策略。

本教程介绍如何故障回复单个 Azure VM。 故障转移后，可在主要区域可用时故障回复到主要区域。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> 
> * 故障回复辅助 VM
> * 重新保护主 VM，以便它复制回次要区域
> 
> [!NOTE]
> 
> 本教程旨在引导用户完成相关步骤，以便在尽量减少自定义的情况下故障转移到目标区域并进行故障回复；如果需要详细了解与故障转移相关联的各个方面（包括网络注意事项、自动化或故障排除），请参阅适用于 Azure VM 的“操作方法”下面的文档。

## <a name="prerequisites"></a>先决条件

> * 确保 VM 处于“故障转移已提交”状态，检查主要区域是否可用，并且你是否能够在其中创建和访问新资源。
> * 请确保启用重新保护。

## <a name="fail-back-to-the-primary-region"></a>故障回复到主要区域

VM 重新受保护后，可以根据需要故障回复到主要区域。

1. 转到恢复服务保管库。 单击“复制的项”，然后选择已重新保护的 VM。

2. 应看到以下内容。 请注意，它类似于测试故障转移的边栏选项卡，并从主要区域进行故障转移。
![故障回复到主要区域](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. 单击“测试故障转移”，以执行测试故障转移回复到主要区域。 为测试故障转移选择“恢复点”和“虚拟网络”，然后选择“确定”。 可以看到在主要区域中创建的测试 VM，你可以对其进行访问和检查。

4. 如果测试故障转移令人满意，可以单击“清除测试故障转移”以清除在源区域中创建的资源以进行测试故障转移。

5. 在“复制的项”中，选择要故障转移的 VM >“故障转移”。

6. 在“故障转移”中，选择要故障转移到其中的“恢复点”。 可以使用以下选项之一：
    1. 最新（默认值）：此选项处理 Site Recovery 服务中的所有数据，并提供最低的恢复点目标 (RPO)
    2. 最新处理：此选项将虚拟机还原到已由 Site Recovery 服务处理的最新恢复点
    3. 自定义：使用此选项故障转移到特定恢复点。 此选项可用于执行测试故障转移

7. 如果希望 Site Recovery 在触发故障转移之前尝试关闭源虚拟机，请选择“在开始故障转移之前关闭计算机”。 即使关机失败，故障转移也仍会继续。 请注意，Site Recovery 不在故障转移之后清理源。

8. 在“作业”页上跟踪故障转移进度

9. 故障转移后，请登录到虚拟机来对它进行验证。 如果想要转到虚拟机的另一个恢复点，可以使用“更改恢复点”选项。

10. 如果故障转移后的虚拟机符合要求，可以“提交”故障转移。 提交会删除该服务提供的所有恢复点。 “更改恢复点”选项不再可用。

![主要和次要区域的 VM](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

如果你看到前面的屏幕截图，则“ContosoWin2016”VM 已从美国中部故障转移到美国东部，然后从美国东部故障回复到美国中部。

请注意，DR VM 将保持关闭解除分配状态。 此行为是设计使然，因为 Azure Site Recovery 会保存虚拟机的信息，这可能有助于以后从主要区域故障转移到次要区域。 你不需要为已解除分配的虚拟机付费，因此它应保持原样。

> [!NOTE]
> 请参阅[“操作方法”部分](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection)，了解有关重新保护工作流和重新保护期间发生的情况的更多详细信息。
