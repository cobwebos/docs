---
title: "使用 Azure Site Recovery 实现将 VMM 云中的 Hyper-V VM 复制到 Azure | Microsoft Docs"
description: "介绍如何使用 Azure Site Recovery 实现将 VMM 云中的 Hyper-V VM 复制到 Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 89a2c4fc-7e03-4a86-a2c0-52831ccebc1a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 96a817e43a830e836f2faa4603fc88ed9c0b1828
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="step-11-enable-replication-to-azure-for-hyper-v-vms-in-vmm-clouds"></a>步骤 11：实现将 VMM 云中的 Hyper-V VM 复制到 Azure

设置复制策略后，根据本文在 Azure 门户中使用 [Azure Site Recovery](site-recovery-overview.md) 服务实现将 System Center Virtual Machine Manager (VMM) 云中托管的本地 Hyper-V 虚拟机 (VM) 复制到 Azure。

请将评论和问题发布到这篇文章的底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。


## <a name="before-you-start"></a>开始之前

确保 Azure 帐户具有创建 Azure VM 的正确[权限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)。 [深入了解](../active-directory/role-based-access-built-in-roles.md) Azure 基于角色的访问控制。

## <a name="exclude-disks-from-replication"></a>从复制中排除磁盘

默认情况下将复制计算机上的所有磁盘。 可以从复制中排除磁盘。 例如，你可能不希望复制包含临时数据，或者每当重新启动计算机或应用程序时刷新的数据（例如 pagefile.sys 或 SQL Server tempdb）的磁盘。 [了解详细信息](site-recovery-exclude-disk.md)

## <a name="replicate-vms"></a>复制 VM

为 VM 启用复制，如下所示：  

1. 单击“步骤 2: 复制应用程序” > “源”。 首次启用复制后，请在保管库中单击“+复制”，对其他计算机启用复制。

    ![启用复制](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication1.png)
2. 在“源”边栏选项卡中，选择 VMM 服务器和 Hyper-V 主机所在的云。 然后单击“确定”。

    ![启用复制](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication-source.png)
3. 在“目标”中，选择订阅、故障转移后的部署模型，以及用于复制数据的存储帐户。

    ![启用复制](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication-target.png)
4. 选择要使用的存储帐户。 如果要使用与现有存储帐户不同的存储帐户，可以[创建一个](#set-up-an-azure-storage-account)。 如果使用高级存储帐户保存复制的数据，则需要选择其他标准存储帐户来存储复制日志，这些日志将捕获本地数据正在发生的更改。要使用 Resource Manager 模型创建存储帐户，请单击“新建”。 如果想要使用经典模型创建存储帐户，请[在 Azure 门户中](../storage/common/storage-create-storage-account.md)执行该操作。 然后单击“确定”。
5. 选择 Azure VM 在故障转移后创建时所要连接的 Azure 网络和子网。 选择“立即为选定的计算机配置”，将网络设置应用到选择保护的所有计算机。 选择“稍后配置”以选择每个计算机的 Azure 网络。 如果要使用与现有不同的网络，可以[创建一个](#set-up-an-azure-network)。 若要使用 Resource Manager 模型创建网络，请单击“新建”。 如果想要使用经典模型创建网络，请[在 Azure 门户中](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)执行该操作。 选择适用的子网。 然后单击“确定”。
6. 在“虚拟机” > “选择虚拟机”中，单击并选择要复制的每个计算机。 只能选择可以启用复制的计算机。 然后单击“确定”。

    ![启用复制](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication5.png)

7. 在“属性” > “配置属性”中，选择所选 VM 的操作系统和 OS 磁盘。

    - 验证 Azure VM 名称（目标名称）是否符合 [Azure 虚拟机要求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。   
    - 默认情况下，为复制选择了 VM 的所有磁盘，但可以通过清除磁盘将其排除。

        - 可能要排除磁盘来减少复制带宽。 例如，可能不需要复制包含临时数据的磁盘，或包含每次重启计算机/应用时刷新的数据（例如 pagefile.sys 或 Microsoft SQL Server tempdb）的磁盘。 通过取消选中该磁盘，可从复制中排除磁盘。
        - 只能排除基本磁盘。 不能排除 OS 磁盘。
        - 建议不要排除动态磁盘。 Site Recovery 无法识别来宾 VM 内的虚拟硬盘是基本磁盘还是动态磁盘。 如果未排除所有相关动态卷磁盘，则受保护的动态磁盘会在故障转移 VM 时显示为故障磁盘，且该磁盘上的数据将无法访问。
        - 启用复制后，无法添加或删除要复制的磁盘。 如果要添加或排除磁盘，需要禁用 VM 保护，并重启保护。
        - 在 Azure 中手动创建的磁盘不会故障回复。 例如，如果对三个磁盘进行故障转移，并直接在 Azure VM 中创建两个磁盘，只会对那三个故障转移的磁盘进行从 Azure 到 Hyper-V 的故障回复。 在故障回复中或从 Hyper-V 到 Azure 的反向复制中，不能包括手动创建的磁盘。
        - 如果排除了运行某个应用程序所需的磁盘，则故障转移到 Azure 后，需要在 Azure 中手动创建该磁盘，以便复制的应用程序可以运行。 或者，可以将 Azure 自动化集成到恢复计划中，以便在故障转移计算机期间创建磁盘。

    单击“确定”保存更改。 可以稍后再设置其他属性。

    ![启用复制](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication6-with-exclude-disk.png)

8. 在“复制设置” > “配置复制设置”中，选择要应用于受保护 VM 的复制策略。 然后单击“确定”。 可以在“复制策略”> 策略名称 >“编辑设置”中修改复制策略。 应用的更改用于已在复制的计算机和新计算机。

   ![启用复制](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication7.png)

可以在“作业” > “Site Recovery 作业”中，跟踪“启用保护”作业的进度。 在“完成保护”作业运行之后，计算机就可以进行故障转移了。



## <a name="next-steps"></a>后续步骤

转到[步骤 12：运行测试故障转移](vmm-to-azure-walkthrough-test-failover.md)

