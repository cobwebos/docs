---
title: 准备好要通过 Azure Migrate 进行迁移的计算机
description: 了解如何准备好要通过 Azure Migrate 进行迁移的本地计算机。
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: MVC
ms.openlocfilehash: aec2e95b65be2e3c69b2d29111fa1cfdbd66674e
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223612"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>准备好要迁移到 Azure 的本地计算机

本文介绍在使用 [Azure Migrate 服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)工具将本地计算机迁移到 Azure 之前，如何准备好这些计算机。

本文内容：
> [!div class="checklist"]
> * 查看迁移限制。
> * 选择迁移 VMware VM 的方法
> * 检查要迁移的计算机的虚拟机监控程序和操作系统要求。
> * 检查要迁移的计算机的 URL 和端口访问权限。
> * 检查在开始迁移之前可能需要做出的更改。
> * 检查已迁移计算机的 Azure VM 要求
> * 准备好计算机，以便在迁移后可以连接到 Azure VM。



## <a name="verify-migration-limitations"></a>验证迁移限制

下表汇总了 Azure Migrate 的发现、评估和迁移限制。 建议在迁移之前先评估计算机，但并非必须这样做。

**方案** | **项目** | **发现/评估** | **迁移**
--- | --- | --- | ---
**VMware VM** | 最多可在一个 Azure Migrate 项目中发现和评估 35,000 个 VM。 | VMware 的一台 [Azure Migrate 设备](common-questions-appliance.md)最多可发现 10,000 个 VMware VM。 | **无代理迁移**：最多可同时复制 300 个 VM。 为了获得最佳性能，如果 VM 数超过 50 个，建议创建多批 VM。<br/><br/> **基于代理的迁移**：可[横向扩展](./agent-based-migration-architecture.md#performance-and-scaling)[复制设备](migrate-replication-appliance.md)来复制大量 VM。<br/><br/> 在门户中，一次最多可选择 10 台计算机进行复制。 若要复制更多计算机，请分批添加，每次添加 10 台。
**Hyper-V VM** | 最多可在一个 Azure Migrate 项目中发现和评估 35,000 个 VM。 | 一台 Azure Migrate 设备最多可发现 5,000 个 Hyper-V VM | 设备不用于 Hyper-V 迁移。 Hyper-V 复制提供程序在每个 Hyper-V 主机上运行。<br/><br/> 复制容量受性能因素（例如 VM 改动）和复制数据的上传带宽影响。<br/><br/> 在门户中，一次最多可选择 10 台计算机进行复制。 若要复制更多计算机，请分批添加，每次添加 10 台。
**物理计算机** | 最多可在一个 Azure Migrate 项目中发现和评估 35,000 台计算机。 | 物理服务器的一台 Azure Migrate 设备最多可发现 250 个物理服务器。 | 可[横向扩展](./agent-based-migration-architecture.md#performance-and-scaling)[复制设备](migrate-replication-appliance.md)来复制大量服务器。<br/><br/> 在门户中，一次最多可选择 10 台计算机进行复制。 若要复制更多计算机，请分批添加，每次添加 10 台。

## <a name="select-a-vmware-migration-method"></a>选择 VMware 迁移方法

如果要将 VMware VM 迁移到 Azure，请无代理迁移方法和基于代理的迁移方法进行[比较](server-migrate-overview.md#compare-migration-methods)，确定适合你的方法。

## <a name="verify-hypervisor-requirements"></a>验证虚拟机监控程序要求

- 验证 [VMware 无代理](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)或[基于 VMware 代理](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based)的要求。
- 验证 [Hyper-V 主机](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements)要求。


## <a name="verify-operating-system-requirements"></a>验证操作系统要求

验证迁移支持的操作系统：

- 如果要迁移 VMware VM 或 Hyper-V VM，请验证[无代理](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless)和[基于代理](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based)的迁移的 VMware VM 要求，以及 [Hyper-V VM](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) 的要求。
- 验证 Azure 是否支持 [Windows 操作系统](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。
- 验证 Azure 是否支持 [Linux 发行版](../virtual-machines/linux/endorsed-distros.md)。

## <a name="review-url-and-port-access"></a>查看 URL 和端口访问权限

查看在迁移过程中访问哪些 URL 和端口。

**方案** | **详细信息** |  **URLs** | **端口**
--- | --- | --- | ---
**VMware 无代理迁移** | 使用 [Azure Migrate 设备](migrate-appliance-architecture.md)进行迁移。 未在 VMware VM 上安装任何内容。 | 通过设备查看发现、评估和迁移所需的公有云和政府 [URL](migrate-appliance.md#url-access)。 | [查看](migrate-support-matrix-vmware-migration.md#port-requirements-agentless)无代理迁移的端口要求。
**基于 VMware 代理的迁移** | 使用[复制设备](migrate-replication-appliance.md)进行迁移。 VM 上安装了移动服务代理。 | 查看复制设备需要访问的[公有云](migrate-replication-appliance.md#url-access)和 [Azure 政府](migrate-replication-appliance.md#azure-government-url-access) URL。 | [查看](migrate-replication-appliance.md#port-access)在基于代理的迁移过程中使用的端口。
**Hyper-V 迁移** | 使用安装在 Hyper-V 主机上的提供程序进行迁移。 未在 Hyper-V VM 上安装任何内容。 | 查看主机上运行的复制提供程序需要访问的[公有云](migrate-support-matrix-hyper-v-migration.md#url-access-public-cloud)和 [Azure 政府](migrate-support-matrix-hyper-v-migration.md#url-access-azure-government) URL。 | Hyper-V 主机上的复制提供程序使用 HTTPS 端口 443 上的出站连接来发送 VM 复制数据。
**物理计算机** | 使用[复制设备](migrate-replication-appliance.md)进行迁移。 物理计算机上安装了移动服务代理。 | 查看复制设备需要访问的[公有云](migrate-replication-appliance.md#url-access)和 [Azure 政府](migrate-replication-appliance.md#azure-government-url-access) URL。 | [查看](migrate-replication-appliance.md#port-access)在物理迁移过程中使用的端口。

## <a name="verify-required-changes-before-migrating"></a>验证迁移之前需要做出的更改

在将 VM 迁移到 Azure 之前，需要在 VM 上进行一些更改。

- 对于某些操作系统，Azure Migrate 会在复制/迁移过程中自动更改。
- 对于其他操作系统，则需要手动配置设置。
- 在开始迁移之前，请务必手动配置设置。 如果在做出更改之前迁移 VM，VM 可能无法在 Azure 中启动。

查看表以确定需要进行的更改。

### <a name="windows-machines"></a>Windows 计算机

下表汇总了需要进行的更改。

**Action** | **VMware（无代理迁移）** | **VMware （基于代理）/物理计算机** | **Hyper-V 上的 Windows** 
--- | --- | --- | ---
**将 SAN 策略配置为“全部联机”**<br/><br/> 此操作确保 Azure VM 中的 Windows 卷使用的驱动器号分配与本地 VM 相同。 | 运行 Windows Server 2008 R2 或更高版本的虚拟机会自动设置。<br/><br/> 早期版本的操作系统需要手动配置。 | 大多数情况下会自动设置。 | 手动配置。
**安装 Hyper-V 来宾集成** | 运行 Windows Server 2003 的计算机需要[手动安装](prepare-windows-server-2003-migration.md#install-on-vmware-vms)。 | 运行 Windows Server 2003 的计算机需要[手动安装](prepare-windows-server-2003-migration.md#install-on-vmware-vms)。 | 运行 Windows Server 2003 的计算机需要[手动安装](prepare-windows-server-2003-migration.md#install-on-hyper-v-vms)。
**启用 Azure 串行控制台**。<br/><br/>在 Azure VM 上[启用控制台](../virtual-machines/troubleshooting/serial-console-windows.md)，来帮助进行故障排除。 无需重新启动 VM。 Azure VM 将使用磁盘映像启动。 启动磁盘映像相当于重新启动新 VM。 | 需要手动启用 | 需要手动启用 | 需要手动启用
**在迁移后进行连接**<br/><br/> 若要在迁移后进行连接，请在迁移之前执行一些步骤。 | 需要[手动设置](#prepare-to-connect-to-azure-windows-vms)。 | 需要[手动设置](#prepare-to-connect-to-azure-windows-vms)。 | 需要[手动设置](#prepare-to-connect-to-azure-windows-vms)。


#### <a name="configure-san-policy"></a>配置 SAN 策略

默认情况下，将为 Azure VM 分配驱动器 D 用作临时存储。

- 这种驱动器分配会导致所有其他附加存储驱动器分配递增一个字母。
- 例如，如果本地安装使用分配了驱动器 D 的数据磁盘（用于应用程序安装），将 VM 迁移到 Azure 后，为此驱动器分配的驱动器号将递增为 E。 
- 若要避免这种自动分配，并确保 Azure 将下一个可用驱动器号分配给其临时卷，请将存储区域网络 (SAN) 策略设置为 **OnlineAll**：

手动配置此设置，如下所示：

1. 在本地计算机（而不是主机服务器）上，以提升的权限打开命令提示符。
2. 输入 **diskpart**。
3. 输入 **SAN**。 如果未保留来宾操作系统的驱动器号，则会返回 **Offline All** 或 **Offline Shared**。
4. 在 **DISKPART** 提示符下，输入 **SAN Policy=OnlineAll**。 此设置可确保将磁盘联机，并确保可以读取和写入这两个磁盘。
5. 在测试迁移期间，可以验证驱动器号是否已保留。


### <a name="linux-machines"></a>Linux 计算机

对于以下版本，Azure Migrate 会自动完成这些操作

- Red Hat Enterprise Linux 7.0+、6.5+
- CentOS 7.0+、6.5+
- SUSE Linux Enterprise Server 12 SP1+
- Ubuntu 18.04LTS、16.04LTS、14.04LTS
- Debian 8、7

对于其他版本，请按表中汇总所示准备计算机。  


**Action** | **详细信息** | **Linux 版本**
--- | --- | ---
**安装 Hyper-V Linux Integration Services** | 重新生成 Linux init 映像，使之包含所需的 Hyper-V 驱动程序。 重新生成 init 映像可以确保 VM 在 Azure 中启动。 | 默认情况下，大多数最新版本的 Linux 分发版已包含此服务。<br/><br/> 如果不包含，除以上所示版本之外的所有版本，都需要手动安装。
**启用 Azure 串行控制台日志记录** | 启用控制台日志记录有助于进行故障排除。 无需重新启动 VM。 Azure VM 将使用磁盘映像启动。 启动磁盘映像相当于重新启动新 VM。<br/><br/> 遵照[这些说明](../virtual-machines/troubleshooting/serial-console-linux.md)启用此功能。
**更新设备映射文件** | 更新包含设备名称到卷的关联的设备映射文件，以使用永久设备标识符。 | 除以上所示版本之外的所有版本，都需要手动安装。 （仅适用于基于代理的 VMware 场景）
**更新 fstab 条目** |  更新条目以使用永久卷标识符。    | 除以上所示版本之外的所有版本，都需要手动更新。
**删除 udev 规则** | 删除基于 MAC 地址等属性保留接口名称的所有 udev 规则。 | 除以上所示版本之外的所有版本，都需要手动删除。
**更新网络接口** | 更新网络接口以基于 DHCP.nst 接收 IP 地址 | 除以上所示版本之外的所有版本，都需要手动更新。
**启用 SSH** | 确保启用 SSH，并将 sshd 服务设置为在重新启动时自动启动。<br/><br/> 确保传入的 SSH 连接请求未被 OS 防火墙或脚本化规则阻止。| 除以上所示版本之外的所有版本，都需要手动启用。

详细了解[在 Azure 上运行 Linux VM](../virtual-machines/linux/create-upload-generic.md) 所要执行的步骤，并获取适用于某些热门 Linux 发行版的说明。


## <a name="check-azure-vm-requirements"></a>检查 Azure VM 要求

复制到 Azure 的本地计算机必须在操作系统和体系结构、磁盘、网络设置与 VM 命名方面符合 Azure VM 要求。

在迁移之前，请查看 [VMware](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)、[Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements) 和[物理服务器](migrate-support-matrix-physical-migration.md#azure-vm-requirements)迁移的 Azure VM 要求。



## <a name="prepare-to-connect-after-migration"></a>准备在迁移后进行连接

Azure VM 是在迁移到 Azure 的过程中创建的。 迁移后，必须能够连接到新的 Azure VM。 需要完成多个步骤才能成功连接。

### <a name="prepare-to-connect-to-azure-windows-vms"></a>准备连接到 Azure Windows VM

本地 Windows 计算机上：

1. 配置 Windows 设置。 设置包括删除任何静态永久性路由或 WinHTTP 代理。
2. 确保[所需的服务](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)正在运行。
3. 启用远程桌面 (RDP)，以便能够远程连接到本地计算机。 了解如何[使用 PowerShell 启用 RDP](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)。
4. 若要在迁移后通过 Internet 访问 Azure VM，请在本地计算机上的 Windows 防火墙中，在“公共”配置文件中允许 TCP 和 UDP，并将 RDP 设置为所有配置文件允许的应用。
5. 若要在迁移后通过站点到站点 VPN 访问 Azure VM，请在本地计算机上的 Windows 防火墙中，允许对“域”和“专用”配置文件使用 RDP。 了解如何[允许 RDP 流量](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)。 
6. 确保在迁移时本地 VM 上没有任何挂起的 Windows 更新。 如果有，在迁移后，这些更新可能会在 Azure VM 上开始安装，而在更新完成之前，你将无法登录到 VM。


### <a name="prepare-to-connect-with-linux-azure-vms"></a>准备连接 Linux Azure VM

在本地 Linux 计算机上：

1. 检查安全外壳服务是否设置为在系统引导时自动启动。
2. 确保防火墙规则允许 SSH 连接。

### <a name="configure-azure-vms-after-migration"></a>迁移后配置 Azure VM

迁移后，在创建的 Azure VM 上完成以下步骤：

1. 若要通过 Internet 连接到 VM，请将公共 IP 地址分配到该 VM。 对 Azure VM 使用的公共 IP 地址必须与用于本地计算机的公共 IP 地址不同。 [了解详细信息](../virtual-network/virtual-network-public-ip-address.md)。
2. 检查 VM 上的网络安全组 (NSG) 规则是否允许与 RDP 或 SSH 端口建立传入连接。
3. 在[启动诊断](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine)中查看 VM。


## <a name="next-steps"></a>后续步骤

确定要使用哪种方法[将 VMware VM 迁移](server-migrate-overview.md)到 Azure，或者开始迁移 [Hyper-V VM](tutorial-migrate-hyper-v.md)、[物理服务器、虚拟化 VM 或云 VM](tutorial-migrate-physical-virtual-machines.md)。

## <a name="see-whats-supported"></a>支持的操作

对于 VMware VM，服务器迁移支持[无代理或基于代理的迁移](server-migrate-overview.md)。

- **VMware VM**：验证 VMware VM 的[迁移要求和支持](migrate-support-matrix-vmware-migration.md)。
- **Hyper-V VM**：验证 Hyper-V VM 的[迁移要求和支持](migrate-support-matrix-hyper-v-migration.md)。
- **物理机**：验证本地物理机和其他虚拟化服务器的[迁移要求和支持](migrate-support-matrix-physical-migration.md)。 
