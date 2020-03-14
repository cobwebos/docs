---
title: Azure Migrate 中的物理服务器迁移支持
description: 了解 Azure Migrate 中对物理服务器迁移的支持。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: e55cf6dddbc8dafd33b444e4a0dbe378d807aea1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269544"
---
# <a name="support-matrix-for-physical-server-migration"></a>物理服务器迁移的支持矩阵

本文总结了在[Azure Migrate：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)的情况下迁移物理服务器的支持设置和限制。 如果你正在寻找有关评估要迁移到 Azure 的物理服务器的信息，请查看[评估支持矩阵](migrate-support-matrix-physical.md)。


## <a name="overview"></a>概述

可以使用基于代理的复制将本地计算机作为物理服务器迁移。 使用此工具可将各种计算机迁移到 Azure：

- 本地物理服务器。
- 虚拟机通过诸如 Xen、KVM 等平台虚拟化。
- Hyper-v Vm 或 VMware Vm 如果出于某种原因，你不想使用标准[hyper-v](tutorial-migrate-hyper-v.md)或[vmware](server-migrate-overview.md)流。
- 在私有云中运行的虚拟机。
- 在公有云（例如 Amazon Web Services （AWS）或 Google Cloud Platform （GCP））中运行的 Vm。


## <a name="migration-limitations"></a>迁移限制

对于复制，最多可以选择10台计算机。 如果要迁移更多计算机，请在10个组中进行复制。


## <a name="physical-server-requirements"></a>物理服务器要求

该表汇总了对你要使用基于代理的迁移来迁移的物理服务器的支持。

**支持** | **详细信息**
--- | ---
**计算机工作负荷** | Azure Migrate 支持迁移受支持计算机上运行的任何工作负荷（假设 Active Directory、SQL server 等）。
**操作系统** | 有关最新信息，请查看 Site Recovery 的[操作系统支持](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)。 Azure Migrate 提供完全相同的操作系统支持。
**Linux 文件系统/来宾存储** | 有关最新信息，请查看[Linux 文件系统](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)对 Site Recovery 的支持。 Azure Migrate 提供完全相同的 Linux 文件系统支持。
**网络/存储** | 有关最新信息，请查看 Site Recovery 的[网络](../site-recovery/vmware-physical-azure-support-matrix.md#network)和[存储](../site-recovery/vmware-physical-azure-support-matrix.md#storage)必备组件。 Azure Migrate 提供完全相同的网络/存储要求。
**Azure 要求** | 有关最新信息，请查看 Site Recovery 的[Azure 网络](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、[存储](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)和[计算](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute)要求。 对于物理服务器迁移，Azure Migrate 具有相同的要求。
**移动服务** | 必须在要迁移的每台计算机上安装移动服务代理。
**UEFI 启动** | Azure 中迁移的计算机将自动转换为 BIOS 启动 Azure VM。<br/><br/> OS 磁盘最多应有四个分区，卷应使用 NTFS 进行格式化。
**目标磁盘** | 计算机只能迁移到 Azure 中的托管磁盘（标准 HDD、高级 SSD）。
**磁盘大小** | 2 TB 操作系统磁盘;8 TB （适用于数据磁盘）。
**磁盘限制** |  每台计算机最多63磁盘。
**加密磁盘/卷** |  不支持对具有加密磁盘/卷的计算机进行迁移。
**共享磁盘群集** | 不支持。
**独立磁盘** | 支持。
**传递磁盘** | 支持。
**NFS** | 不会复制在计算机上作为卷装载的 NFS 卷。
**iSCSI 目标** | 无代理迁移不支持具有 iSCSI 目标的计算机。
**多路径 IO** | 不支持。
**存储 vMotion** | 支持
**成组 Nic** | 不支持。
**IPv6** | 不支持。



## <a name="replication-appliance-requirements"></a>复制设备要求

如果在物理服务器上手动设置复制设备，请确保它符合表中汇总的要求。 使用 Azure Migrate 中心提供的 .OVA 模板将 Azure Migrate 复制设备设置为 VMware VM 时，会使用 Windows Server 2016 设置设备，并符合支持要求。 

- 了解[复制设备要求](migrate-replication-appliance.md#appliance-requirements)。
- 必须在设备上安装 MySQL。 了解[安装选项](migrate-replication-appliance.md#mysql-installation)。
- 了解复制设备需要访问的[url](migrate-replication-appliance.md#url-access) 。

## <a name="azure-vm-requirements"></a>Azure VM 要求

复制到 Azure 的所有本地 Vm 必须满足此表中汇总的 Azure VM 要求。 当 Site Recovery 为复制运行必备项检查时，如果无法满足某些要求，则检查将失败。

组件 | **要求** | **详细信息**
--- | --- | ---
来宾操作系统 | 验证支持的操作系统。<br/> 你可以迁移在受支持的操作系统上运行的任何工作负荷。 | 如果不支持，检查会失败。
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
迁移后连接-Windows | 若要在迁移后连接到运行 Windows 的 Azure Vm：<br/> -迁移之前，在本地 VM 上启用 RDP。 请确保为“公共”配置文件添加了 TCP 和 UDP 规则，并确保在“Windows 防火墙” **“允许的应用”中针对所有配置文件允许 RDP** > 。<br/> 对于站点到站点 VPN 访问，在**Windows 防火墙**中启用 rdp 和允许 Rdp -> 允许用于**域和专用**网络的**应用和功能**。 此外，请检查操作系统的 SAN 策略是否设置为**OnlineAll**。 [了解详细信息](prepare-for-migration.md)。 |
迁移后连接-Linux | 使用 SSH 迁移后连接到 Azure Vm：<br/> 在迁移之前，请在本地计算机上检查安全外壳服务是否设置为 "启动"，以及防火墙规则是否允许 SSH 连接。<br/> 故障转移后，在 Azure VM 上，允许已故障转移的 VM 上的网络安全组和连接到的 Azure 子网的 SSH 端口建立传入连接。 此外，为 VM 添加公共 IP 地址。 |  


## <a name="next-steps"></a>后续步骤

[迁移](tutorial-migrate-physical-virtual-machines.md)物理服务器。
