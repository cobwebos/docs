---
title: 在 Azure Site Recovery 中配置 Azure VM 复制 | Microsoft Docs
description: 本文介绍如何使用 Site Recovery 配置从一个 Azure 区域到另一个区域的 Azure VM 复制。
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 19a1e13815f1d83f13392892dd9c87b84a40a1c1
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551696"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>将 Azure VM 复制到另一个 Azure 区域


本文介绍如何启用从一个 Azure 区域到另一个区域的 Azure VM 复制。

## <a name="before-you-start"></a>开始之前

本文假设你已根据 [Azure 到 Azure 灾难恢复教程](azure-to-azure-tutorial-enable-replication.md)中所述进行了 Site Recovery 部署准备。

必备组件应已到位，并且你应该已创建恢复服务保管库。


## <a name="enable-replication"></a>启用复制

启用复制。 此过程假设主要 Azure 区域是东亚，次要区域是东南亚。

1. 在保管库中，单击“+复制”  。
2. 指定以下字段：
   - **源**：VM 的起始点，在本例中为 Azure  。
   - **源位置**：要在其中保护 VM 的 Azure 区域。 在本演示中，源位置为“东亚”
   - **部署模型**：源计算机的 Azure 部署模型。
   - **源订阅**：源 VM 所属的订阅。 这可以是存在恢复服务保管库的同一 Azure Active Directory 租户中的任何订阅。
   - **资源组**：源虚拟机所属的资源组。 所选资源组下的所有 VM 都会在下一步中列出，以供保护。

     ![启用复制](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. 在“虚拟机”>“选择虚拟机”  中，单击并选择要复制的每个 VM。 只能选择可以启用复制的计算机。 然后单击“确定”  。
    ![启用复制](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. 在“设置”中，可以选择性地配置目标站点设置： 

   - **目标位置**：要在其中复制源虚拟机数据的位置。 根据所选的计算机位置，Site Recovery 会提供合适目标区域的列表。 我们建议将目标位置与恢复服务保管库位置保持相同。
   - **目标订阅**：用于灾难恢复的目标订阅。 默认情况下，目标订阅将与源订阅相同。
   - **目标资源组**：所有复制虚拟机所属的资源组。
       - 默认情况下，Site Recovery 会在目标位置中创建一个名称带“asr”后缀的新资源组。
       - 如果 Site recovery 创建的资源组已存在，则会重复使用。
       - 可以自定义资源组设置。
       - 目标资源组的位置可以是除托管源 VM 区域以外的任何 Azure 区域。
   - **目标虚拟网络**：默认情况下，Site Recovery 会在目标位置中创建一个名称带“asr”后缀的新虚拟网络。 这会映射到源网络并会用于任何将来的保护。 [详细了解](site-recovery-network-mapping-azure-to-azure.md)网络映射。
   - **目标存储帐户（源 VM 不使用托管磁盘）** ：默认情况下，Site Recovery 会创建模拟源 VM 存储配置的新目标存储帐户。 如果存储帐户已存在，则重复使用。
   - **副本托管磁盘（源 VM 使用托管磁盘）** ：Site Recovery 在目标区域新建托管磁盘副本，以生成和源 VM 的托管磁盘存储类型一致（标准或高级）的镜像磁盘。
   - **缓存存储帐户**：Site Recovery 需要源区域中称为缓存存储的额外存储帐户。 在复制到目标位置前，系统会跟踪源 VM 上发生的所有更改并发送到缓存存储帐户。 此存储帐户应是标准。
   - **目标可用性集**：默认情况下，Site Recovery 会在目标区域中创建一个名称带“asr”后缀（针对源区域中属于可用性集的 VM）的新可用性集。 如果 Site recovery 创建的可用性集已存在，则重复使用它。
   - **目标可用性区域**：默认情况下，Site Recovery 会在目标区域中分配与源区域相同的区域编号，前提是目标区域支持可用性区域。

     如果目标区域不支持可用性区域，则会将目标 VM 默认配置为单一实例。 如果需要，可以单击“自定义”，以便在目标区域中将此类 VM 配置为可用性集的一部分。

     >[!NOTE]
     >在启用复制以后，不能更改可用性类型 - 单一实例、可用性集或可用性区域。 若要更改可用性类型，需要先禁用复制，然后再启用复制。
     >
    
   - **复制策略**：定义恢复点保留期历史记录和应用一致性快照频率的设置。 默认情况下，Azure Site Recovery 使用的"24 小时的恢复点保留期默认设置创建新的复制策略和 4 个小时的应用一致性快照频率。

     ![启用复制](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>为添加的磁盘启用复制

将磁盘添加到支持复制的 Azure VM 时，会发生以下情况：
-   VM 的复制运行状况会显示一条警告，并且会通过备注告知你需要对一个或多个磁盘进行保护。
-   如果为添加的磁盘启用保护，此警告会在初始复制磁盘后消失。
-   如果选择不为磁盘启用复制，则可选择关闭此警告。

    
    ![添加的新磁盘](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

若要为添加的磁盘启用复制，请执行以下操作：

1.  在保管库 >“复制的项”  中，单击已向其添加磁盘的 VM。
2.  单击“磁盘”  ，然后选择要为其启用复制的数据磁盘（这些磁盘具有“未受保护”  状态）。
3.  在“磁盘详细信息”  中，单击“启用复制”  。

    ![为添加的磁盘启用复制](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

在启用复制作业运行且初始复制完成后，将删除针对磁盘问题的复制运行状况警告。


  
## <a name="customize-target-resources"></a>自定义目标资源

可以修改 Site Recovery 使用的默认目标设置。

1. 单击“目标订阅”旁边的“自定义：”，修改默认目标订阅  。 从同一 Azure Active Directory (AAD) 租户中可用的所有订阅列表中选择订阅。

2. 单击“自定义:”修改默认设置： 
    - 在“目标资源组”中，从订阅内目标位置中的所有资源组列表中选择资源组。 
    - 在“目标虚拟网络”中，从目标位置中所有虚拟网络列表中选择网络。 
    - 在“可用性集”中，可将可用性集设置添加到 VM（如果它们是源区域中可用性集的一部分）。 
    - 在“目标存储帐户”中，选择要使用的帐户。 

        ![启用复制](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. 单击“自定义:”以修改默认设置。 
4. 在“多 VM 一致性”  中，选择要一起复制的 VM。
    - 故障转移时，复制组中的所有计算机将具有共享的崩溃一致性恢复点和应用程序一致性恢复点。
    - 启用多 VM 一致性会影响工作负荷的性能（因为它是 CPU 密集型的）。 仅当计算机运行相同的工作负荷并且你需要在多个计算机之间保持一致时，才应启用此功能。
    - 例如，如果应用程序有 2 个 SQL Server 虚拟机和 2 个 Web 服务器，则只应将 SQL Server VM 添加到复制组。
    - 在一个复制组中最多可以选择包含 16 个 VM。
    - 如果启用了多 VM 一致性，则复制组中的计算机将通过端口 20004 相互通信。
    - 确保防火墙设备没有阻止 VM 之间通过端口 20004 进行的内部通信。
    - 如果想要 Linux VM 成为复制组的一部分，请确保按照特定 Linux 版本的指南手动打开端口 20004 上的出站流量。
![启用复制](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)
    
5. 单击“创建目标资源” > “启用复制”。  
6. 为 VM 启用复制后，可以在“复制的项”下检查 VM 的运行状况 

>[!NOTE]
>在初始复制期间，VM 状态刷新可能需要一段时间，但不显示进度。 单击“刷新”按钮可获取最新状态。 
>

# <a name="next-steps"></a>后续步骤

[详细了解](site-recovery-test-failover-to-azure.md)如何运行测试故障转移。
