---
title: "Azure 上的 SAP HANA 操作 | Microsoft Docs"
description: "Azure 本机 VM 上的 SAP HANA 操作"
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
ms.openlocfilehash: 0328bdc40429e1e82a76f290f5bde39089db0a9d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="sap-hana-on-azure-operations-guide"></a>Azure 上的 SAP HANA 操作指南
本指南提供有关操作 Azure 虚拟机上部署的 SAP HANA 系统的指导。 本文档并不旨在取代任何标准 SAP 文档。 可在以下位置找到 SAP 指南和说明：

- [SAP 管理指南](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP 安装指南](https://service.sap.com/instguides)
- [SAP 说明](https://sservice.sap.com/notes)

先决条件是读者对以下各种 Azure 组件有基本的了解：

- [Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure 网络和 VNet](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure 存储](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

在 [Azure 文档](https://docs.microsoft.com/azure/)的 [Azure 上的 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) 部分可以找到有关 Azure 中 SAP NetWeaver 和其他 SAP 组件的附加文档。

## <a name="basic-setup-considerations"></a>基本设置注意事项
### <a name="connecting-into-azure"></a>连接到 Azure
如 [SAP NetWeaver 的 Azure 虚拟机规划和实施指南][planning-guide] 中所述，可使用两种基本方法连接到 Azure 虚拟机。 

- 通过跳接 VM 或运行 SAP HANA 的 VM 上的 Internet 和公共终结点进行连接
- 通过 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) 或 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 进行连接

对于生产方案，或者结合 SAP 软件向生产方案馈送数据的非生产方案，需按下图所示，通过 VPN 或 ExpressRoute 建立站点到站点连接：

![跨站点连接](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choice-of-azure-vm-types"></a>Azure VM 类型选项
可在[此处](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)查找可用于生产方案的 Azure VM 类型。 对于非生产方案，可以选择更广泛的本机 Azure VM。 但是，应该将选择范围限定为 [SAP 说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 中所述的 VM 类型。 可通过以下方式在 Azure 中部署这些 VM：

- Azure 门户
- Azure Powershell Cmdlet
- Azure CLI

也可以根据[此文](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)所述，通过 [SAP 云平台](https://cal.sap.com/)将整个已安装的 SAP HANA 平台部署到 Azure 虚拟机服务。

### <a name="choice-of-azure-storage"></a>Azure 存储选项
对于运行 SAP HANA 的 Azure VM，Azure 提供两种适用的主要存储类型

- [Azure 标准存储](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure 高级存储](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure 针对 Azure 标准和高级存储上的 VHD 提供两种部署方法。 如果总体方案允许的话，我们建议利用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)部署。

有关具体的存储类型及其相关的 SLA，请查看[此文档](https://azure.microsoft.com/pricing/details/managed-disks/)

建议对 /hana/data 和 /hana/log 卷使用 Azure 高级磁盘。 支持基于多个高级存储磁盘构建 LVM RAID，并使用这些 RAID 卷作为 /hana/data 和 /hana/log 卷。

到目前为止，客户在 Azure VM 上托管 SAP HANA 所用的常见 VM 类型的可能配置如下所示：

| VM SKU | RAM | /hana/data and /hana/log<br /> 使用 LVM 或 MDADM 进行条带化 | /hana/shared | /root 卷 | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S10 |
| E32v3 | 256GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1TB | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1.7TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2 TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128ms | 3.8TB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 5 x S30 |


### <a name="azure-networking"></a>Azure 网络
假设已与 Azure 建立 VPN 或 ExpressRoute 站点到站点连接，则至少有一个 [Azure VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 已通过虚拟网关连接到 VPN 或 ExpressRoute 线路。 虚拟网关驻留在 Azure VNet 中的子网内。 若要安装 HANA，请在 VNet 中再创建两个子网。 其中一个子网托管运行 SAP HANA 实例的 VM，另一个子网运行可托管 SAP HANA Studio 或其他管理软件的最终 Jumpbox 或管理 VM。
在安装运行 HANA 的 VM 时，这些 VM 应包含：

- 已安装两个虚拟 NIC，其中一个 NIC 连接到管理子网，另一个从本地或其他网络连接到 Azure VM 中的 SAP HANA 实例。
- 为两个 vNIC 部署静态专用 IP 地址

[此文](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)提供了各种可能的 IP 地址分配方法的概述。 

直接路由到 SAP HANA 实例或路由到 Jumpbox 的流量由与 HANA 子网和管理子网关联的 [Azure 网络安全组](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg)定向。

总体上，大致的部署架构如下所示：

![SAP HANA 的大致部署架构](media/hana-vm-operations/hana-simple-networking.PNG)


如果只是在 Azure 中部署 SAP HANA 而未建立站点到站点连接（与 Azure 建立 VPN 或 ExpressRoute 连接），可通过分配给运行 Jumpbox VM 的 Azure VM 的公共 IP 地址访问 SAP HANA 实例。 如果情况比较简单，则还可依赖于 Azure 内置的 DNS 服务来解析主机名。 尤其是在使用面向公众的 IP 地址时，可以使用 Azure 网络安全组来限制有权通过面向公众的 IP 地址连接到 Azure 子网的开放端口或 IP 地址范围。 此类部署的架构如下所示：
  
![未建立站点到站点连接的大致 SAP HANA 部署架构](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations"></a>操作
### <a name="backup-and-restore-operations-on-azure-vms"></a>Azure VM 上的备份和还原操作
以下文档阐述了可能的 SAP HANA 备份和还原方法：

- [SAP HANA 备份概述](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA 文件级备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA 存储快照基准](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)



### <a name="start-and-restart-of-vms-containing-sap-hana"></a>启动和重启包含 SAP HANA 的 VM
Azure 公有云的优势之一是只需按分钟支付计算资源费用。 这意味着，如果关闭某个运行 SAP HANA 的 VM，只需支付运行期间的存储费用。 再次启动包含 SAP HANA 的 VM 时，该 VM 将再次启动，并使用相同的 IP 地址（如果 VM 是使用静态 IP 地址部署的）。 


### <a name="saprouter-enabling-sap-remote-support"></a>启用 SAP 远程支持的 SAPRouter
如果在本地位置与 Azure 之间建立了站点到站点连接且已运行 SAP 组件，则很有可能已运行了 SAProuter。 在这种情况下，对于 Azure 中部署的 SAP HANA 实例无需执行任何操作。 不过，需要在 SAPRouter 配置中保留托管 HANA 的 VM 的专用和静态 IP 地址，并改写托管 HANA VM 的子网的 NSG（允许流量通过 TCP/IP 端口 3299）。

如果正在部署 SAP HANA 并通过 Internet 连接到 Azure，且未在 VNet（其中运行包含 SAP HANA 的 VM）中安装 SAP 路由器，则应在管理子网中某个独立的 VM 上安装 SAPRouter：


![未建立站点到站点连接且不包含 SAPRouter 的大致 SAP HANA 部署架构](media/hana-vm-operations/hana-simple-networking3.PNG)

应在独立的 VM 中安装 SAPRouter，而不能在 Jumpbox VM 中安装。 该独立 VM 需要静态 IP 地址。 若要将该 SAPRouter 连接到 SAP 托管的 SAPRouter（装有 VM 的 SAPRouter 实例的对等方），需要联系 SAP 获取 IP 地址，因为配置 SAPRouter 实例时需要用到它。 所需的唯一端口是 TCP 端口 3299。
有关如何通过 SAPRouter 设置和维护远程支持连接的详细信息，请查看此 [SAP 资源](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)。

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Azure 本机 VM 上的 SAP HANA 的高可用性
运行 SUSE Linux 12 SP1 和更高版本，可以建立包含 STONITH 设备的 Pacemaker 群集，以设置一个可将同步复制与 HANA 系统复制和自动故障转移配合使用的 SAP HANA 配置。 [Azure 虚拟机上的 SAP HANA 的高可用性](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-high-availability)一文介绍了设置过程。

 










