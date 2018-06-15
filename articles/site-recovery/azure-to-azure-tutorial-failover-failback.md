---
title: 使用 Azure Site Recovery 对复制到 Azure 次要区域的 Azure VM 进行故障转移和故障回复
description: 了解如何使用 Azure Site Recovery 对复制到 Azure 次要区域的 Azure VM 进行故障转移和故障回复
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/31/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: cb9bfd000c377f63e99350d0615a7e92ba8bb8ef
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716238"
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions"></a>在 Azure 区域之间故障转移和故障回复 Azure VM

[Azure Site Recovery](site-recovery-overview.md) 服务可管理和协调本地计算机和 Azure 虚拟机 (VM) 的复制、故障转移和故障回复，进而有利于灾难恢复策略。

本教程介绍如何将单个 Azure VM 故障转移到 Azure 次要区域。 故障转移后，可在主要区域可用时故障回复到主要区域。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 故障转移 Azure VM
> * 重新保护辅助 Azure VM，以便它复制到主要区域
> * 故障回复辅助 VM
> * 重新保护主 VM，以便它复制回次要区域



## <a name="prerequisites"></a>先决条件

- 确保已完成[灾难恢复演练](azure-to-azure-tutorial-dr-drill.md)，检查所有内容是否都按预期工作。
- 在运行测试故障转移之前，验证 VM 属性。 该 VM 必须符合 [Azure 要求](azure-to-azure-support-matrix.md#support-for-replicated-machine-os-versions)。

## <a name="run-a-failover-to-the-secondary-region"></a>运行到次要区域的故障转移

1. 在“已复制项”中，选择要故障转移的 VM >“故障转移”

   ![故障转移](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. 在“故障转移”中，选择要故障转移到的“恢复点”。 可以使用以下选项之一：

   * **最新**（默认值）：此选项处理 Site Recovery 服务中的所有数据，并提供最低的恢复点目标 (RPO)。
   * **最新处理**：此选项将虚拟机还原到已由 Site Recovery 服务处理的最新恢复点。
   * **自定义**：使用此选项故障转移到特定恢复点。 此选项可用于执行测试故障转移。

3. 如果希望 Site Recovery 在触发故障转移之前尝试关闭源虚拟机，请选择“在开始故障转移之前关闭计算机”。 即使关机失败，故障转移也仍会继续。

4. 在“作业”页上跟踪故障转移进度。

5. 故障转移后，请登录到虚拟机来对它进行验证。 如果想要转到虚拟机的另一个恢复点，可以使用“更改恢复点”选项。

6. 如果故障转移后的虚拟机符合要求，可以**提交**故障转移。
   提交会删除该服务提供的所有恢复点。 “更改恢复点”选项不再可用。

## <a name="reprotect-the-secondary-vm"></a>重新保护辅助 VM

故障转移 VM 之后，需要重新保护它，以便它复制回主要区域。

1. 确保 VM 处于“故障转移已提交”状态，检查主要区域是否可用，并且你是否能够在其中创建和访问新资源。
2. 在“保管库” > “已复制项”中，右键单击已故障转移的 VM，并选择“重新保护”。

   ![右键单击以重新保护](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. 请注意，已选择的保护方向为次要区域到主要区域。
3. 查看“资源组、网络、存储和可用性集”信息。 任何标记为“新”的资源将在重新保护操作中创建。
4. 单击“确定”触发重新保护作业。 此作业使用最新数据植入目标站点。 然后，它将增量复制到主要区域。 该 VM 现在处于受保护状态。

## <a name="fail-back-to-the-primary-region"></a>故障回复到主要区域

VM 重新受保护后，可以根据需要故障回复到主要区域。 若要执行此操作，请按照[故障转移](#run-a-failover)说明进行操作。
