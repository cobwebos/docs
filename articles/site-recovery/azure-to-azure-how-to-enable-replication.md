---
title: 在 Azure Site Recovery 中配置 Azure Vm 的复制
description: 了解如何使用 Site Recovery 为 Azure Vm 配置到另一区域的复制。
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2018
ms.openlocfilehash: 1c6b7cfbf193f02598052b6922efec17fb16ec83
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75973692"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>将 Azure Vm 复制到另一个 Azure 区域


本文介绍如何启用从一个 Azure 区域到另一个区域的 Azure VM 复制。

## <a name="before-you-start"></a>开始之前

本文假设已根据[azure 到 azure 灾难恢复教程](azure-to-azure-tutorial-enable-replication.md)中所述，准备好 Site Recovery 部署。

先决条件应该已准备就绪，并且你应该已经创建了恢复服务保管库。


## <a name="enable-replication"></a>启用复制

启用复制。 此过程假设主要 Azure 区域是东亚，次要区域是东南亚。

1. 在保管库中，单击“+复制”。
2. 指定以下字段：
   - **源**：VM 的起始点，在本例中为 **Azure**。
   - **源位置**：要在其中保护 Vm 的 Azure 区域。 在本演示中，源位置为“东亚”
   - **部署模型**：源计算机的 Azure 部署模型。
   - **源订阅**：源 vm 所属的订阅。 这可以是存在恢复服务保管库的同一 Azure Active Directory 租户中的任何订阅。
   - **资源组**：源虚拟机所属的资源组。 所选资源组下的所有 VM 都会在下一步中列出，以供保护。

     ![启用复制](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. 在“虚拟机”>“选择虚拟机”中，单击并选择要复制的每个 VM。 只能选择可以启用复制的计算机。 然后单击“确定”。
    ![启用复制](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. 在“设置”中，可以选择性地配置目标站点设置：

   - **目标位置**：要在其中复制源虚拟机数据的位置。 根据所选的计算机位置，Site Recovery 会提供合适目标区域的列表。 我们建议将目标位置与恢复服务保管库位置保持相同。
   - **目标订阅**：用于灾难恢复的目标订阅。 默认情况下，目标订阅将与源订阅相同。
   - **目标资源组**：所有复制虚拟机所属的资源组。
       - 默认情况下 Site Recovery 在目标区域中创建一个名称中包含 "asr" 后缀的新资源组。
       - 如果 Site Recovery 创建的资源组已存在，则会重复使用。
       - 你可以自定义资源组设置。
       - 目标资源组的位置可以是任何 Azure 区域（源 Vm 所托管的区域除外）。
   - **目标虚拟网络**：默认情况下，Site Recovery 在目标区域中创建一个名称中包含 "asr" 后缀的新虚拟网络。 这会映射到源网络并会用于任何将来的保护。 [详细了解](site-recovery-network-mapping-azure-to-azure.md)网络映射。
   - **目标存储帐户（源 vm 不使用托管磁盘）** ：默认情况下，Site Recovery 创建模拟源 vm 存储配置的新目标存储帐户。 如果存储帐户已存在，则重复使用。
   - **副本托管磁盘（源 VM 使用托管磁盘）** ： Site Recovery 在目标区域中创建新的副本托管磁盘，以将源 vm 的托管磁盘与源 vm 的托管磁盘的存储类型（标准或高级）进行镜像。
   - **缓存存储帐户**：Site Recovery 需要源区域中称为“缓存存储”的额外存储帐户。 在复制到目标位置前，系统会跟踪源 VM 上发生的所有更改并发送到缓存存储帐户。 此存储帐户应为 Standard。
   - **目标可用性集**：默认情况下，Site Recovery 使用名称中的 "asr" 后缀在目标区域中创建新的可用性集，对于作为源区域中可用性集的一部分的 vm。 如果 Site Recovery 创建的可用性集已存在，则会重复使用。
   - **目标可用性区域**：默认情况下，Site Recovery 会在目标区域中分配与源区域相同的区域编号，前提是目标区域支持可用性区域。

     如果目标区域不支持可用性区域，则会将目标 VM 默认配置为单一实例。 如果需要，可以单击“自定义”，以便在目标区域中将此类 VM 配置为可用性集的一部分。

     >[!NOTE]
     >在启用复制以后，不能更改可用性类型 - 单一实例、可用性集或可用性区域。 若要更改可用性类型，需要先禁用复制，然后再启用复制。
     >

   - **复制策略**：定义恢复点保留期历史记录和应用一致性快照频率的设置。 默认情况下，Azure Site Recovery 会创建一个新的复制策略，其中默认设置为 "24 小时"，对于 "应用一致性快照频率" 为 "4 小时"。

     ![启用复制](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>为添加的磁盘启用复制

如果将磁盘添加到启用了复制的 Azure VM，则会发生以下情况：
-   VM 的复制运行状况显示一条警告，说明通知你有一个或多个磁盘可用于保护。
-   如果为添加的磁盘启用保护，则在初始复制磁盘后，将会出现警告。
-   如果选择不启用磁盘复制，则可以选择关闭该警告。


    ![已添加新磁盘](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

若要为添加的磁盘启用复制，请执行以下操作：

1.  在保管库中 > "**复制的项**"，单击要向其添加磁盘的 VM。
2.  单击 "**磁盘**"，然后选择要为其启用复制的数据磁盘（这些磁盘的状态为 "**不受保护**"）。
3.  在 "**磁盘详细信息**" 中，单击 "**启用复制**"。

    ![为添加的磁盘启用复制](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

运行 "启用复制" 作业并完成初始复制后，将删除磁盘问题的复制运行状况警告。



## <a name="customize-target-resources"></a>自定义目标资源

可以修改 Site Recovery 使用的默认目标设置。

1. 单击“目标订阅”旁边的“自定义：”，修改默认目标订阅。 从同一 Azure Active Directory (AAD) 租户中可用的所有订阅列表中选择订阅。

2. 单击“自定义:”修改默认设置：
    - 在“目标资源组”中，从订阅内目标位置中的所有资源组列表中选择资源组。
    - 在“目标虚拟网络”中，从目标位置中所有虚拟网络列表中选择网络。
    - 在“可用性集”中，可将可用性集设置添加到 VM（如果它们是源区域中可用性集的一部分）。
    - 在“目标存储帐户”中，选择要使用的帐户。

        ![启用复制](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. 单击“自定义:”以修改默认设置。
4. 在 "**多 VM 一致性**" 中，选择要一起复制的 vm。
    - 故障转移时，复制组中的所有计算机将具有共享的崩溃一致性恢复点和应用程序一致性恢复点。
    - 启用多 VM 一致性可能会影响工作负荷性能（因为它消耗大量 CPU）。 仅当计算机运行相同的工作负荷并且需要跨多台计算机的一致性时，才应启用此功能。
    - 例如，如果应用程序具有2个 SQL Server 虚拟机和两个 web 服务器，则只应将 SQL Server Vm 添加到复制组。
    - 最多可选择复制组中有16个 Vm。
    - 如果启用了多 VM 一致性，则复制组中的计算机将通过端口 20004 相互通信。
    - 确保没有防火墙设备阻止 Vm 之间通过端口20004进行的内部通信。
    - 如果希望 Linux Vm 成为复制组的一部分，请确保按照特定 Linux 版本的指南手动打开端口20004上的出站流量。
![启用复制](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)

5. 单击“创建目标资源” > “启用复制”。
6. 为 VM 启用复制后，可以在“复制的项”下检查 VM 的运行状况

>[!NOTE]
>在初始复制期间，VM 状态刷新可能需要一段时间，但不显示进度。 单击“刷新”按钮可获取最新状态。
>

## <a name="next-steps"></a>后续步骤

[详细了解](site-recovery-test-failover-to-azure.md)如何运行测试故障转移。
