---
title: "Azure 上的 SAP HANA 操作 | Microsoft Docs"
description: "Azure 虚拟机上部署的 SAP HANA 系统的操作指南。"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: juergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2017
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1d6991d40b9bb8543898bbbdc9d7c905dfe11536
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2018
---
# <a name="sap-hana-on-azure-operations-guide"></a>Azure 上的 SAP HANA 操作指南
本文档提供有关操作 Azure 本机虚拟机 (VM) 上部署的 SAP HANA 系统的指导。 本文档并不旨在取代标准 SAP 文档，后者包括以下内容：

- [SAP 管理指南](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP 安装指南](https://service.sap.com/instguides)
- [SAP 说明](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>先决条件
要使用本指南，需要具备以下 Azure 组件的基础知识：

- [Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure 网络和虚拟网络](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure 存储](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

若要了解有关 Azure 上的 SAP NetWeaver 和其他 SAP 组件的详细信息，请参阅 [Azure 文档](https://docs.microsoft.com/azure/)的 [Azure 上的 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) 部分。

## <a name="basic-setup-considerations"></a>基本设置注意事项
以下各节介绍了在 Azure VM 上部署 SAP HANA 系统的基本设置注意事项。

### <a name="connect-into-azure-virtual-machines"></a>连接到 Azure 虚拟机
如 [Azure 虚拟机规划指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中所述，可通过两种基本方法连接到 Azure VM：

- 通过跳接 VM 或运行 SAP HANA 的 VM 上的 Internet 和公共终结点进行连接。
- 通过 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) 或 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 进行连接。

生产方案需要通过 VPN 或 ExpressRoute 建立的站点到站点连接。 对于送入使用 SAP 软件的生产方案的非生产方案，也需要此类型的连接。 下图显示了跨站点连接的示例：

![跨站点连接](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>选择 Azure VM 类型
[IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中列出了可用于生产方案的 Azure VM 类型。 对于非生产方案，可使用更广泛的本机 Azure VM 类型。

>[!NOTE]
>对于非生产方案，请使用 [SAP 说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 中列出的 VM 类型。

通过使用以下方法在 Azure 中部署 VM：

- Azure 门户。
- Azure PowerShell cmdlet。
- Azure CLI。

还可通过 [SAP 云平台](https://cal.sap.com/)在 Azure VM 服务上部署整个已安装的 SAP HANA 平台。 [在 Azure 上部署 SAP S/4HANA 或 BW/4HANA ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)中介绍了安装过程。

### <a name="choose-azure-storage-type"></a>选择 Azure 存储类型
对于正在运行 SAP HANA 的 Azure VM，Azure 提供两种适用的存储类型：

- [Azure 标准存储](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure 高级存储](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure 针对 Azure 标准和高级存储上的 VHD 提供两种部署方法。 如果总体方案允许，请利用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)部署。

有关存储类型及其 SLA 的列表，请查看[有关托管磁盘的 Azure 文档](https://azure.microsoft.com/pricing/details/managed-disks/)。

建议对 /hana/data 和 /hana/log 卷使用 Azure 高级磁盘。 可基于多个高级存储磁盘构建 LVM RAID，并使用这些 RAID 卷作为 /hana/data 和 /hana/log 卷。

下表显示了客户通常用于在 Azure VM 上托管 SAP HANA 的 VM 类型的配置：

| VM SKU | RAM | 每个  VM I/O<br /> Throughput | /hana/data and /hana/log<br /> 使用 LVM 或 MDADM 进行条带化 | /hana/shared | /root 卷 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128 GiB | 384 MB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GiB | 1200 GB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 GB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1000 GiB | 1000 GB | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 GiB | 1000 GB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 GiB | 2000 GB |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 GiB | 2000 GB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


> [!NOTE]
> 根据 [SAP TDI 存储白皮书](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，对于采用 3 x P20 配置的较小 VM 类型，建议的磁盘空间大于建议的卷空间。 但是，表中显示的选项旨在为 SAP HANA 提供足够的磁盘吞吐量。 如果需要较少的 I/O 吞吐量，可调整 /hana/data 和 /hana/log 的高级存储磁盘选项。 这一点同样适用于 /hana/backup 卷的大小，该卷的大小适合保留相当于两倍内存卷的备份。 如果所需空间较少，可进行调整。 在确定 VM 大小或决定 VM 时，还要考虑总体 VM I/O 吞吐量。 [内存优化虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)一文中记录了总体 VM 吞吐量  

> [!NOTE]
> 如果想从 [Azure 虚拟机单一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) 中受益，则需将所有列为标准存储 (Sxx) 的 VHD 更改为高级存储 (Pxx)。 


### <a name="set-up-azure-virtual-networks"></a>设置 Azure 虚拟网络
当通过 VPN 或 ExpressRoute 与 Azure 建立站点到站点连接时，必须至少有一个 [Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)已通过虚拟网关连接到 VPN 或 ExpressRoute 线路。 虚拟网关驻留在 Azure 虚拟网络中的子网内。 要安装 SAP HANA，需要在虚拟网络中另外创建两个子网。 一个子网托管 VM 以运行 SAP HANA 实例。 另一子网运行 Jumpbox 或管理 VM，以托管 SAP HANA Studio 或其他管理软件。

安装 VM 以运行 SAP HANA 时，VM 需要：

- 已安装两个虚拟 NIC：其中一个 NIC 连接到管理子网，另一个从本地或其他网络连接到 Azure VM 中的 SAP HANA 实例。
- 为两个虚拟 NIC 部署静态专用 IP 地址。

有关分配 IP 地址的不同方法的概述，请参阅 [Azure 中的 IP 地址类型和分配方法](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)。 

[Azure 网络安全组 (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) 用于定向路由到 SAP HANA 实例或 Jumpbox 的流量。 NSG 与 SAP HANA 子网和管理子网关联。

下图概述了 SAP HANA 的大致部署架构：

![SAP HANA 的大致部署架构](media/hana-vm-operations/hana-simple-networking.PNG)


要在不建立站点到站点连接的情况下在 Azure 中部署 SAP HANA，请通过公共 IP 地址访问 SAP HANA 实例。 必须将 IP 地址分配给运行 Jumpbox VM 的 Azure VM。 在此基本方案中，部署依赖于 Azure 内置的 DNS 服务来解析主机名。 对于使用面向公众的 IP 地址的更复杂部署，Azure 内置的 DNS 服务尤为重要。 使用 Azure NSG 来限制可通过具有面向公众的 IP 地址的资产连接到 Azure 子网的开放端口或 IP 地址范围。 下图显示了在不建立站点到站点连接的情况下部署 SAP HANA 的大致架构：
  
![未建立站点到站点连接的大致 SAP HANA 部署架构](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>在 Azure VM 上部署 SAP HANA 的操作
以下各节介绍了在 Azure VM 上部署 SAP HANA 系统的一些相关操作。

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Azure VM 上的备份和还原操作
以下文档介绍如何备份和还原 SAP HANA 部署：

- [SAP HANA 备份概述](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA 文件级备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA 存储快照基准](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>启动并重启包含 SAP HANA 的 VM
Azure 公有云的一个突出特性是只需为计算时间付费。 例如，当关闭正在运行 SAP HANA 的 VM 时，只需为此期间的存储成本付费。 在初始部署中为 VM 指定静态 IP 地址时，可利用另一特性。 重启具有 SAP HANA 的 VM 时，VM 使用其先前的 IP 地址重启。 


### <a name="use-saprouter-for-sap-remote-support"></a>使用 SAProuter 实现 SAP 远程支持
如果已在本地位置与 Azure 之间建立了站点到站点的连接，并且正在运行 SAP 组件，那么可能已在运行 SAProuter。 在此情况下，请通过完成以下操作实现远程支持：

- 在 SAProuter 配置中保留托管 SAP HANA 的 VM 的专用和静态 IP 地址。
- 配置托管 HANA VM 的子网的 NSG，允许流量通过 TCP/IP 端口 3299。

如果通过 Internet 连接到 Azure，且包含 SAP HANA 的 VM 没有 SAP 路由器，则需安装该组件。 在管理子网中某个独立的 VM 上安装 SAProuter。 下图显示了在不建立站点到站点连接且不包含 SAProuter 的情况下部署 SAP HANA 的大致架构：

![未建立站点到站点连接且不包含 SAProuter 的大致 SAP HANA 部署架构](media/hana-vm-operations/hana-simple-networking3.PNG)

确保在独立的 VM 中安装 SAProuter，而不是在 Jumpbox VM 中安装。 该独立 VM 必须具有静态 IP 地址。 要将 SAProuter 连接到 SAP 托管的 SAProuter，请联系 SAP 获取 IP 地址。 （由 SAP 托管的 SAProuter 与在 VM 上安装的 SAProuter 实例相对应。）使用 SAP 提供的 IP 地址配置 SAProuter 实例。 在配置设置中，唯一必需的端口是 TCP 端口 3299。

有关如何通过 SAPRouter 设置和维护远程支持连接的详细信息，请参阅 [SAP 文档](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)。

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Azure 本机 VM 上的 SAP HANA 的高可用性
如果正在运行 SUSE Linux 12 SP1 或更高版本，可以建立包含 STONITH 设备的 Pacemaker 群集。 可使用这些设备设置一个可将同步复制与 HANA 系统复制和自动故障转移配合使用的 SAP HANA 配置。 有关设置过程的详细信息，请参阅 [Azure 虚拟机上的 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)。
