---
title: 在从本地故障转移到 Azure 后，通过 Azure Site Recovery 连接到 Azure Vm
description: 介绍如何使用 Azure Site Recovery 从本地故障转移到 Azure 后连接到 Azure Vm
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: f535a681ac3508aafc2823bcc9b9ae7f22cc2d8e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333045"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>在从本地故障转移后连接到 Azure Vm 


本文介绍如何设置连接，以便在故障转移后能够成功连接到 Azure Vm。

将本地虚拟机（Vm）和物理服务器的灾难恢复设置为 Azure 时， [Azure Site Recovery](site-recovery-overview.md)会开始将计算机复制到 azure。 然后，当发生中断时，可以从本地站点故障转移到 Azure。 发生故障转移时，Site Recovery 使用复制的本地数据创建 Azure Vm。 作为灾难恢复计划的一部分，你需要了解如何在故障转移后连接到在这些 Azure Vm 上运行的应用。

本文介绍如何执行以下操作：

> [!div class="checklist"]
> * 在故障转移之前准备本地计算机。
> * 故障转移后准备 Azure Vm。 
> * 故障转移后保留 Azure Vm 上的 IP 地址。
> * 在故障转移后将新的 IP 地址分配给 Azure Vm。

## <a name="prepare-on-premises-machines"></a>准备本地计算机

若要确保连接到 Azure Vm，请在故障转移之前准备本地计算机。

### <a name="prepare-windows-machines"></a>准备 Windows 计算机

在本地 Windows 计算机上执行以下操作：

1. 配置 Windows 设置。 这包括删除任何静态持久路由或 WinHTTP 代理，并将磁盘 SAN 策略设置为**OnlineAll**。 [按照](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure)这些说明进行操作。

2. 确保[这些服务](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)正在运行。

3. 启用远程桌面 (RDP)，以便能够远程连接到本地计算机。 [了解如何](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)使用 PowerShell 启用 RDP。

4. 若要在故障转移后通过 internet 访问 Azure VM，请在本地计算机上的 Windows 防火墙中，在公用配置文件中允许 TCP 和 UDP，并将 RDP 设置为适用于所有配置文件的允许的应用。

5. 如果要在故障转移后通过站点到站点 VPN 访问 Azure VM，请在本地计算机上的 Windows 防火墙中，为域和专用配置文件允许 RDP。 [了解](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)如何允许 RDP 流量。
6. 在触发故障转移时，请确保在本地 VM 上没有任何 Windows 更新处于挂起状态。 如果有，则在故障转移后，更新可能会在 Azure VM 上开始安装，并且在更新完成之前，你将无法登录到 VM。

### <a name="prepare-linux-machines"></a>准备 Linux 计算机

在本地 Linux 计算机上执行以下操作：

1. 检查安全外壳服务是否设置为在系统引导时自动启动。
2. 确保防火墙规则允许 SSH 连接。


## <a name="configure-azure-vms-after-failover"></a>故障转移后配置 Azure Vm

故障转移后，请在创建的 Azure Vm 上执行以下操作。

1. 若要通过 Internet 连接到 VM，请将公共 IP 地址分配到该 VM。 不能将本地计算机所用的同一个公共 IP 地址用于 Azure VM。 [了解详细信息](../virtual-network/virtual-network-public-ip-address.md)
2. 检查 VM 上的网络安全组 (NSG) 规则是否允许与 RDP 或 SSH 端口建立传入连接。
3. 在[启动诊断](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine)中查看 VM。


> [!NOTE]
> Azure Bastion 服务提供对 Azure VM 的 RDP 和 SSH 私密访问。 [详细了解](../bastion/bastion-overview.md)此服务。

## <a name="set-a-public-ip-address"></a>设置公共 IP 地址

作为将公共 IP 地址手动分配给 Azure VM 的替代方法，可以在故障转移过程中使用脚本或 Azure 自动化 runbook 在 Site Recovery[恢复计划](site-recovery-create-recovery-plans.md)中分配该地址，也可以使用 Azure 流量管理器设置 DNS 级别的路由。 [了解](concepts-public-ip-address-with-site-recovery.md)有关设置公用地址的详细信息。


## <a name="assign-an-internal-address"></a>分配内部地址

若要在故障转移后设置 Azure VM 的内部 IP 地址，可以使用以下几个选项：

- **保留相同的 ip 地址**：你可以在 Azure VM 上使用与分配给本地计算机的 ip 地址相同的 ip 地址。
- **使用不同的 ip 地址**：可以为 Azure VM 使用不同的 ip 地址。


## <a name="retain-ip-addresses"></a>保留 IP 地址

Site Recovery 使你可以在故障转移到 Azure 时保留相同的 IP 地址。 保留相同的 IP 地址可避免在故障转移后出现潜在的网络问题，但这会带来一些复杂性。

- 如果目标 Azure VM 使用与本地站点相同的 IP 地址/子网，则无法使用站点到站点 VPN 连接或 ExpressRoute 在它们之间进行连接，因为地址重叠。 子网必须是唯一的。
- 故障转移后，需要从本地连接到 Azure，以便可以在 Azure Vm 上使用应用。 Azure 不支持延伸 Vlan，因此，如果想要保留 IP 地址，则除了本地计算机之外，还需要对整个子网进行故障转移，从而将 IP 空间通过故障转移到 Azure。
- 子网故障转移可确保在本地和 Azure 中不能同时使用特定子网。

保留 IP 地址需要执行以下步骤：

- 在 "计算" & 复制项的网络属性中，为目标 Azure VM 设置网络和 IP 寻址以镜像本地设置。
- 在灾难恢复过程中，必须管理子网。 需要 Azure VNet 才能匹配本地网络，并且在故障转移后，必须修改故障转移网络路由，以反映子网已移动到 Azure 和新的 IP 地址位置。  

### <a name="failover-example"></a>故障转移示例

接下来举例说明。

- 虚构的公司 Woodgrove Bank 在本地托管其业务应用程序，在 Azure 中托管其移动应用。
- 它们通过站点到站点 VPN 从本地连接到 Azure。 
- Woodgrove 正在使用 Site Recovery 将本地计算机复制到 Azure。
- 其本地应用使用硬编码的 IP 地址，因此他们想要在 Azure 中保留相同的 IP 地址。
- 本地运行应用的计算机运行在三个子网中：
    - 192.168.1.0/24。
    - 192.168.2.0/24
    - 192.168.3.0/24
- 它们在 Azure 中运行的应用位于两个子网中的 Azure VNet **Azure 网络**中：
- 172.16.1.0/24
- 172.16.2.0/24。

若要保留地址，请执行以下操作。

1. 当它们启用复制时，它们指定计算机应复制到**Azure 网络**。
2. 它们在 Azure 中创建**恢复网络**。 此 VNet 反映其本地网络中的 192.168.1.0/24 子网。
3. Woodgrove 设置两个网络之间的[vnet 到 vnet 连接](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)。 

    > [!NOTE]
    > 根据应用程序的要求，可以在故障转移之前设置 VNet 到 VNet 连接，作为 Site Recovery[恢复计划](site-recovery-create-recovery-plans.md)中的手动步骤/脚本步骤/Azure 自动化 runbook，或在故障转移完成后。

4. 在故障转移之前，在 Site Recovery 中的计算机属性上，它们将目标 IP 地址设置为本地计算机的地址，如以下过程中所述。
5. 故障转移后，将创建具有相同 IP 地址的 Azure Vm。 Woodgrove 使用 VNet 对等互连（启用了传输连接）从**Azure 网络**连接到**恢复网络**VNet。
6. 在本地，Woodgrove 需要进行网络更改，包括修改路由，以反映 192.168.1.0/24 已移到 Azure。  

**故障转移前的基础结构**

![运行子网故障转移前](./media/site-recovery-network-design/network-design7.png)


**故障转移后的基础结构**

![运行子网故障转移后](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>设置目标网络设置

在故障转移之前，请为目标 Azure VM 指定网络设置和 IP 地址。

1.  在 "恢复服务保管库->**复制的项**" 中，选择 "本地计算机"。
2. 在计算机的 "**计算和网络**" 页中，单击 "**编辑**" 以配置目标 Azure VM 的网络和适配器设置。
3. 在 "**网络属性**" 中，选择 Azure VM 在故障转移后创建时所在的目标网络。
4. 在 "**网络接口**" 中，配置目标网络中的网络适配器。 默认情况下 Site Recovery 显示本地计算机上检测到的所有 Nic。
    - 在**目标网络接口类型**中，你可以将每个 nic 设置为**主**nic、**辅助**nic，如果不需要目标网络中的特定 nic，则**不创建**。 必须将一个网络适配器设置为故障转移的主网络。 请注意，修改目标网络会影响 Azure VM 的所有 Nic。
    - 单击 NIC 名称以指定将在其中部署 Azure VM 的子网。
    - 用要分配给目标 Azure VM 的专用 IP 地址覆盖**动态**。 如果未指定 IP 地址 Site Recovery 会在故障转移时将子网中的下一个可用 IP 地址分配给 NIC。
    - [详细了解](site-recovery-manage-network-interfaces-on-premises-to-azure.md)如何管理用于本地故障转移到 Azure 的 nic。


## <a name="get-new-ip-addresses"></a>获取新的 IP 地址

在此方案中，Azure VM 在故障转移后获取新的 IP 地址。 DNS 更新，用于更新已故障转移的计算机的记录，使其指向 Azure VM 的 IP 地址。



## <a name="next-steps"></a>后续步骤
[了解](site-recovery-active-directory.md)如何将本地 ACTIVE DIRECTORY 和 DNS 复制到 Azure。


