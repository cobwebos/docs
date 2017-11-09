---
title: "使用 Azure Site Recovery 针对 Azure 次要区域的 Azure VM 运行灾难恢复演练（预览）"
description: "了解如何使用 Azure Site Recovery 服务针对 Azure 次要区域的 Azure VM 运行灾难恢复演练。"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5bcd3d64714951508d984c17326e845ae4842670
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="run-a-disaster-recovery-drill-for-azure-vms-to-a-secondary-azure-region-preview"></a>针对 Azure 次要区域的 Azure VM 运行灾难恢复演练（预览）

[Azure Site Recovery](site-recovery-overview.md) 服务通过在计划内和计划外停机期间使商业应用程序保持启动和运行可用状态，有助于实施业务连续性和灾难恢复 (BCDR) 策略。 Site Recovery 管理并安排本地计算机和 Azure 虚拟机 (VM) 的灾难恢复，包括复制、故障转移和恢复。

本教程演示如何使用测试故障转移从一个 Azure 区域到另一个 Azure 区域对 Azure VM 运行灾难恢复演练。 演练如何在不丢失数据或不停机的情况下验证复制策略，并且不影响生产环境。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 检查先决条件
> * 为单个 VM 运行测试故障转移

## <a name="prerequisites"></a>先决条件

- 在运行测试故障转移前，我们建议先验证 VM 属性以确保一切按预期进行。  访问“复制项”中的 VM 属性。 “概要”边栏选项卡显示有关计算机设置和状态的信息。
- 建议使用单独的 Azure VM 网络进行测试故障转移，而不是使用启用复制时设置的默认网络。


## <a name="run-a-test-failover"></a>运行测试故障转移

1. 在“设置” > “复制的项”中，单击“VM”>“+测试故障转移”。

2. 在“测试故障转移”中，选择要用于故障转移的恢复点：

   - **最新处理**：将 VM 故障转移到由 Site Recovery 处理的最新恢复点。 将显示时间戳。 使用此选项时，无需费时处理数据，因此 RTO（恢复时间目标）会较低
   - **最新的应用一致**：此选项将所有 VM 故障转移到最新的应用一致恢复点。 将显示时间戳。
   - **自定义**：选择任何恢复点。

3. 选择辅助区域中的 Azure VM 在故障转移后要连接到的目标 Azure 虚拟网络。

4. 若要启动故障转移，请单击“确定”。 若要跟踪进度，请单击 VM 以打开其属性。 或者，可以在保管库名称 >“设置” > “作业” > “Site Recovery 作业”中单击“测试故障转移”作业。
5. 故障转移完成后，副本 Azure VM 会显示在 Azure 门户 >“虚拟机”中。 请确保 VM 正在运行、大小适当并已连接到相应的网络。
6. 若要删除在测试故障转移期间创建的 VM，请单击复制项或恢复计划上的“清理测试故障转移”。 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [运行生产故障转移](azure-to-azure-tutorial-failover-failback.md)
