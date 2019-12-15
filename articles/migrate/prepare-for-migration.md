---
title: 准备好要通过 Azure Migrate 进行迁移的计算机
description: 了解如何准备好要通过 Azure Migrate 进行迁移的本地计算机。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6f5535a57fae847c8a376b8b39e43955675da739
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974778"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>准备好要迁移到 Azure 的本地计算机

本文介绍在开始使用 [Azure Migrate 服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)功能将本地计算机迁移到 Azure 之前，如何准备好这些计算机。


本文内容：
> [!div class="checklist"]
> * 验证迁移限制。
> * 检查操作系统要求和支持限制。
> * 检查要迁移的计算机的 URL/端口访问权限。
> * 检查在开始迁移之前可能需要做出的更改。
> * 配置设置，以便在迁移后保留驱动器号。
> * 准备好计算机，以便在迁移后可以连接到 Azure VM。


## <a name="verify-migration-limitations"></a>验证迁移限制

- 可以使用 Azure Migrate 服务器迁移来评估单个 Azure Migrate 项目中的最多 35,000个 VMware VM/Hyper-V VM。 在项目中可以组合 VMware VM 和 Hyper-V VM，只要不超过每个 VM 的限制即可。
- 一次最多可选择迁移 10 个 VM。 如果需要复制更多的 VM，请以 10 个一组的形式进行复制。
- 对于 VMware 无代理迁移，最多可以同时运行 100 项复制。

## <a name="verify-operating-system-requirements"></a>验证操作系统要求

- 验证 Azure 是否支持你的 [Windows 操作系统](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。
- 验证 Azure 是否支持你的 [Linux 分发版](../virtual-machines/linux/endorsed-distros.md)。


## <a name="check-whats-supported"></a>检查支持的操作

- 对于 VMware VM，Azure Migrate 服务器迁移支持[无代理或基于代理的迁移](server-migrate-overview.md)。 验证[无代理](migrate-support-matrix-vmware.md#migration---limitations)和[基于代理](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements)的迁移的 VMware VM 要求/支持。
- 验证 Hyper-V VM 的[迁移要求和支持](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements)。
- 验证本地物理机或其他虚拟化服务器的[迁移要求和支持](migrate-support-matrix-physical.md)。 




## <a name="review-urlport-access"></a>检查 URL/端口访问权限

在迁移期间，计算机可能需要访问 Internet。

- 检查在[无代理](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements)或[基于代理](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements)的迁移期间 VMware VM 需要访问的 URL。
- 检查在迁移期间 Hyper-V 主机需要访问的 URL。 Hyper-V VM 不需要访问 Internet。
- [检查](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements)在迁移期间物理机或其他虚拟化服务器需要访问的 URL。
- 在 VMware VM/物理服务器的基于代理的迁移过程中，计算机上运行的移动服务需要访问 Azure Migrate 组件。 为了进行复制管理，计算机上运行的服务需要与 HTTPS 443 入站端口上的本地 Azure Migrate 复制设备通信。 计算机通过 HTTPS 9443 入站端口将复制数据发送到 Azure Migrate 进程服务器。 可以修改此端口。


## <a name="verify-required-changes-before-migration"></a>验证迁移之前需要做出的更改

某些 VM 可能需要经过更改才能在 Azure 中运行。 Azure Migrate 会自动对运行以下操作系统的 VM 做出这些更改：
- Red Hat Enterprise Linux 6.5+、7.0+
- CentOS 6.5+、7.0+
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 14.04LTS、16.04LTS、18.04LTS
- Debian 7、8

对于其他操作系统，需要在迁移之前手动准备计算机。 

### <a name="prepare-windows-machines"></a>准备 Windows 计算机

如果迁移的是 Windows 计算机，请在迁移之前做出这些更改。 如果在做出更改之前迁移 VM，VM 可能无法在 Azure 中启动。

1. 为 Azure VM [启用 Azure 串行访问控制台](../virtual-machines/troubleshooting/serial-console-windows.md)。 这有助于完成故障排除。 无需重新启动 VM。 Azure VM 将使用磁盘映像启动。 这相当于重新启动新的 VM。 
2. 如果要迁移的计算机运行 Windows Server 2003，请在 VM 操作系统上安装 Hyper-V 来宾 Integration Services。 [了解详细信息](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services)。

### <a name="prepare-linux-machines"></a>准备 Linux 计算机

1. 安装 Hyper-V Linux Integration Services。 默认情况下，大多数新的 Linux 分发版包含此服务。
2. 重新生成 Linux init 映像，使之包含所需的 Hyper-V 驱动程序。 这可以确保 VM 在 Azure 中启动；只需在某些分发版中执行此操作。
3. [启用 Azure 串行控制台日志记录](../virtual-machines/troubleshooting/serial-console-linux.md)。 这有助于完成故障排除。 无需重新启动 VM。 Azure VM 将使用磁盘映像启动。 这相当于重新启动新的 VM。
4. 更新包含设备名称到卷的关联的设备映射文件，以使用永久设备标识符。
5. 更新 fstab 条目以使用永久卷标识符。
6. 删除基于 MAC 地址等属性保留接口名称的所有 udev 规则。
7. 更新网络接口以通过 DHCP 接收 IP 地址。
8. [详细了解](../virtual-machines/linux/create-upload-generic.md)在 Azure 上运行 Linux VM 所要执行的步骤，并获取适用于某些热门 Linux 分发版的说明。

## <a name="preserve-drive-letters-after-migration"></a>迁移后保留驱动器号

将本地计算机迁移到 Microsoft Azure 时，附加数据磁盘的驱动器号可能会改变，而不使用以前的值。 默认情况下，将为 Azure VM 分配驱动器 D（用作临时存储）。 这种驱动器分配会导致所有其他附加存储驱动器分配递增一个字母。

例如，如果本地安装使用分配了驱动器 D 的数据磁盘（用于应用程序安装），将 VM 迁移到 Azure 后，为此驱动器分配的驱动器号将递增为 E。 若要避免这种自动分配，并确保 Azure 将下一个可用驱动器号分配给其临时卷，请将存储区域网络 (SAN) 策略设置为 OnlineAll，如下所示：

1. 在本地计算机（而不是主机服务器）上，以提升的权限打开命令提示符。
2. 键入 **diskpart**。
3. 键入 **SAN**。 如果未保留来宾操作系统的驱动器号，则会返回 **Offline All** 或 **Offline Shared**。
4. 在 **DISKPART** 提示符下，键入 **SAN Policy=OnlineAll**。 此设置可确保磁盘处于联机状态，并且可读写。
5. 在测试迁移期间，可以验证驱动器号是否已保留。


## <a name="check-azure-vm-requirements"></a>检查 Azure VM 要求

复制到 Azure 的本地计算机必须在操作系统和体系结构、磁盘、网络设置与 VM 命名方面符合 Azure VM 要求。 在迁移之前验证 [VMware VM/物理服务器](migrate-support-matrix-vmware.md#azure-vm-requirements)和 [Hyper-V VM](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) 的要求。


## <a name="prepare-to-connect-after-migration"></a>准备在迁移后进行连接

Azure VM 是在迁移到 Azure 的过程中创建的。 迁移后，需要能够连接到新的 Azure VM。 需要完成几个步骤才能成功连接。

### <a name="prepare-to-connect-to-windows-azure-vms"></a>准备连接到 Microsoft Azure VM

在本地 Windows 计算机上执行以下操作：

1. 配置 Windows 设置。 这包括删除任何静态永久性路由或 WinHTTP 代理。
2. 确保[这些服务](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)正在运行。
3. 启用远程桌面 (RDP)，以便能够远程连接到本地计算机。 [了解如何](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)使用 PowerShell 启用 RDP。
4. 若要在迁移后通过 Internet 访问 Azure VM，请在本地计算机上的 Windows 防火墙中，在“公共”配置文件中允许 TCP 和 UDP，并将 RDP 设置为所有配置文件允许的应用。
5. 若要在迁移后通过站点到站点 VPN 访问 Azure VM，请在本地计算机上的 Windows 防火墙中，允许对“域”和“专用”配置文件使用 RDP。 [了解](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)如何允许 RDP 流量。 
6. 确保在迁移时本地 VM 上没有任何挂起的 Windows 更新。 如果有，在迁移后，这些更新可能会在 Azure VM 上开始安装，而在更新完成之前，你将无法登录到 VM。


### <a name="prepare-to-connect-with-linux-azure-vms"></a>准备连接 Linux Azure VM

在本地 Linux 计算机上执行以下操作：

1. 检查安全外壳服务是否设置为在系统引导时自动启动。
2. 确保防火墙规则允许 SSH 连接。

### <a name="configure-azure-vms-after-migration"></a>迁移后配置 Azure VM

迁移后，在创建的 Azure VM 上执行以下操作。

1. 若要通过 Internet 连接到 VM，请将公共 IP 地址分配到该 VM。 不能将本地计算机所用的同一个公共 IP 地址用于 Azure VM。 [了解详细信息](../virtual-network/virtual-network-public-ip-address.md)。
2. 检查 VM 上的网络安全组 (NSG) 规则是否允许与 RDP 或 SSH 端口建立传入连接。
3. 在[启动诊断](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine)中查看 VM。

> [!NOTE]
> Azure Bastion 服务提供对 Azure VM 的 RDP 和 SSH 私密访问。 [详细了解](../bastion/bastion-overview.md)此服务。



## <a name="next-steps"></a>后续步骤

确定要使用哪种方法[将 VMware VM 迁移](server-migrate-overview.md)到 Azure，或者开始迁移 [Hyper-V VM](tutorial-migrate-hyper-v.md) 或[物理服务器或虚拟化/云 VM](tutorial-migrate-physical-virtual-machines.md)。
