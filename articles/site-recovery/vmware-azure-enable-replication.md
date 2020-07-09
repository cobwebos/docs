---
title: 使用 Azure Site Recovery 为灾难恢复启用 VMware VM
description: 本文介绍如何使用 Azure Site Recovery 服务启用本地 VMware VM 的复制，以便进行灾难恢复
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 04/01/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 74870d10348421bf726b9bdc58504a74cf4105a9
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86129923"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>为 VMware VM 启用到 Azure 的复制

本文介绍如何启用对本地 VMware 虚拟机(VM)到 Azure 的复制。

## <a name="prerequisites"></a>先决条件

本文假设系统满足以下条件：

- [设置本地源环境](vmware-azure-set-up-source.md)。
- [在 Azure 中设置目标环境](vmware-azure-set-up-target.md)。
- 在开始之前，请[验证要求和先决条件](vmware-physical-azure-support-matrix.md)。 需要注意的几个要点：
  - 复制的计算机[支持的操作系统](vmware-physical-azure-support-matrix.md#replicated-machines)。
  - [存储/磁盘](vmware-physical-azure-support-matrix.md#storage)支持。
  - 本地计算机应遵循的 [Azure 要求](vmware-physical-azure-support-matrix.md#azure-vm-requirements)。

### <a name="resolve-common-issues"></a>解决常见问题

- 每个磁盘的大小应小于 4 TB。
- 操作系统磁盘应是基本磁盘而不是动态磁盘。
- 对于第 2 代已启用 UEFI 的虚拟机，操作系统系列应是 Windows，并且启动盘应小于 300 GB。

## <a name="before-you-start"></a>开始之前

复制 VMware 虚拟机时，请注意以下信息：

- Azure 用户帐户需要具有某些[权限](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)，才能启用新的虚拟机到 Azure 的复制。
- 系统会每隔 15 分钟发现 VMware VM 一次。 发现之后，可能需要 15 分钟或更长时间，VM 才会出现在 Azure 门户中。 添加新的 vCenter 服务器或 vSphere 主机时，发现可能需要 15 分钟或更长时间。
- 可能需要 15 分钟或更长时间虚拟机上的环境更改才能在门户中更新。 例如，VMware 工具安装。
- 可以检查 VMware VM 的上次发现时间：查看 vCenter 服务器/vSphere 主机的“配置服务器”页上的“上次联系时间”字段。 
- 若要添加要复制的虚拟机且不想要等待执行计划的发现完成，请突出显示配置服务器（不要单击它），然后选择“刷新”。
- 启用复制时，如果虚拟机已做好准备，进程服务器会自动在 VM 上安装 Azure Site Recovery 移动性服务。

## <a name="enable-replication"></a>启用复制

执行本部分中的步骤之前，请注意以下信息：

- Azure Site Recovery 现在会将所有新复制项直接复制到托管磁盘。 进程服务器将复制日志写入到目标区域中的缓存存储帐户。 这些日志用于在具有 `asrseeddisk` 命名约定的副本托管磁盘中创建恢复点。
- 自 [Az.RecoveryServices 模块版本 2.0.0 ](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 起，PowerShell 支持对托管磁盘的复制
- 故障转移时，所选的恢复点将用于创建目标托管磁盘。
- 以前配置为复制到目标存储帐户的 VM 不受影响。
- 只能通过表述性状态转移 (REST) API 或 PowerShell 实现复制到新虚拟机的存储帐户。 使用 Azure REST API 版本 2016-08-10 或 2018-01-10 来复制到存储帐户。

若要启用复制，请遵循以下步骤：

1. 转到“步骤 2：复制应用程序” > “源”。 首次启用复制后，请在保管库中选择“+复制”，对其他虚拟机启用复制。
1. 在“源”页 >“源”中，选择配置服务器。
1. 对于“计算机类型”，请选择“虚拟机”或“物理机”。  
1. 在“vCenter/vSphere 虚拟机监控程序”中，选择管理 vSphere 主机的 vCenter 服务器，或选择该主机。 如果要复制物理计算机，则此设置无关紧要。
1. 选择进程服务器。 如果未创建任何其他进程服务器，则下拉菜单中将提供配置服务器的内置进程服务器。 根据建议的限制和其他参数显示每个进程服务器的运行状况状态。 选择一个正常运行的进程服务器。 不能选择[有严重错误的](vmware-physical-azure-monitor-process-server.md#process-server-alerts)进程服务器。 你可以[进行故障排除并解决](vmware-physical-azure-troubleshoot-process-server.md)错误**或者**设置一个[横向扩展进程服务器](vmware-azure-set-up-process-server-scale.md)。

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="“启用复制源”窗口":::

   > [!NOTE]
   > 从 [9.24 版本](site-recovery-whats-new.md)开始，引入了其他警报，以增强进程服务器的运行状况警报。 将 Site Recovery 组件升级到 9.24 版或更高版本，以便生成所有警报。

1. 对于“目标”，请选择要在其中创建故障转移虚拟机的订阅和资源组。 选择要在 Azure 中用于故障转移 VM 的部署模型。
1. 选择 Azure VM 在故障转移后所要连接的 Azure 网络和子网。 该网络必须位于与 Site Recovery 服务保管库相同的区域中。

   选择“立即为选定的计算机配置”，将网络设置应用到选择保护的所有虚拟机。 选择“稍后配置”以选择每个虚拟机的 Azure 网络。 如果没有网络，需要创建一个。 若要使用 Azure 资源管理器创建网络，请选择“新建”。 选择适用的子网，然后选择“确定”。

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="启用复制目标窗口":::

1. 在“虚拟机” > “选择虚拟机”中，选择要复制的每个虚拟机 。 只能选择可以启用复制的虚拟机。 然后选择“确定”。 如果无法查看或选择任何特定的虚拟机，请参阅[源计算机未在 Azure 门户中列出](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication)以解决此问题。

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="“启用复制”>“选择虚拟机”窗口":::

1. 在“属性” > “配置属性”中，选择进程服务器使用的帐户，以在虚拟机上自动安装 Site Recovery 移动性服务。  此外，请根据数据改动模式选择要用于复制的目标托管磁盘的类型。
1. 默认会复制源 VM 的所有磁盘。 若要从复制中排除磁盘，请清除不想要复制的所有磁盘对应的“包括”复选框。 然后选择“确定”。 可以稍后再设置其他属性。 [详细了解](vmware-azure-exclude-disk.md)如何排除磁盘。

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="“启用复制”>“配置属性”窗口":::

1. 在“复制设置” > “配置复制设置”中，检查是否选择了正确的复制策略。  可以在“设置” > “复制策略” >  策略名称  > “编辑设置”中修改复制策略设置。  应用于策略的更改也会应用于复制和新的虚拟机。
1. 若要将虚拟机集合到一个复制组，请启用“多 VM 一致性”。 指定组的名称，然后选择“确定”。

   > [!NOTE]
   > - 复制组中的虚拟机将一起复制，并在故障转移时共享崩溃一致和应用一致恢复点。
   > - 将 VM 和物理服务器集合在一起，使其镜像工作负荷。 启用多 VM 一致性可能影响工作负荷性能。 仅在虚拟机正在运行相同的工作负荷并且需要一致性的情况下，才执行此操作。

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="“启用复制”窗口":::

1. 选择“启用复制”。 可以在“设置” > “作业” > “Site Recovery 作业”中，跟踪“启用保护”作业的进度   。 在“完成保护”作业运行之后，虚拟机就可以进行故障转移了。

## <a name="view-and-manage-vm-properties"></a>查看和管理 VM 属性

接下来，验证源虚拟机的属性。 请记住，Azure VM 名称需要符合 [Azure 虚拟机要求](vmware-physical-azure-support-matrix.md#replicated-machines)。

1. 中转到 "**设置**  >  " "**复制的项**"，然后选择虚拟机。 “概要”页显示有关 VM 设置和状态的信息。
1. 在“属性”中，可以查看 VM 的复制和故障转移信息。
1. 在“计算和网络” > “计算属性”中，可以更改多个 VM 属性：

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="“计算和网络属性”窗口":::

   - **Azure VM 名称**：根据需要修改名称以使其符合 Azure 要求。
   - **目标 VM 大小或 VM 类型**：基于一些参数选择默认 VM 大小，这些参数包括目标 Azure 区域中的磁盘计数、NIC 计数、CPU 核心计数、内存和可用 VM 角色大小。 Azure Site Recovery 将选取满足所有条件的第一个可用 VM 大小。 在故障转移之前，随时可以根据需要选择不同的 VM 大小。 VM 磁盘大小还取决于源磁盘大小，并且它只能在故障转移后进行更改。 在 [Windows 上的 VM 磁盘的可伸缩性和性能目标](../virtual-machines/windows/disk-scalability-targets.md)中了解磁盘大小和 IOPS 速率。
   - **资源组**：可以选择虚拟机会在故障转移后成为其中一部分的[资源组](../azure-resource-manager/management/overview.md#resource-groups)。 在故障转移之前，随时可以更改此设置。 故障转移之后，如果将虚拟机迁移到其他资源组，则会中断该虚拟机的保护设置。
   - **可用性集**：如果需要虚拟机在故障转移后成为某个[可用性集](../virtual-machines/windows/tutorial-availability-sets.md)的一部分，可以选择一个可用性集。 选择可用性集时，请注意以下信息：
     - 仅会列出属于指定资源组的可用性集。
     - 位于不同虚拟网络中的 VM 不能属于同一个可用性集。
     - 仅大小相同的虚拟机可以属于同一可用性集。

1. 还可以添加有关目标网络、子网和分配给 Azure VM 的 IP 地址的信息。
1. 在“磁盘”中，可以看到 VM 上将要复制的操作系统和数据磁盘。

### <a name="configure-networks-and-ip-addresses"></a>配置网络和 IP 地址

可以设置目标 IP 地址：

- 如果未提供地址，故障转移的 VM 会使用 DHCP。
- 如果设置的地址在故障转移时不可用，则故障转移不会正常工作。
- 如果地址可用于测试故障转移网络，则可以使用同一目标 IP 地址执行测试故障转移。

网络适配器数目根据为目标虚拟机指定的大小来确定，如下所述：

- 如果源虚拟机上的网络适配器数小于或等于目标 VM 大小允许的适配器数，则目标虚拟机拥有与源虚拟机相同的适配器数。
- 如果源虚拟机的适配器数大于目标 VM 大小允许的数目，则使用目标大小允许的最大数目。 例如，如果源虚拟机有两个网络适配器，而目标 VM 大小支持四个，则目标虚拟机有两个适配器。 如果源 VM 有两个适配器，但目标大小仅支持一个，则目标 VM 只有一个适配器。
- 如果虚拟机有多个网络适配器，它们会全部连接到同一网络。 另外，列表中显示的第一个适配器会成为 Azure 虚拟机中的默认网络适配器。

### <a name="azure-hybrid-benefit"></a>Azure 混合权益

Microsoft 软件保障客户可以使用 Azure 混合权益来节省迁移到 Azure 的 Windows Server 计算机的许可成本。 该权益也适用于 Azure 灾难恢复。 如果符合条件，你可以在故障转移时，将该权益分配到 Azure Site Recovery 创建的虚拟机。

1. 转到复制的虚拟机的“计算和网络属性”。
1. 当系统询问你是否具有可提供 Azure 混合权益的 Windows Server 许可证时，请给予回答。
1. 确认你具有附带软件保障的符合条件的 Windows Server 许可证，可以使用它来对在故障转移时创建的 VM 应用 Azure 混合权益。
1. 保存复制的虚拟机的设置。

[深入了解](https://azure.microsoft.com/pricing/hybrid-benefit/) Azure 混合权益。

## <a name="next-steps"></a>后续步骤

虚拟机进入保护状态后，请尝试[故障转移](site-recovery-failover.md)，检查应用程序是否显示在 Azure 中。

- [详细了解](site-recovery-manage-registration-and-protection.md)如何清理注册和保护设置以禁用复制。
- [详细了解](vmware-azure-disaster-recovery-powershell.md)如何使用 PowerShell 自动复制虚拟机。
