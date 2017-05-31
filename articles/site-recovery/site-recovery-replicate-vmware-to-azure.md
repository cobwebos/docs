---
title: "复制应用程序（VMware 到 Azure） | Microsoft Docs"
description: "本文介绍如何设置 VMware 上运行的虚拟机到 Azure 的复制。"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/17/2017
ms.author: asgang
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: f78a857a795031f6188635091c76431cd5440d1c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/17/2017


---


# <a name="replicate-applications"></a>复制应用程序


本文介绍如何设置 VMware 上运行的虚拟机到 Azure 的复制。
## <a name="prerequisites"></a>先决条件

本文假设用户已

1.  [设置本地源环境](site-recovery-set-up-vmware-to-azure.md)
2.  [在 Azure 中设置目标环境](site-recovery-prepare-target-vmware-to-azure.md)


## <a name="enable-replication"></a>启用复制
#### <a name="before-you-start"></a>开始之前
复制 VMware 虚拟机时，请注意：

* Azure 用户帐户需要具有某些[权限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能启用新的虚拟机到 Azure 的复制。
* 系统会每隔 15 分钟发现 VMware VM 一次。 在发现之后，可能需要 15 分钟或更长时间，虚拟机才会出现在门户中。 同样，当你添加新的 vCenter 服务器或 vSphere 主机时，发现可能要花费 15 分钟或更长时间。
* 虚拟机上的环境更改（例如 VMware 工具安装）可能需要 15 分钟或更长时间才能在门户中更新。
* 可以在“配置服务器”边栏选项卡上 vCenter 服务器/vSphere 主机的“上次联系时间”字段中检查上次发现 VMware VM 的时间。
* 若要添加用于复制的计算机而不想要等待执行计划的发现，请突出显示配置服务器（不要单击它），然后单击“刷新”按钮。
* 启用复制时，如果计算机已做好准备，进程服务器将自动在其上安装移动服务。


**现在，请按如下所述启用复制**：

1. 单击“步骤 2: 复制应用程序” > “源”。 首次启用复制后，请在保管库中单击“+复制”，对其他计算机启用复制。
2. 在“源”边栏选项卡 >“源”中，选择配置服务器。
3. 在“计算机类型”中，选择“虚拟机”或“物理机”。
4. 在“vCenter/vSphere 虚拟机监控程序”中，选择管理 vSphere 主机的 vCenter 服务器，或选择该主机。 如果你要复制物理机，则此设置无关紧要。
5. 选择进程服务器。 如果你未创建任何额外的进程服务器，则这是配置服务器的名称。 。

    ![启用复制](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. 在“目标”中，选择要在其中创建故障转移虚拟机的订阅和资源组。 选择希望在 Azure 中为故障转移虚拟机使用的部署模型（经典或资源管理）。


7. 选择要用于复制数据的 Azure 存储帐户。 请注意：

   * 可以选择高级或标准存储帐户。 如果选择高级帐户，则需要指定其他标准存储帐户来持续写入复制日志。 这些帐户必须位于与恢复服务保管库相同的区域中。
   * 若要使用已有帐户以外的存储帐户，可以创建帐户*占位链接：使用 Resource Manager 创建存储帐户（将在“入门”中介绍）*。 若要使用 Resource Manager 模型创建存储帐户，请单击“新建”。 如果想要使用经典模型创建存储帐户，请[在 Azure 门户中](../storage/storage-create-storage-account-classic-portal.md)执行该操作。


8. 选择 Azure VM 在故障转移后启动时所要连接的 Azure 网络和子网。 该网络必须位于与恢复服务保管库相同的区域中。 选择“立即为选定的计算机配置”，将网络设置应用到选择保护的所有计算机。 选择“稍后配置”以选择每个计算机的 Azure 网络。 如果没有网络，需要[创建一个](#set-up-an-azure-network)。 若要使用 Resource Manager 创建网络，请单击“新建”。 如果想要使用经典模型创建网络，请[在 Azure 门户中](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)执行该操作。 选择适用的子网。 。

    ![启用复制](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. 在“虚拟机” > “选择虚拟机”中，单击并选择要复制的每个计算机。 只能选择可以启用复制的计算机。 。

    ![启用复制](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. 在“属性” > “配置属性”中，选择进程服务器在计算机上自动安装移动服务时使用的帐户。 默认情况下会复制所有磁盘。 单击“所有磁盘”并清除不想要复制的所有磁盘。 。 可以稍后再设置其他属性。

    ![启用复制](./media/site-recovery-vmware-to-azure/enable-replication6.png)


> [!NOTE]
> 默认情况下将复制计算机上的所有磁盘。 可以[从复制中排除磁盘](site-recovery-exclude-disk.md)。 例如，你可能不想要复制包含临时数据，或者每当重新启动计算机或应用程序时刷新的数据（例如 pagefile.sys 或 SQL Server tempdb）的磁盘。
>



11. 在“复制设置” > “配置复制设置”中，检查是否选择了正确的复制策略。 可以在“设置” > “复制策略”> 策略名称 >“编辑设置”中修改复制策略设置。 应用到策略的更改将应用于复制计算机和新计算机。
12. 如果要将计算机集合到复制组，请启用“多 VM 一致性”并指定组的名称。 。 请注意：

    * 复制组中的计算机将一起复制，并在故障转移时获得崩溃一致且应用一致的共享恢复点。
    * 我们建议你将 VM 和物理服务器集合在一起，使其镜像你的工作负荷。 启用多 VM 一致性可能会影响工作负荷性能，因此，仅当计算机运行相同的工作负荷并且你需要一致性时，才应使用该设置。

    ![启用复制](./media/site-recovery-vmware-to-azure/enable-replication7.png)
13. 单击“启用复制”。 可以在“设置” > “作业” > “Site Recovery 作业”中，跟踪“启用保护”作业的进度。 在“完成保护”作业运行之后，计算机就可以进行故障转移了。

> [!NOTE]
> 如果计算机已准备好进行推送安装，则启用保护后，将安装移动服务组件。 在计算机上安装该组件后，某个保护作业将启动但会失败。 发生这种失败后，需要手动重新启动每台计算机。 重启后，保护作业重新启动，初始复制开始。
>
>

## <a name="view-and-manage-vm-properties"></a>查看和管理 VM 属性
建议你验证源计算机的属性。 请记住，Azure VM 名称应符合 [Azure 虚拟机要求](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。

1. 单击“设置” > “复制的项”，然后选择计算机。 “概要”边栏选项卡显示有关计算机设置和状态的信息。
2. 在“属性”中，可以查看 VM 的复制和故障转移信息。
3. 在“计算和网络” > “计算属性”中，可以指定 Azure VM 名称和目标大小。 根据需要修改名称，使其符合 Azure 要求。
![启用复制](./media/site-recovery-vmware-to-azure/VMProperties_AVSET.png)

*资源组*

  * 可以选择计算机会在故障转移后成为其中一部分的[资源组](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines)。 在故障转移前，可以随时更改此设置。

> [!NOTE]
> 在故障转移后，如果将计算机迁移到其他资源组，则计算机的保护设置会中断。

*可用性集*

如果需要计算机在故障转移后成为某个[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)的一部分，可以选择一个。
选择可用性集时，请记住：

* 仅会列出属于指定资源组的可用性集  
* 具有不同虚拟网络的计算机不能属于同一可用性集
* 仅大小相同的虚拟机可以属于同一可用性集

*网络属性*

你还可以查看和添加目标网络、子网的相关信息，以及要分配到 Azure VM 的 IP 地址。 注意以下事项：

   * 可以设置目标 IP 地址。 如果未提供地址，故障转移的计算机将使用 DHCP。 如果设置了无法用于故障转移的地址，故障转移将不会正常工作。 如果地址可用于测试故障转移网络，则同一个目标 IP 地址可用于测试故障转移。
   * 网络适配器数目根据你为目标虚拟机指定的大小来确定，如下所述：

     * 如果源计算机上的网络适配器数小于或等于目标计算机大小允许的适配器数，则目标的适配器数将与源相同。
     * 如果源虚拟机的适配器数大于目标大小允许的数目，则使用目标大小允许的最大数目。
     * 例如，如果源计算机有两个网络适配器，而目标计算机大小支持四个，则目标计算机将有两个适配器。 如果源计算机有两个适配器，但支持的目标大小只支持一个，则目标计算机只有一个适配器。     
   * 如果虚拟机有多个网络适配器，它们将全部连接到同一个网络。
   * 如果虚拟机有多个网络适配器，列表中显示的第一个适配器将成为 Azure 虚拟机中的*默认*网络适配器。

4. 在“磁盘”中，可以看到 VM 上将要复制的操作系统和数据磁盘。


## <a name="common-issues"></a>常见问题

* 每个磁盘的大小应小于 1TB。
* OS 磁盘应是基本磁盘而不是动态磁盘
* 对于第 2 代虚拟机/已启用 UEFI 的虚拟机，操作系统系列应是 Windows，并且启动盘应小于 300GB。

## <a name="next-steps"></a>后续步骤

保护完成后，可以尝试[故障转移](site-recovery-failover.md)，检查应用程序是否出现在 Azure 中。

如果想要禁用保护，请查看如何[清理注册和保护设置](site-recovery-manage-registration-and-protection.md)

