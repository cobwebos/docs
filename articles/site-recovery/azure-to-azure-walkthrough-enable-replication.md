---
title: "使用 Azure Site Recovery 将 Azure VM 复制到其他 Azure 区域 | Microsoft Docs"
description: "概述了使用 Azure Site Recovery 将 Azure VM 复制到另一 Azure 区域所需的步骤"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a309644f-d36b-4188-bba7-ad45a2d9bede
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 8/01/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: f426ba4341e61d3c7da820d7d5097b217e94ca0e
ms.contentlocale: zh-cn
ms.lasthandoff: 08/02/2017

---


# <a name="step-5-enable-replication-for-azure-vms"></a>步骤 5：启用 Azure VM 复制


设置[恢复服务保管库](azure-to-azure-walkthrough-vault.md)后，请参阅本文内容，使用 [Azure Site Recovery](site-recovery-overview.md) 将虚拟机 (VM) 复制到其他 Azure 区域。 若要启用复制，请设置源和目标设置，验证复制策略，然后选择要复制的虚拟机。

- 按本文内容操作后，Azure VM 应会复制到辅助 Azure 区域。
- 请将任何评论发布到本文底部，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提问

>[!NOTE]
>
> Azure VM 复制目前处于预览版。


## <a name="select-the-source"></a>选择源

1. 在“恢复服务保管库”中，单击保管库名称 >“+复制”。
2. 在“源”中，选择“Azure - 预览版”。
2. 在“源位置”中，选择当前运行 VM 的 Azure 源区域。
3. 为 VM 选择 Azure 虚拟机部署模型：“资源管理器”或“经典”。
4. 为资源管理器 VM 选择“源资源组”，为经典 VM 选择“云服务”。
5. 单击“确定”保存设置。

    ![配置源](./media/azure-to-azure-walkthrough-enable-replication/source.png)

## <a name="select-the-vms"></a>选择 VM

Site Recovery 检索与订阅和资源组/云服务关联的 VM 列表。

1. 在“虚拟机”中，选择要复制的 VM。
2. 单击 **“确定”**。

    ![选择 VM](./media/azure-to-azure-walkthrough-enable-replication/vms.png)


## <a name="configure-settings"></a>配置设置

Site Recovery 为目标区域预配默认设置（基于源区域设置）和复制策略：

   - **目标位置**：要用于灾难恢复的目标区域。 建议选择与 Site Recovery 保管库位置匹配的目标位置。
   - **目标资源组**：故障转移后目标区域中 Azure VM 所属的资源组。 默认情况下，Site Recovery 会在目标位置中创建一个带有“asr”后缀的新资源组。
   - **目标虚拟网络**：故障转移后目标区域中 Azure VM 所位于的网络。 默认情况下，Site Recovery 会在目标位置中创建一个带有“asr”后缀的新虚拟网络（以及子网）。 此网络映射到源网络。 注意：如需在源位置和目标位置中保持相同 IP 地址，可在 VM 故障转移后分配一个特定 IP 地址。
   - **缓存存储帐户**：Site Recovery 使用源区域中的一个存储帐户。 复制到目标位置之前，对源 VM 的更改会发送到此帐户。
   - **目标存储帐户**：默认情况下，Site Recovery 会在目标区域中创建新存储帐户，从而形成源虚拟机存储帐户的镜像。
   -  **目标可用性集**：默认情况下，Site Recovery 会在目标区域中创建一个带有“asr”后缀的新可用性集。
   - **复制策略名称**：策略名称。
   - **恢复点保留期**： 默认情况下，Site Recovery 会将恢复点保留 24 小时。 可将此值配置为 1 - 72 小时。
   - **“应用一致”快照频率**：默认情况下，Site Recovery 每隔 4 小时拍取 1 次“应用一致”快照。 可将此值配置为 1 - 12 小时之间的任何值。 连续复制数据：
    - “崩溃时一致”恢复点在创建时会保持一致的数据写入顺序。 如果应用设计为从崩溃恢复时保持数据一致，则此类型的恢复点足以满足需求
    - 每隔几分钟生成“崩溃时一致”恢复点。 使用这些恢复点进行故障转移和恢复 VM 时会按分钟顺序提供恢复点目标 (RPO)。
    - “应用一致”恢复点（同时保持写入顺序一致性）确保正在运行的应用完成所有操作并将缓冲刷新至磁盘（应用程序处于静止）。 建议对 SQL Server、Oracle 和 Exchange 等数据库应用使用这些恢复点。

    ![配置设置](./media/azure-to-azure-walkthrough-enable-replication/settings.png)


### <a name="modify-settings"></a>修改设置

如要修改目标设置和复制策略设置，请执行以下操作：

1. 若要查看或修改目标设置，请单击“设置”。
2. 若要替代默认目标设置，请单击“自定义”。 可以指定目标资源组、虚拟网络、可用性集和目标存储帐户。 如果 VM 是源区域中一个集的一部分，则仅可添加可用性集。

    ![配置设置](./media/azure-to-azure-walkthrough-enable-replication/customize-target.png)

3. 若要替代恢复点和“应用一致”快照的复制设置，请单击“复制策略”旁边的“自定义”。

    ![配置设置](./media/azure-to-azure-walkthrough-enable-replication/customize-policy.png)

4. 若要开始预配目标资源，请单击“创建目标资源”。 预配大约需要 1 分钟。 预配期间请勿关闭边栏选项卡，否则需要重新开始。




## <a name="enable-replication"></a>启用复制

1. 在“设置”中，单击“启用复制”。 这可启用所选 VM 的初始复制。 初始复制状态刷新可能需要一些时间。 单击“刷新”可查看最新状态。

2. 可以在“设置” > “作业” > “Site Recovery 作业”中，跟踪“启用保护”作业的进度。

3. 在“设置” > “复制的项”中，可以查看 VM 的状态和初始复制进度。 单击 VM，向下钻取其设置。



## <a name="next-steps"></a>后续步骤

转到[第 6 步：运行测试故障转移](azure-to-azure-walkthrough-test-failover.md)

