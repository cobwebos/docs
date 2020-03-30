---
title: 支持 Azure 迁移中的 VMware 迁移
description: 了解在 Azure 迁移中对 VMware VM 迁移的支持。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 9d8dc4dadc975a0fb69ea207f6062b72231460ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269505"
---
# <a name="support-matrix-for-vmware-migration"></a>VMware 迁移的支持矩阵

本文总结了使用[Azure 迁移：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)迁移 VMware VM 的支持设置和限制。 如果要查找有关评估 VMware VM 以迁移到 Azure 的信息，请查看[评估支持矩阵](migrate-support-matrix-vmware.md)。


## <a name="migration-options"></a>迁移选项

您可以通过多种方式迁移 VMware VM：

- 使用无代理迁移：迁移 VM 而无需在 VM 上安装任何内容。 部署[Azure 迁移设备](migrate-appliance.md)以进行无代理迁移。
- 使用基于代理的迁移：在 VM 上安装代理以进行复制。 对于基于代理的迁移，您需要部署[复制设备](migrate-replication-appliance.md)。

查看[本文](server-migrate-overview.md)，了解要使用的方法。

## <a name="migration-limitations"></a>迁移限制

- 一次最多可以选择 10 个 VM 进行复制。 如果要迁移更多计算机，则以 10 的组进行复制。
- 对于 VMware 无代理迁移，最多可以同时运行 100 项复制。

## <a name="agentless-vmware-servers"></a>无代理 VMware 服务器

**Vmware** | **详细信息**
--- | ---
**VMware vCenter 服务器** | 版本 5.5、6.0、6.5 或 6.7。
**VMware vSphere ESXI 主机** | 版本 5.5、6.0、6.5 或 6.7。
**vCenter 服务器权限** | 无代理迁移使用[迁移设备](migrate-appliance.md)。 设备需要以下权限：<br/><br/> - **数据存储.浏览**：允许浏览 VM 日志文件以排除快照创建和删除问题。<br/><br/> - **数据存储.低电平文件操作**：允许在数据存储浏览器中读取/写入/删除/重命名操作，以排除快照创建和删除问题。<br/><br/> - **虚拟机.配置.磁盘更改跟踪**：允许启用或禁用 VM 磁盘的更改跟踪，以便在快照之间提取已更改的数据块。<br/><br/> - **虚拟机.配置.磁盘租赁**：允许 VM 的磁盘租赁操作，使用 VMware vSphere 虚拟磁盘开发工具包 （VDDK） 读取磁盘。<br/><br/> - **虚拟机.预配.允许DiskAccess：（** 特别是 vSphere 6.0 及以上）允许使用 VDDK 在 VM 上打开磁盘以进行随机读取访问。<br/><br/> - **虚拟机.预配.允许ReadOnlyDisk访问**：允许在 VM 上打开磁盘，以便使用 VDDK 读取磁盘。<br/><br/> - **虚拟机.预配.允许Disk随机访问**：允许在 VM 上打开磁盘，以便使用 VDDK 读取磁盘。<br/><br/> - **虚拟机.预配.允许虚拟计算机下载**：允许对与 VM 关联的文件进行读取操作，以便在发生故障时下载日志并进行故障排除。<br/><br/> - [虚拟机.快照管理.]：允许创建和管理用于复制的 VM 快照。<br/><br/> - **虚拟机.交互.关闭电源**：允许在迁移到 Azure 期间关闭 VM 的电源。



## <a name="agentless-vmware-vms"></a>无代理 VMware VM

**支持** | **详细信息**
--- | ---
**支持的操作系统** | 可以使用无代理迁移迁移 Azure 支持的[Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)和[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)操作系统。
**Azure 所需的更改** | 某些 VM 可能需要经过更改才能在 Azure 中运行。 Azure 迁移会自动为以下操作系统进行这些更改：<br/> - 红帽企业 Linux 6.5+，7.0*<br/> - CentOS 6.5°， 7.0°</br> - SUSE Linux 企业服务器 12 SP1*<br/> - 乌本图 14.04LTS， 16.04LTS， 18.04LTS<br/> - Debian 7， 8<br/><br/> 对于其他操作系统，您需要在迁移之前手动进行调整。 相关文章包含有关如何执行此操作的说明。
**Linux 启动** | 如果 /boot 位于专用分区上，则应驻留在操作系统磁盘上，并且不应跨多个磁盘分布。<br/> 如果 /boot 是根 （/） 分区的一部分，则'/' 分区应位于 OS 磁盘上，而不是跨其他磁盘。
**UEFI 启动** | 迁移不支持具有 UEFI 启动的 VM。
**磁盘大小** | 2 TB OS 磁盘;4 TB 的数据磁盘。
**磁盘限制** |  每个 VM 最多 60 个磁盘。
**加密磁盘/卷** | 迁移不支持具有加密磁盘/卷的 VM。
**共享磁盘群集** | 不支持。
**独立磁盘** | 不支持。
**RDM/直通磁盘** | 如果 VM 具有 RDM 或直通磁盘，则这些磁盘不会复制到 Azure。
**NFS** | 在 VM 上装载的卷的 NFS 卷不会复制。
**iSCSI 目标** | 不支持具有 iSCSI 目标的 VM 进行无代理迁移。
**多路径 IO** | 不支持。
**存储 vMotion** | 不支持。 如果 VM 使用存储 vMotion，则复制不起作用。
**已组 NIC** | 不支持。
**IPv6** | 不支持。
**目标磁盘** | VM 只能迁移到 Azure 中的托管磁盘（标准硬盘、高级 SSD）。
**同时复制** | 每个 vCenter 服务器 100 个 VM。 如果更多，则分批迁移 100。


## <a name="agentless-azure-migrate-appliance"></a>无代理-Azure 迁移设备 
无代理迁移使用部署在 VMware VM 上的 Azure 迁移设备。

- 了解 VMware[的设备要求](migrate-appliance.md#appliance---vmware)。
- 了解设备需要访问的[URL。](migrate-appliance.md#url-access)

## <a name="agentless-ports"></a>无代理端口

**设备** | **连接**
--- | ---
家用电器 | 端口 443 上的出站连接，用于将复制的数据上载到 Azure，并与 Azure 迁移服务进行协调复制和迁移。
vCenter 服务器 | 端口 443 上的入站连接，允许设备协调复制 - 创建快照、复制数据、发布快照
vSphere/EXSI 主机 | 在 TCP 端口 902 上入站，用于设备从快照复制数据。


## <a name="agent-based-vmware-servers"></a>基于代理的 VMware 服务器
下表总结了 VMware 虚拟化服务器的评估支持和限制。

**VMware 要求** | **详细信息**
--- | ---
**VMware vCenter 服务器** | 版本 5.5、6.0、6.5 或 6.7。
**VMware vSphere ESXI 主机** | 版本 5.5、6.0、6.5 或 6.7。
**vCenter 服务器权限** | vCenter 服务器的只读帐户。

## <a name="agent-based-vmware-vms"></a>基于代理的 VMware VM

该表总结了 VMware VM 对要使用基于代理的迁移迁移的 VMware VM 的支持。

**支持** | **详细信息**
--- | ---
**计算机工作负载** | Azure 迁移支持在受支持的计算机上运行的任何工作负荷（如活动目录、SQL 服务器等）的迁移。
**操作系统** | 有关最新信息，请查看操作系统对站点恢复[的支持](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)。 Azure 迁移提供相同的 VM 操作系统支持。
**Linux 文件系统/来宾存储** | 有关最新信息，请查看[Linux 文件系统](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)对站点恢复的支持。 Azure 迁移具有相同的 Linux 文件系统支持。
**网络/存储** | 有关最新[信息，请查看](../site-recovery/vmware-physical-azure-support-matrix.md#network)站点恢复的网络和[存储](../site-recovery/vmware-physical-azure-support-matrix.md#storage)先决条件。 Azure 迁移提供相同的网络/存储要求。
**Azure 要求** | 有关最新信息，请查看站点恢复的[Azure 网络](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、[存储](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)和[计算](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute)要求。 Azure 迁移对 VMware 迁移具有相同的要求。
**移动服务** | 移动服务代理必须安装在要迁移的每个 VM 上。
**UEFI 启动** | Azure 中迁移的 VM 将自动转换为 BIOS 引导 VM。<br/><br/> 操作系统磁盘应最多具有四个分区，并且卷应使用 NTFS 进行格式化。
**目标磁盘** | VM 只能迁移到 Azure 中的托管磁盘（标准硬盘、高级 SSD）。
**磁盘大小** | 2 TB OS 磁盘;8 TB 的数据磁盘。
**磁盘限制** |  每个 VM 最多 63 个磁盘。
**加密磁盘/卷** | 迁移不支持具有加密磁盘/卷的 VM。
**共享磁盘群集** | 不支持。
**独立磁盘** | 支持。
**直通磁盘** | 支持。
**NFS** | 在 VM 上装载的卷的 NFS 卷不会复制。
**iSCSI 目标** | 不支持具有 iSCSI 目标的 VM 进行无代理迁移。
**多路径 IO** | 不支持。
**存储 vMotion** | 支持
**已组 NIC** | 不支持。
**IPv6** | 不支持。




## <a name="agent-based-replication-appliance"></a>基于代理的复制设备 

使用 Azure 迁移中心提供的 OVA 模板设置复制设备时，该设备将运行 Windows Server 2016 并符合支持要求。 如果在物理服务器上手动设置复制设备，请确保它符合要求。

- 了解 VMware 的[复制设备要求](migrate-replication-appliance.md#appliance-requirements)。
- 必须在设备上安装 MySQL。 了解[安装选项](migrate-replication-appliance.md#mysql-installation)。
- 了解复制设备需要访问的[URL](migrate-replication-appliance.md#url-access)和[端口](migrate-replication-appliance.md#port-access)。

## <a name="agent-based-ports"></a>基于代理的端口

**设备** | **连接**
--- | ---
VM | 在 VM 上运行的移动服务与端口 HTTPS 443 入站上的本地复制设备（配置服务器）进行通信，以便进行复制管理。<br/><br/> VM 将复制数据发送到 HTTPS 9443 入站端口上的进程服务器（在配置服务器计算机上运行）。 可以修改此端口。
复制设备 | 复制设备通过端口 HTTPS 443 出站协调使用 Azure 进行复制。
进程服务器 | 进程服务器接收复制数据、优化和加密数据，然后通过端口 443 出站将其发送到 Azure 存储。<br/> 默认情况下，进程服务器在复制设备上运行。

## <a name="azure-vm-requirements"></a>Azure VM 要求

复制到 Azure 的所有本地 VM 必须满足此表中总结的 Azure VM 要求。 Site Recovery 运行复制先决条件检查时，如果不符合某些要求，检查将会失败。

**组件** | **要求** | **详细信息**
--- | --- | ---
来宾操作系统 | 验证支持的 VMware VM 操作系统进行迁移。<br/> 您可以迁移在受支持的操作系统上运行的任何工作负载。 | 如果不支持，检查会失败。
来宾操作系统体系结构 | 64 位。 | 如果不支持，检查会失败。
操作系统磁盘大小 | 最大 2,048 GB。 | 如果不支持，检查会失败。
操作系统磁盘计数 | 1 | 如果不支持，检查会失败。
数据磁盘计数 | 64 或更少。 | 如果不支持，检查会失败。
数据磁盘大小 | 最大 4,095 GB | 如果不支持，检查会失败。
网络适配器 | 支持多个适配器。 |
共享 VHD | 不支持。 | 如果不支持，检查会失败。
FC 磁盘 | 不支持。 | 如果不支持，检查会失败。
BitLocker | 不支持。 | 为计算机启用复制之前，必须先禁用 BitLocker。
VM 名称 | 1 到 63 个字符。<br/> 限制为字母、数字和连字符。<br/><br/> 计算机名称必须以字母或数字开头和结尾。 |  请在 Site Recovery 中的计算机属性中更新该值。
迁移后连接 - Windows | 要在迁移后连接到运行 Windows 的 Azure VM，请执行：<br/> - 在迁移在本地 VM 上启用 RDP 之前。 确保为**公共**配置文件添加了 TCP 和 UDP 规则，并且所有配置文件都允许在 Windows**防火墙** > **允许应用**中使用 RDP。<br/> 对于站点到站点 VPN 访问，请在**Windows 防火墙** -> **允许的应用和功能**中启用 RDP。 **Domain and Private** 此外，请检查操作系统的 SAN 策略是否设置为**OnlineAll**。 [了解详情](prepare-for-migration.md)。 |
迁移后连接 -Linux | 要使用 SSH 在迁移后连接到 Azure VM，<br/> 在迁移之前，在本地计算机上，检查安全外壳服务是否设置为"开始"，并且防火墙规则是否允许 SSH 连接。<br/> 故障转移后，在 Azure VM 上，允许传入连接到 SSH 端口，用于通过 VM 故障的网络安全组规则以及连接到它的 Azure 子网。 此外，为 VM 添加公共 IP 地址。 |  


## <a name="next-steps"></a>后续步骤

[选择](server-migrate-overview.md)VMware 迁移选项。
