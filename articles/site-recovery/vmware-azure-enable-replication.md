---
title: 使用 Azure Site Recovery 启用用于灾难恢复的 VMware Vm
description: 本文介绍如何使用 Azure Site Recovery 服务为灾难恢复启用 VMware VM 复制
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 10b3e572ec61d1eff342f24a6a5a7bcba6276983
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257311"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>为 VMware VM 启用到 Azure 的复制

本文介绍如何启用将本地 VMware VM 复制到 Azure。

## <a name="resolve-common-issues"></a>解决常见问题

* 每个磁盘应小于 4 TB。
* OS 磁盘应是基本磁盘，而不是动态磁盘。
* 对于启用了第2代/UEFI 的虚拟机，操作系统系列应为 Windows，启动磁盘应小于 300 GB。

## <a name="prerequisites"></a>必备条件

本文假设用户具备以下条件：

- [设置本地源环境](vmware-azure-set-up-source.md)。
- [在 Azure 中设置目标环境](vmware-azure-set-up-target.md)。
- 在开始之前，[请验证要求和先决条件](vmware-physical-azure-support-matrix.md)。 需要注意的重要事项包括：
    - 复制的计算机[支持的操作系统](vmware-physical-azure-support-matrix.md#replicated-machines)。
    - [存储/磁盘](vmware-physical-azure-support-matrix.md#storage)支持。
    - 本地计算机应符合的[Azure 要求](vmware-physical-azure-support-matrix.md#azure-vm-requirements)。


## <a name="before-you-start"></a>开始之前
复制 VMware 虚拟机时，请记住以下信息：

* Azure 用户帐户需要具有某些[权限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能将新的虚拟机复制到 Azure。
* 系统会每隔 15 分钟发现 VMware VM 一次。 Vm 可能需要15分钟或更长时间才能在发现后出现在 Azure 门户中。 同样，添加新的 vCenter 服务器或 vSphere 主机时，发现可能需要15分钟或更长时间。
* 在门户中更新虚拟机上的环境更改（例如 VMware 工具安装）可能需要15分钟或更长时间。
* 可以查看 VMware Vm 的上次发现时间：在 vCenter server/vSphere 主机的 "**配置服务器**" 页上，查看**最后一个 "联系人**" 字段。
* 若要添加用于复制的虚拟机而不等待计划的发现，请突出显示配置服务器（但不要单击它），然后选择 "**刷新**"。
* 启用复制时，如果虚拟机已准备就绪，进程服务器将自动在其上安装 Azure Site Recovery 移动服务。

## <a name="enable-replication"></a>启用复制

在执行本部分中的步骤之前，请注意以下信息：
* 现在，Azure Site Recovery 会直接复制到所有新复制的托管磁盘。 进程服务器将复制日志写入目标区域中的缓存存储帐户。 这些日志用于在名称约定为 asrseeddisk 的副本托管磁盘中创建恢复点。
* 支持从[microsoft.recoveryservices 模块版本2.0.0 向上](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)复制到托管磁盘的 Powershell 支持。 
* 故障转移时，你选择的恢复点将用于创建目标托管磁盘。
* 以前配置为复制到目标存储帐户的 Vm 不会受到影响。
* 仅可通过具象状态传输（REST） API 和 Powershell 复制到新虚拟机的存储帐户。 使用 Azure REST API 版本2016-08-10 或2018-01-10 复制到存储帐户。

请按照以下步骤启用复制：
1. 请参阅**步骤2：复制应用程序** > **源**。 首次启用复制后，请在保管库中选择 " **+ 复制**"，为其他虚拟机启用复制。
2. 在“源”页 >“源”中，选择配置服务器。
3. 对于 "**计算机类型**"，请选择 "**虚拟机**" 或 "**物理计算机**"。
4. 在“vCenter/vSphere 虚拟机监控程序”中，选择管理 vSphere 主机的 vCenter 服务器，或选择该主机。 如果要复制物理计算机，则此设置不适用。
5. 选择进程服务器。 如果没有创建其他进程服务器，则会在下拉列表中提供配置服务器的内置进程服务器。 每个进程服务器的运行状况状态是根据建议的限制和其他参数指示的。 选择一个正常运行的进程服务器。 不能选择[有严重错误的](vmware-physical-azure-monitor-process-server.md#process-server-alerts)进程服务器。 你可以[进行故障排除并解决](vmware-physical-azure-troubleshoot-process-server.md)错误**或者**设置一个[横向扩展进程服务器](vmware-azure-set-up-process-server-scale.md)。
    ![启用复制源窗口](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> 在[9.24 版本](service-updates-how-to.md#links-to-currently-supported-update-rollups)中，引入了附加警报来增强进程服务器的运行状况警报。 对于要生成的所有警报，将 Site Recovery 组件升级到9.24 版本或更高版本。

6. 对于 "**目标**"，请选择要在其中创建故障转移虚拟机的订阅和资源组。 选择要在 Azure 中用于故障转移 Vm 的部署模型。
2. 选择 Azure Vm 在故障转移后将连接到的 Azure 网络和子网。 网络必须与 Site Recovery 服务保管库位于同一区域。

   选择 "**立即为选定的计算机配置**"，将网络设置应用到选择保护的所有虚拟机。 选择 "**稍后配置**" 以选择每个虚拟机的 Azure 网络。 如果没有网络，需要创建一个。 若要使用 Azure 资源管理器创建网络，请选择 "**新建**"。 选择一个子网（如果适用），然后选择 **"确定"** 。
   
   ![启用复制目标窗口](./media/vmware-azure-enable-replication/enable-rep3.png)

1. 对于**虚拟机** > **选择 "虚拟**机"，请选择要复制的每个虚拟机。 只能选择可为其启用复制的虚拟机。 然后选择“确定”。 如果看不到或无法选择任何特定虚拟机，请参阅["Azure 门户中未列出源计算机](https://aka.ms/doc-plugin-VM-not-showing)以解决此问题。

    ![启用复制选择虚拟机窗口](./media/vmware-azure-enable-replication/enable-replication5.png)

1.  > **配置属性**的**属性**，请选择进程服务器用于在虚拟机上自动安装 Site Recovery 移动服务的帐户。 另外，根据数据改动模式，选择要复制到的目标托管磁盘的类型。
10. 默认情况下，将复制源虚拟机的所有磁盘。 若要从复制中排除磁盘，请清除不想复制的任何磁盘的 "**包含**" 复选框。 然后选择“确定”。 可以稍后再设置其他属性。 详细了解如何[排除磁盘](vmware-azure-exclude-disk.md)。

    ![启用复制配置属性窗口](./media/vmware-azure-enable-replication/enable-replication6.png)

1. 在 "**复制设置**" > **配置复制设置**"，验证是否选择了正确的复制策略。 可以在 "**设置**" > "**复制**策略" > ***策略名称*** > "**编辑设置**" 中修改复制策略设置。 应用于策略的更改也适用于复制虚拟机和新虚拟机。
1. 如果要将虚拟机收集到复制组，请启用**多 VM 一致性**。 指定组的名称，然后选择 **"确定"** 。

    > [!NOTE]
    >    * 复制组中的虚拟机一起复制，并在故障转移时使用共享的、崩溃一致且应用一致的恢复点。
    >    * 将 VM 和物理服务器集合在一起，使其镜像工作负荷。 启用多 VM 一致性可能影响工作负荷性能。 仅当虚拟机运行相同的工作负荷并且需要一致性时，才执行此操作。

    ![启用复制窗口](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. 选择“启用复制”。 可以在 "**设置**" > **作业** > **Site Recovery "作业**中跟踪"**启用保护**"作业的进度。 在“完成保护”作业运行之后，虚拟机就可以进行故障转移了。

## <a name="view-and-manage-vm-properties"></a>查看和管理 VM 属性

接下来，验证源虚拟机的属性。 请记住，Azure VM 名称需要符合 [Azure 虚拟机要求](vmware-physical-azure-support-matrix.md#replicated-machines)。

1. 请参阅 "**设置**" > "**复制的项**"，然后选择虚拟机。 "**概要**" 页显示有关 VM 的设置和状态的信息。
1. 在“属性”中，可以查看 VM 的复制和故障转移信息。
1. 在**计算和网络** > **计算属性**中，你可以更改多个 VM 属性。 

    !["计算和网络" 属性窗口](./media/vmware-azure-enable-replication/vmproperties.png)

    * Azure VM 名称：根据需要修改名称以满足 Azure 要求。
    * 目标 VM 大小或 VM 类型：根据在目标 Azure 区域中包含磁盘计数、NIC 计数、CPU 核心数、内存和可用 VM 角色大小的几个参数，选择默认 VM 大小。 Azure Site Recovery 选取满足所有条件的第一个可用 VM 大小。 在故障转移之前，你可以根据需要选择不同的 VM 大小。 请注意，VM 磁盘大小也基于源磁盘大小，只能在故障转移后更改。 了解有关[Windows 上的 VM 磁盘的可伸缩性和性能目标的](../virtual-machines/windows/disk-scalability-targets.md)磁盘大小和 IOPS 速率的详细信息。

    *  资源组：可以选择一个[资源组](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines)，其中的虚拟机将成为故障转移后的一部分。 你可以在故障转移之前随时更改此设置。 故障转移后，如果将虚拟机迁移到其他资源组，则该虚拟机的保护设置会中断。
    * 可用性集：如果虚拟机需要成为故障转移后的一部分，则可以选择[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)。 选择可用性集时，请记住以下信息：

        * 仅列出属于指定资源组的可用性集。  
        * 位于不同虚拟网络中的 Vm 不能属于同一可用性集。
        * 仅大小相同的虚拟机可以属于同一可用性集。
1. 你还可以添加有关分配给 Azure VM 的目标网络、子网和 IP 地址的信息。
2. 在“磁盘”中，可以看到 VM 上将要复制的操作系统和数据磁盘。

### <a name="configure-networks-and-ip-addresses"></a>配置网络和 IP 地址

可以设置目标 IP 地址。 如果未提供地址，则已故障转移的虚拟机将使用 DHCP。 如果设置的地址在故障转移时不可用，则故障转移不会正常工作。 如果该地址在测试故障转移网络中可用，则可以使用相同的目标 IP 地址进行测试故障转移。

网络适配器的数目按你为目标虚拟机指定的大小来决定，如下所示：

- 如果源虚拟机上的网络适配器数小于或等于目标 VM 大小允许的适配器数，则目标的适配器数将与源的适配器数量相同。
- 如果源虚拟机的适配器数目超出了目标 VM 大小允许的数目，则使用目标大小的最大值。 例如，如果源虚拟机有两个网络适配器，而目标 VM 的大小支持四个，则目标虚拟机有两个适配器。 如果源 VM 有两个适配器，但目标大小仅支持一个，则目标 VM 只有一个适配器。
- 如果虚拟机有多个网络适配器，它们会全部连接到同一网络。 此外，列表中显示的第一个适配器将成为 Azure 虚拟机中的*默认*网络适配器。 

### <a name="azure-hybrid-benefit"></a>Azure 混合权益

Microsoft 软件保障客户可以使用 Azure 混合权益来节省迁移到 Azure 的 Windows Server 计算机的许可成本。 此权益还适用于 Azure 灾难恢复。 如果有资格，则可以将权益分配给 Site Recovery 在发生故障转移时创建的虚拟机。 为此，请执行以下步骤：
1. 中转到复制的虚拟机的 "**计算机" 和 "网络" 属性**。
2. 当系统询问你是否有资格使用 Azure 混合权益的 Windows Server 许可证时，请回答。
3. 确认你具有具有软件保障的符合条件的 Windows Server 许可证，你可以使用它来将权益应用到将在故障转移时创建的 VM。
4. 保存已复制虚拟机的设置。

深入了解 [Azure 混合权益](https://aka.ms/azure-hybrid-benefit-pricing)。



## <a name="next-steps"></a>后续步骤

虚拟机进入受保护状态后，尝试[故障转移](site-recovery-failover.md)以检查应用程序是否出现在 Azure 中。

* 若要禁用保护，请了解如何[清理注册和保护设置](site-recovery-manage-registration-and-protection.md)。
* 了解如何[使用 Powershell 自动执行虚拟机复制](vmware-azure-disaster-recovery-powershell.md)。
