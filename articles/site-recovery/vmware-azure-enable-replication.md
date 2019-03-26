---
title: 启用 VMware Vm 复制到 Azure 中使用 Azure Site Recovery 的灾难恢复 |Microsoft Docs
description: 本文介绍如何使用 Azure Site Recovery 复制到 Azure 的灾难恢复启用 VMware Vm。
author: mayurigupta13
ms.service: site-recovery
ms.date: 3/6/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 472ff7810852bd03ef322cd5eb647c3d61f09b01
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418102"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>为 VMware VM 启用到 Azure 的复制

本文介绍如何启用将本地 VMware VM 复制到 Azure。

## <a name="prerequisites"></a>必备组件

本文假设用户具备以下条件：

- [设置本地源环境](vmware-azure-set-up-source.md)。
- [设置目标环境在 Azure 中](vmware-azure-set-up-target.md)。

## <a name="before-you-start"></a>开始之前
如果要复制 VMware 虚拟机，请记住此信息：

* Azure 用户帐户需要具有某些[权限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能将新的虚拟机复制到 Azure。
* 系统会每隔 15 分钟发现 VMware VM 一次。 可能需要 15 分钟或更长时间，以发现后显示在 Azure 门户中的 Vm。 同样，发现可能需要 15 分钟或更长时间时添加一个新的 vCenter 服务器或 vSphere 主机。
* 可能需要 15 分钟或更长时间 （例如 VMware 工具安装） 在虚拟机上的环境更改，以在门户中更新。
* 为 VMware Vm，可以检查上次发现时间：请参阅**上次联系时间**字段**配置服务器**vCenter server/vSphere 主机的页。
* 若要添加为复制而不想等待计划的发现的虚拟机，请突出显示配置服务器 （但不要单击它），然后选择**刷新**。
* 当启用复制，如果虚拟机已准备好时，进程服务器会自动在其上安装 Azure Site Recovery 移动服务。

## <a name="enable-replication"></a>启用复制

按照本部分中的步骤之前，请注意以下信息：
* Azure Site Recovery 现在直接复制到所有的新复制的托管磁盘。 进程服务器将复制日志写入到目标区域中的缓存存储帐户。 这些日志用于在托管磁盘副本中创建恢复点。
* 在故障转移时，你选择的恢复点用于创建目标托管磁盘。
* 以前配置为将复制到目标存储帐户的 Vm 不会受到影响。
* 复制到新的虚拟机的存储帐户才可通过具象状态传输 (REST) API 或 Powershell。 使用 Azure REST API 版本 2016年-08-10 或 2018年-01-10 将复制到存储帐户。

1. 转到**步骤 2:** 复制应用程序” > “源”。 为第一次启用复制后，选择 **+ 复制**在保管库中对其他虚拟机启用复制。
1. 在“源”页 >“源”中，选择配置服务器。
1. 有关**计算机类型**，选择**虚拟机**或**物理机**。
1. 在“vCenter/vSphere 虚拟机监控程序”中，选择管理 vSphere 主机的 vCenter 服务器，或选择该主机。 如果要复制物理计算机，则此设置无关。
1. 选择进程服务器，如果你尚未创建任何额外的进程服务器将是配置服务器。 然后选择“确定”。

    ![启用复制源窗口](./media/vmware-azure-enable-replication/enable-replication2.png)

1. 有关**目标**，选择你想要创建故障转移虚拟机的订阅和资源组。 选择你想要在 Azure 中使用适用于故障转移 Vm 的部署模型。

1. 选择 Azure 网络和 Azure 虚拟机将故障转移后连接到的子网。 网络必须位于与站点恢复服务保管库位于同一区域中。

   选择**立即为选定的计算机配置**要将网络设置应用于为保护选择的所有虚拟机。 选择**更高版本配置**以选择每个虚拟机的 Azure 网络。 如果没有网络，需要创建一个。 若要使用 Azure 资源管理器创建一个网络，请选择**新建**。 选择适用的子网，然后选择**确定**。
   
   ![启用复制目标窗口](./media/vmware-azure-enable-replication/enable-rep3.png)

1. 有关**虚拟机** > **选择虚拟机**，选择想要复制的每个虚拟机。 您只能选择可以为其启用复制的虚拟机。 然后选择“确定”。 如果您不能查看或选择任何特定的虚拟机，请参阅[源计算机未在 Azure 门户中列出](https://aka.ms/doc-plugin-VM-not-showing)来解决此问题。

    ![启用复制选择虚拟机窗口](./media/vmware-azure-enable-replication/enable-replication5.png)

1. 有关**属性** > **配置属性**，选择进程服务器使用自动虚拟机上安装 Site Recovery 移动服务的帐户。 此外，选择要进行复制，以根据你的数据变动量模式的目标托管磁盘的类型。
10. 默认情况下，将复制的源虚拟机的所有磁盘。 若要从复制中排除磁盘，请清除**Include**对于任何不想要复制的磁盘的复选框。 然后选择“确定”。 可以稍后再设置其他属性。 详细了解如何[排除磁盘](vmware-azure-exclude-disk.md)。

    ![启用复制配置属性窗口](./media/vmware-azure-enable-replication/enable-replication6.png)

1. 在**复制设置** > **配置复制设置**，验证是否选择了正确的复制策略。 您可以修改复制策略设置在**设置** > **复制策略** > ***策略名称*** >  **编辑设置**。 应用于策略的更改也适用于复制和新虚拟机。
1. 启用**多 VM 一致性**如果你想要收集到复制组的虚拟机。 指定组的名称，然后选择**确定**。

    > [!NOTE]
    >    * 复制组中的虚拟机一起复制，并在故障转移时共享崩溃一致且应用一致恢复点。
    >    * 将 VM 和物理服务器集合在一起，使其镜像工作负荷。 启用多 VM 一致性可能影响工作负荷性能。 仅当虚拟机正在运行相同的工作负荷，并且需要一致性时，才执行此操作。

    ![启用复制窗口](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. 选择“启用复制”。 您可以跟踪进度**启用保护**在作业**设置** > **作业** > **Site Recovery 作业**. 在“完成保护”作业运行之后，虚拟机就可以进行故障转移了。

## <a name="view-and-manage-vm-properties"></a>查看和管理 VM 属性

接下来，验证源虚拟机的属性。 请记住，Azure VM 名称需要符合 [Azure 虚拟机要求](vmware-physical-azure-support-matrix.md#replicated-machines)。

1. 转到**设置** > **复制的项**，然后选择虚拟机。 **Essentials**页显示有关 VM 的设置和状态信息。
1. 在“属性”中，可以查看 VM 的复制和故障转移信息。
1. 在中**计算和网络** > **计算属性**，可以更改多个虚拟机属性。 

    ![计算和网络属性窗口](./media/vmware-azure-enable-replication/vmproperties.png)

    * Azure VM 名称：如有必要，请修改名称，以便满足 Azure 要求。
    * 目标 VM 大小或 VM 类型：默认 VM 大小是选择基于源 VM 大小。 可以选择根据故障转移之前的任何时候需要不同的 VM 大小。 请注意，VM 磁盘大小还取决于源磁盘大小，并故障转移后可以只更改。 了解有关磁盘大小和 IOPS 速率[Windows 上的 VM 磁盘的可伸缩性和性能目标](../virtual-machines/windows/disk-scalability-targets.md)。

    *  资源组：可以选择[资源组](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines)，从虚拟机成为故障转移后的一部分。 您可以更改此设置在故障转移之前的任何时间。 故障转移后，如果虚拟机迁移到不同的资源组，该虚拟机的保护设置会中断。
    * 可用性集：可以选择[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)如果你的虚拟机需要故障转移后的一部分。 当选择一个可用性集时，请记住以下信息：

        * 列出属于指定的资源组的唯一可用性集。  
        * 在不同虚拟网络的 Vm 不能为同一个可用性集的一部分。
        * 仅大小相同的虚拟机可以属于同一可用性集。
1. 此外可以添加有关目标网络、 子网和 IP 地址分配给 Azure VM 的信息。
2. 在“磁盘”中，可以看到 VM 上将要复制的操作系统和数据磁盘。

### <a name="configure-networks-and-ip-addresses"></a>配置网络和 IP 地址

可以设置目标 IP 地址。 如果未提供地址，故障转移虚拟机使用 DHCP。 如果设置的地址在故障转移时不可用，则故障转移不会正常工作。 如果地址是可在测试故障转移网络中，可以将同一个目标 IP 地址用于测试故障转移。

按如下所示指定目标虚拟机的大小来，确定网络适配器数目：

- 如果源虚拟机上的网络适配器数小于或等于目标 VM 的大小允许的适配器数，目标与源具有相同数量的适配器。
- 如果源虚拟机的适配器数超过允许的目标 VM 大小的数目，则使用目标大小最大值。 例如，如果源虚拟机有两个网络适配器和目标 VM 大小支持四个，则目标虚拟机有两个适配器。 如果源 VM 具有两个适配器，但目标大小只支持一个，目标 VM 具有只有一个适配器。
- 如果虚拟机有多个网络适配器，它们会全部连接到同一网络。 此外，在列表中所示的第一个适配器将成为*默认*Azure 虚拟机中的网络适配器。 

### <a name="azure-hybrid-benefit"></a>Azure 混合权益

Microsoft 软件保障客户可以使用 Azure 混合权益来节省迁移到 Azure 的 Windows Server 计算机的许可成本。 优势也适用于 Azure 的灾难恢复。 如果你有资格，您可以权益分配到 Site Recovery 会创建如果故障转移的虚拟机。 为此，请执行以下步骤：
1. 转到**计算机和网络属性**的复制的虚拟机。
2. 当系统询问你是否具有可使你有资格使用 Azure 混合权益的 Windows Server 许可证回答。
3. 确认你有合格的 Windows Server 许可证具有软件保障，可用于将权益应用于将在故障转移创建 VM。
4. 保存复制的虚拟机的设置。

深入了解 [Azure 混合权益](https://aka.ms/azure-hybrid-benefit-pricing)。

## <a name="resolve-common-issues"></a>解决常见的问题

* 每个磁盘应小于 4 TB。
* OS 磁盘应为基本磁盘，而不是动态磁盘。
* 对于生成 2/启用了 UEFI 的虚拟机，操作系统系列应是 Windows，并且启动盘应小于 300 GB。

## <a name="next-steps"></a>后续步骤

虚拟机进入受保护的状态后，请尝试[故障转移](site-recovery-failover.md)来检查你的应用程序是否显示在 Azure 中。

* 若要禁用保护，请了解如何[清理注册和保护设置](site-recovery-manage-registration-and-protection.md)。
* 了解如何[使用 Powershell 自动执行虚拟机的复制](vmware-azure-disaster-recovery-powershell.md)。
