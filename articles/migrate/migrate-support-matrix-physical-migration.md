---
title: 支持 Azure 迁移中的物理服务器迁移
description: 了解对 Azure 迁移中物理服务器迁移的支持。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: e55cf6dddbc8dafd33b444e4a0dbe378d807aea1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269544"
---
# <a name="support-matrix-for-physical-server-migration"></a>支持物理服务器迁移矩阵

本文总结了使用[Azure 迁移：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)迁移物理服务器的支持设置和限制。 如果要查找有关评估物理服务器以迁移到 Azure 的信息，请查看[评估支持矩阵](migrate-support-matrix-physical.md)。


## <a name="overview"></a>概述

您可以使用基于代理的复制将本地计算机迁移到物理服务器。 使用此工具可将各种计算机迁移到 Azure：

- 本地物理服务器。
- 由 Xen、KVM 等平台虚拟化的 VM。
- 如果由于某种原因不想使用标准的[Hyper-V](tutorial-migrate-hyper-v.md)或[VMware](server-migrate-overview.md)流，则 Hyper-VM 或 VMware VM。
- 在私有云中运行的 VM。
- 在公共云中运行的 VM，如亚马逊 Web 服务 （AWS） 或 Google 云平台 （GCP）。


## <a name="migration-limitations"></a>迁移限制

一次最多可以选择 10 台计算机进行复制。 如果要迁移更多计算机，则以 10 的组进行复制。


## <a name="physical-server-requirements"></a>物理服务器要求

该表总结了对要使用基于代理的迁移迁移的物理服务器的支持。

**支持** | **详细信息**
--- | ---
**计算机工作负载** | Azure 迁移支持在受支持的计算机上运行的任何工作负荷（如活动目录、SQL 服务器等）的迁移。
**操作系统** | 有关最新信息，请查看操作系统对站点恢复[的支持](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)。 Azure 迁移提供相同的操作系统支持。
**Linux 文件系统/来宾存储** | 有关最新信息，请查看[Linux 文件系统](../site-recovery/vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)对站点恢复的支持。 Azure 迁移提供相同的 Linux 文件系统支持。
**网络/存储** | 有关最新[信息，请查看](../site-recovery/vmware-physical-azure-support-matrix.md#network)站点恢复的网络和[存储](../site-recovery/vmware-physical-azure-support-matrix.md#storage)先决条件。 Azure 迁移提供相同的网络/存储要求。
**Azure 要求** | 有关最新信息，请查看站点恢复的[Azure 网络](../site-recovery/vmware-physical-azure-support-matrix.md#azure-vm-network-after-failover)、[存储](../site-recovery/vmware-physical-azure-support-matrix.md#azure-storage)和[计算](../site-recovery/vmware-physical-azure-support-matrix.md#azure-compute)要求。 Azure 迁移对物理服务器迁移具有相同的要求。
**移动服务** | 移动服务代理必须安装在要迁移的每台计算机上。
**UEFI 启动** | Azure 中的迁移计算机将自动转换为 BIOS 引导 Azure VM。<br/><br/> 操作系统磁盘应最多具有四个分区，并且卷应使用 NTFS 进行格式化。
**目标磁盘** | 计算机只能迁移到 Azure 中的托管磁盘（标准硬盘、高级 SSD）。
**磁盘大小** | 2 TB OS 磁盘;8 TB 的数据磁盘。
**磁盘限制** |  每台计算机最多 63 个磁盘。
**加密磁盘/卷** |  不支持具有加密磁盘/卷的计算机进行迁移。
**共享磁盘群集** | 不支持。
**独立磁盘** | 支持。
**直通磁盘** | 支持。
**NFS** | 在计算机上装载的卷的 NFS 卷不会复制。
**iSCSI 目标** | 不支持具有 iSCSI 目标的计算机进行无代理迁移。
**多路径 IO** | 不支持。
**存储 vMotion** | 支持
**已组 NIC** | 不支持。
**IPv6** | 不支持。



## <a name="replication-appliance-requirements"></a>复制设备要求

如果在物理服务器上手动设置复制设备，请确保它符合表中概述的要求。 使用 Azure 迁移中心提供的 OVA 模板将 Azure 迁移复制设备设置为 VMware VM 时，该设备将使用 Windows Server 2016 进行设置，并符合支持要求。 

- 了解[复制设备要求](migrate-replication-appliance.md#appliance-requirements)。
- 必须在设备上安装 MySQL。 了解[安装选项](migrate-replication-appliance.md#mysql-installation)。
- 了解复制设备需要访问的[URL。](migrate-replication-appliance.md#url-access)

## <a name="azure-vm-requirements"></a>Azure VM 要求

复制到 Azure 的所有本地 VM 必须满足此表中总结的 Azure VM 要求。 Site Recovery 运行复制先决条件检查时，如果不符合某些要求，检查将会失败。

**组件** | **要求** | **详细信息**
--- | --- | ---
来宾操作系统 | 验证支持的操作系统。<br/> 您可以迁移在受支持的操作系统上运行的任何工作负载。 | 如果不支持，检查会失败。
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

[迁移](tutorial-migrate-physical-virtual-machines.md)物理服务器。
