---
title: 使用 Azure Site Recovery 运行 Azure VM 灾难恢复演练
description: 了解如何使用 Azure Site Recovery 服务针对 Azure VM 的次要区域运行灾难恢复演练。
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166179"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>对 Azure VM 的次要区域运行灾难恢复演练

[Azure Site Recovery](site-recovery-overview.md) 服务通过在计划内和计划外停机期间使商业应用程序保持启动和运行可用状态，有助于实施业务连续性和灾难恢复 (BCDR) 策略。 Site Recovery 管理并安排本地计算机和 Azure 虚拟机 (VM) 的灾难恢复，包括复制、故障转移和恢复。

本教程演示如何使用测试故障转移从一个 Azure 区域到另一个 Azure 区域对 Azure VM 运行灾难恢复演练。 演练如何在不丢失数据或不停机的情况下验证复制策略，并且不影响生产环境。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 检查先决条件
> * 为单个 VM 运行测试故障转移

> [!NOTE]
> 本教程将帮助你以最少的步骤执行灾难恢复演练。 若要详细了解与执行灾难恢复演练相关的各种功能，请参阅与 Azure VM [复制](azure-to-azure-how-to-enable-replication.md)、[网络](azure-to-azure-about-networking.md)、[自动化](azure-to-azure-powershell.md)或[故障排除](azure-to-azure-troubleshoot-errors.md)相关的文档。

## <a name="prerequisites"></a>必备条件

在尝试学习本教程之前，请检查以下各项：

- 在运行测试故障转移之前，建议你检查 VM 的属性，以确保为其配置灾难恢复。 转到 VM 的“操作” > “灾难恢复” > “属性”来查看复制和故障转移属性。   
- **建议使用单独的 Azure VM 网络进行测试故障转移**，而不是使用启用复制时设置的默认网络。
- 根据每个 NIC 的源网络配置，你可以在执行灾难恢复演练之前，在“计算机和网络”中的测试故障转移设置下指定“子网”、“专用 IP 地址”、“公用 IP”、“网络安全组”或“负载均衡器”来连接到每个 NIC。      

## <a name="run-a-test-failover"></a>运行测试故障转移

此示例展示了如何使用恢复服务保管库执行 VM 测试故障转移。

1. 选择一个保管库，转到“受保护的项” > “复制的项”并选择一个 VM。  
1. 在“测试故障转移”中，选择要用于故障转移的恢复点  ：
   - **最新**：处理 Site Recovery 中的所有数据，并提供最低的 RTO（恢复时间目标）。
   - **最新处理**：将 VM 故障转移到由 Site Recovery 处理的最新恢复点。 将显示时间戳。 使用此选项时，无需费时处理数据，因此 RTO 会较低。
   - **最新的应用一致**：此选项将所有 VM 故障转移到最新的应用一致恢复点。 将显示时间戳。
   - **自定义**：故障转移到特定的恢复点。 自定义仅在对单个 VM 进行故障转移时可用，对使用恢复计划进行的故障转移不可用。
1. 选择辅助区域中的 Azure VM 在故障转移后要连接到的目标 Azure 虚拟网络。

   > [!NOTE]
   > 如果为复制的项预先配置了测试故障转移设置，则将不会显示用于选择 Azure 虚拟网络的下拉列表。

1. 若要启动故障转移，请选择“确定”。  若要通过保管库跟踪进度，请转到“监视” > “Site Recovery 作业”并选择“测试故障转移”作业。   
1. 故障转移完成后，副本 Azure VM 会显示在 Azure 门户中的“虚拟机”中。  请确保 VM 正在运行、大小适当并已连接到相应的网络。
1. 若要删除在测试故障转移期间创建的 VM，请选择复制的项或恢复计划上的“清理测试故障转移”。  在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [运行生产故障转移](azure-to-azure-tutorial-failover-failback.md)
