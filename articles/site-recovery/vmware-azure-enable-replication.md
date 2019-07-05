---
title: 启用 VMware Vm 复制到 Azure 中使用 Azure Site Recovery 的灾难恢复 |Microsoft Docs
description: 本文介绍如何使用 Azure Site Recovery 将 VMware VM 复制到 Azure，以实现灾难恢复。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 3f4e4afb4d94a7b2e2a6b246a371cf6234577463
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491736"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>为 VMware VM 启用到 Azure 的复制

本文介绍如何启用将本地 VMware VM 复制到 Azure。

## <a name="prerequisites"></a>必备组件

本文假设用户具备以下条件：

- [设置本地源环境](vmware-azure-set-up-source.md)。
- [在 Azure 中设置目标环境](vmware-azure-set-up-target.md)。

## <a name="before-you-start"></a>开始之前
复制 VMware 虚拟机时，请注意以下信息：

* Azure 用户帐户需要具有某些[权限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能将新的虚拟机复制到 Azure。
* 系统会每隔 15 分钟发现 VMware VM 一次。 发现之后，可能需要 15 分钟或更长时间，VM 才会出现在 Azure 门户中。 同样，添加新的 vCenter 服务器或 vSphere 主机时，发现可能要花费 15 分钟或更长时间。
* 虚拟机上的环境更改（例如 VMware 工具安装）可能需要 15 分钟或更长时间才能在门户中更新。
* 可以检查 VMware VM 的上次发现时间：查看 vCenter 服务器/vSphere 主机的“配置服务器”页上的“上次联系时间”字段。  
* 若要添加为复制而不想等待计划的发现的虚拟机，请突出显示配置服务器 （但不要单击它），然后选择**刷新**。
* 启用复制时，如果虚拟机已做好准备，进程服务器自动在其上安装 Azure Site Recovery 移动服务。

## <a name="enable-replication"></a>启用复制

在遵循本部分中的步骤之前，请注意以下信息：
* Azure Site Recovery 现在会将所有新复制项直接复制到托管磁盘。 进程服务器将复制日志写入到目标区域中的缓存存储帐户。 这些日志用于在具有 asrseeddisk 的命名约定的副本托管磁盘中创建恢复点。
* 将复制到托管磁盘的 Powershell 支持，但可从[Az.RecoveryServices 模块 2.0.0 版及更高版本](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 
* 故障转移时，所选的恢复点将用于创建目标托管磁盘。
* 以前配置为复制到目标存储帐户的 VM 不受影响。
* 只能通过表述性状态转移 (REST) API 或 Powershell 复制到新虚拟机的存储帐户。 使用 Azure REST API 版本 2016-08-10 或 2018-01-10 复制到存储帐户。

请按以下步骤启用复制：
1. 转到“步骤 2：  复制应用程序” >   “源”。 首次启用复制后，请在保管库中选择“+复制”，对其他虚拟机启用复制  。
2. 在“源”  页 >“源”  中，选择配置服务器。
3. 对于“计算机类型”，请选择“虚拟机”或“物理机”。   
4. 在“vCenter/vSphere 虚拟机监控程序”中，选择管理 vSphere 主机的 vCenter 服务器，或选择该主机。  如果要复制物理计算机，则此设置无关紧要。
5. 选择进程服务器。 如果不没有创建任何额外的进程服务器，配置服务器的内置的进程服务器将在下拉列表中可用。 每个进程服务器的运行状况状态是根据建议的限制和其他参数指示的。 选择一个正常运行的进程服务器。 不能选择[有严重错误的](vmware-physical-azure-monitor-process-server.md#process-server-alerts)进程服务器。 你可以[进行故障排除并解决](vmware-physical-azure-troubleshoot-process-server.md)错误**或者**设置一个[横向扩展进程服务器](vmware-azure-set-up-process-server-scale.md)。
    ![启用复制源窗口](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> 从[9.24 版本](service-updates-how-to.md#links-to-currently-supported-update-rollups)，引入了其他警报以增强的进程服务器的运行状况警报。 若要生成的所有警报的都升级到 9.24 版本或更高版本的 Site Recovery 组件。

6. 对于“目标”，请选择要在其中创建故障转移虚拟机的订阅和资源组。  选择要在 Azure 中对故障转移的 VM 使用的部署模型。
2. 选择 Azure VM 在故障转移后所要连接的 Azure 网络和子网。 该网络必须位于与 Site Recovery 服务保管库相同的区域中。

   选择“立即为选定的计算机配置”  ，将网络设置应用到选择保护的所有虚拟机。 选择“稍后配置”  以选择每个虚拟机的 Azure 网络。 如果没有网络，需要创建一个。 若要使用 Azure 资源管理器创建网络，请选择“新建”。  选择适用的子网，然后选择“确定”  。
   
   ![“启用复制目标”窗口](./media/vmware-azure-enable-replication/enable-rep3.png)

1. 在“虚拟机” > “选择虚拟机”中，选择要复制的每个虚拟机   。 只能选择可以启用复制的虚拟机。 然后选择“确定”。  如果无法查看或选择任何特定的虚拟机，请参阅[源计算机未在 Azure 门户中列出](https://aka.ms/doc-plugin-VM-not-showing)以解决此问题。

    ![“启用复制”>“选择虚拟机”窗口](./media/vmware-azure-enable-replication/enable-replication5.png)

1. 在“属性” > “配置属性”中，选择进程服务器在虚拟机上自动安装 Site Recovery 移动服务时使用的帐户   。 此外，请根据数据变动模式选择要复制到的目标托管磁盘的类型。
10. 默认会复制源虚拟机的所有磁盘。 若要从复制中排除磁盘，请清除不想要复制的所有磁盘对应的“包括”复选框。  然后选择“确定”。  可以稍后再设置其他属性。 详细了解如何[排除磁盘](vmware-azure-exclude-disk.md)。

    ![“启用复制”>“配置属性”窗口](./media/vmware-azure-enable-replication/enable-replication6.png)

1. 在“复制设置” > “配置复制设置”中，检查是否选择了正确的复制策略   。 可以在“设置” > “复制策略” >  策略名称  > “编辑设置”中修改复制策略设置。     对策略的更改也会应用于复制的虚拟机和新虚拟机。
1. 若要将虚拟机集合到一个复制组，请启用“多 VM 一致性”  。 指定组的名称，然后选择“确定”  。

    > [!NOTE]
    >    * 复制组中的虚拟机将一起复制，并在故障转移时共享崩溃一致和应用一致恢复点。
    >    * 将 VM 和物理服务器集合在一起，使其镜像工作负荷。 启用多 VM 一致性可能影响工作负荷性能。 仅在虚拟机正在运行相同的工作负荷并且需要一致性的情况下，才执行此操作。

    ![“启用复制”窗口](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. 选择“启用复制”。  可以在“设置” > “作业” > “Site Recovery 作业”中，跟踪“启用保护”作业的进度     。 在“完成保护”作业运行之后，虚拟机就可以进行故障转移了。 

## <a name="view-and-manage-vm-properties"></a>查看和管理 VM 属性

接下来，验证源虚拟机的属性。 请记住，Azure VM 名称需要符合 [Azure 虚拟机要求](vmware-physical-azure-support-matrix.md#replicated-machines)。

1. 转到“设置” > “复制的项”，然后选择虚拟机   。 “概要”  页显示有关 VM 设置和状态的信息。
1. 在“属性”  中，可以查看 VM 的复制和故障转移信息。
1. 在“计算和网络”   > “计算属性”  中，可以更改多个 VM 属性： 

    ![“计算和网络属性”窗口](./media/vmware-azure-enable-replication/vmproperties.png)

    * Azure VM 名称：根据需要修改名称以使其符合 Azure 要求。
    * 目标 VM 大小或 VM 类型：根据包含在目标 Azure 区域中的磁盘数、 NIC 计数、 CPU 核心计数、 内存和可用的 VM 角色大小的几个参数选择的默认 VM 大小。 Azure Site Recovery 会选择第一个的可用 VM 大小这样可满足所有条件。 在故障转移之前，随时可以根据需要选择不同的 VM 大小。 请注意，VM 磁盘大小也取决于源磁盘大小，并且它只能在故障转移后进行更改。 在 [Windows 上的 VM 磁盘的可伸缩性和性能目标](../virtual-machines/windows/disk-scalability-targets.md)中了解磁盘大小和 IOPS 速率。

    *  资源组：可以选择虚拟机会在故障转移后成为其中一部分的[资源组](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines)。 在故障转移之前，随时可以更改此设置。 故障转移之后，如果将虚拟机迁移到其他资源组，则会中断该虚拟机的保护设置。
    * 可用性集：如果需要虚拟机在故障转移后成为某个[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)的一部分，可以选择一个可用性集。 选择可用性集时，请注意以下信息：

        * 仅会列出属于指定资源组的可用性集。  
        * 位于不同虚拟网络中的 VM 不能属于同一个可用性集。
        * 仅大小相同的虚拟机可以属于同一可用性集。
1. 还可以添加有关目标网络、子网和分配给 Azure VM 的 IP 地址的信息。
2. 在“磁盘”中，可以看到 VM 上将要复制的操作系统和数据磁盘。 

### <a name="configure-networks-and-ip-addresses"></a>配置网络和 IP 地址

可以设置目标 IP 地址。 如果未提供地址，故障转移的虚拟机将使用 DHCP。 如果设置的地址在故障转移时不可用，则故障转移不会正常工作。 如果地址可用于测试故障转移网络，则可以使用同一目标 IP 地址执行测试故障转移。

网络适配器数目根据为目标虚拟机指定的大小来确定，如下所述：

- 如果源虚拟机上的网络适配器数小于或等于目标 VM 大小允许的适配器数，则目标虚拟机拥有与源虚拟机相同的适配器数。
- 如果源虚拟机的适配器数大于目标 VM 大小允许的数目，则使用目标大小允许的最大数目。 例如，如果源虚拟机有两个网络适配器，而目标 VM 大小支持四个，则目标虚拟机有两个适配器。 如果源 VM 有两个适配器，但目标大小仅支持一个，则目标 VM 只有一个适配器。
- 如果虚拟机有多个网络适配器，它们会全部连接到同一网络。 另外，列表中显示的第一个适配器会成为 Azure 虚拟机中的默认网络适配器  。 

### <a name="azure-hybrid-benefit"></a>Azure 混合权益

Microsoft 软件保障客户可以使用 Azure 混合权益来节省迁移到 Azure 的 Windows Server 计算机的许可成本。 该权益也适用于 Azure 灾难恢复。 如果符合条件，你可以在故障转移时，将该权益分配到 Azure Site Recovery 创建的虚拟机。 为此，请执行以下步骤：
1. 转到复制的虚拟机的“计算和网络属性”。 
2. 当系统询问你是否具有可提供 Azure 混合权益的 Windows Server 许可证时，请给予回答。
3. 确认你具有附带软件保障的符合条件的 Windows Server 许可证，可以使用它来对在故障转移时创建的 VM 应用 Azure 混合权益。
4. 保存复制的虚拟机的设置。

深入了解 [Azure 混合权益](https://aka.ms/azure-hybrid-benefit-pricing)。

## <a name="resolve-common-issues"></a>解决常见问题

* 每个磁盘的大小应小于 4 TB。
* OS 磁盘应是基本磁盘而不是动态磁盘。
* 对于第 2 代虚拟机/已启用 UEFI 的虚拟机，操作系统系列应是 Windows，并且启动盘应小于 300 GB。

## <a name="next-steps"></a>后续步骤

虚拟机进入保护状态后，请尝试[故障转移](site-recovery-failover.md)，检查应用程序是否显示在 Azure 中。

* 若要禁用保护，请了解如何[清理注册和保护设置](site-recovery-manage-registration-and-protection.md)。
* 了解如何[使用 Powershell 自动复制虚拟机](vmware-azure-disaster-recovery-powershell.md)。
