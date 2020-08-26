---
title: Azure VM 上的 SAP 入门 | Microsoft Docs
description: 了解在 Microsoft Azure 中的虚拟机 (VM) 上运行的 SAP 解决方案
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 704d4c1b4e4e4229f35526748494aaac2e351f99
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871371"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>使用 Azure 托管和运行 SAP 工作负荷方案

使用 Microsoft Azure 时，能够在可缩放、符合要求且经企业认证的平台上可靠地运行任务关键型 SAP 工作负荷和方案。 可利用 Azure 的可伸缩性、灵活性和低成本特性。 由于 Microsoft 和 SAP 之间扩大了合作关系，因此，可以在 Azure 的各个开发和测试以及生产方案中运行 SAP 应用程序，并且获得完全支持。 从 SAP NetWeaver 到 SAP S/4HANA，Linux 上的 SAP BI 到 Windows 上的 SAP BI，SAP HANA 到 SQL，我们都能满足客户的需求。

除了在 Azure 上托管具有不同 DBMS 的 SAP NetWeaver 方案，还可以在 Azure 上托管其他 SAP 工作负荷方案，例如 SAP BI。 

Azure for SAP HANA 的独特之处在于，它是一款能让 Azure 脱颖而出的产品。 为了支持托管涉及 SAP HANA 的更多内存和 CPU 资源消耗较高的 SAP 方案，Azure 提供了客户专用的裸机硬件。 使用此解决方案，可以运行需要将多达 24 TB（120 TB 横向扩展）内存用于 S/4HANA 或其他 SAP HANA 工作负荷的 SAP HANA 部署。 

在 Azure 中托管 SAP 工作负荷方案可能还会生成标识集成和单一登录的需求。 使用 Azure Active Directory (Azure AD) 连接不同的 SAP 组件和 SAP 软件即服务 (SaaS) 或平台即服务 (PaaS) 产品时，可能会出现这种情况。 “AAD SAP 标识集成和单一登录”部分介绍和记录了具有 Azure AD 和 SAP 实体的这类集成和单一登录方案的列表。

## <a name="changes-to-the-sap-workload-section"></a>对 SAP 工作负荷部分的更改
本文末尾列出了对 Azure 上的 SAP 工作负荷部分中文档的更改。 更改日志中的条目将保留大约 180 天。

## <a name="you-want-to-know"></a>你想了解的内容
如果你有特定的问题，我们将在起始页的此部分中为你指出特定的文档或流程。 你想了解：

- 哪些 SAP 软件版本和哪些操作系统版本支持哪些 Azure VM 和 HANA 大型实例单元。 请阅读文档 [Azure 部署支持的 SAP 软件](./sap-supported-product-on-azure.md)，以获取答案和信息查找过程
- Azure VM 和 HANA 大型实例支持哪些 SAP 部署方案。 可在以下文档中找到有关受支持方案的信息：
    - [Azure 虚拟机上的 SAP 工作负荷支持的方案](./sap-planning-supported-configurations.md)
    - [HANA 大型实例支持的方案](./hana-supported-scenario.md)
- 不同的 Azure 区域提供哪些 Azure 服务、Azure VM 类型和 Azure 存储服务，请查看站点[可用产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/) 
- 第三方 HA 帧是否适用于 Windows 和 Pacemaker 以外的支持？ 查看[SAP 支持说明](https://launchpad.support.sap.com/#/notes/1928533)的底部部分 #1928533
- 哪种 Azure 存储最适合我的方案？ 读取 [SAP 工作负荷的 Azure 存储类型](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide-storage)

 
## <a name="sap-hana-on-azure-large-instances"></a>Azure 上的 SAP HANA（大型实例）

可通过阅读一系列文档来了解 Azure 上的 SAP HANA（大型实例），简称 HANA 大型实例。 有关 HANA 大型实例的信息，请先阅读文档 [Azure 上的 SAP HANA（大型实例）概述和体系结构](./hana-overview-architecture.md)，然后阅读 HANA 大型实例部分中的相关文档



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 虚拟机上的 SAP HANA
文档的此部分介绍 SAP HANA 的不同方面。 作为先决条件，应熟悉 Azure 的主体服务，它们提供 Azure IaaS 的基本服务。 因此，你需要了解 Azure 计算、存储和网络。 与 SAP NetWeaver 相关的 [Azure 规划指南](./planning-guide.md)介绍了许多这类主题。 

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure 虚拟机上部署的 SAP NetWeaver
本部分列出了 Azure 上的 SAP NetWeaver、SAP LaMa 和 Business One 的规划和部署文档。 该文档重点介绍了 Azure 中 SAP 工作负荷下的非 HANA 数据库的基本知识和使用情况。 用于实现高可用性的文档和文章也是 Azure 中 SAP HANA 高可用性的基础

有关 Azure 中 SAP 工作负荷的高可用性的信息，请参阅：

- [Azure 虚拟机上 SAP NetWeaver 的高可用性](./sap-high-availability-guide-start.md)



有关 Azure Active Directory (Azure AD) 与 SAP 服务之间的集成和单一登录的信息，请参阅：

- [教程：将 Azure Active Directory 与 SAP Cloud for Customer 集成](../../../active-directory/saas-apps/sap-customer-cloud-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP Cloud Platform Identity Authentication 集成](../../../active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP Cloud Platform 集成](../../../active-directory/saas-apps/sap-hana-cloud-platform-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP NetWeaver 集成](../../../active-directory/saas-apps/sap-netweaver-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP Business ByDesign 集成](../../../active-directory/saas-apps/sapbusinessbydesign-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP HANA 集成](../../../active-directory/saas-apps/saphana-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [S/4HANA 环境：通过 Azure AD 执行的 Fiori Launchpad SAML 单一登录](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

有关将 Azure 服务集成到 SAP 组件的信息，请参阅：

- [在 Power BI Desktop 中使用 SAP HANA](/power-bi/desktop-sap-hana)
- [DirectQuery 和 SAP HANA](/power-bi/desktop-directquery-sap-hana)
- [在 Power BI Desktop 中使用 SAP BW 连接器](/power-bi/desktop-sap-bw-connector) 
- [Azure 数据工厂提供 SAP HANA 和业务数据仓库数据集成](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>更改日志

- 08/25/2020： [针对使用 wsfc 和共享磁盘的 SAP ASCS/scs 的 HA 指南进行](./sap-high-availability-guide-wsfc-shared-disk.md)更改，使用 wsfc 和共享磁盘为 [SAP ASCS/scs 准备 Azure 基础结构](./sap-high-availability-infrastructure-wsfc-shared-disk.md) ，并使用 [WSFC 和共享磁盘安装 sap NW HA](./sap-high-availability-guide-wsfc-shared-disk.md) ，以引入使用 AZURE 共享磁盘和文档 SAP ERS2 体系结构的选项
- 08/25/2020： [为 SAP ASCS/SCS 和 WSFC 和 Azure 共享磁盘发布多 SID HA 指南](./sap-ascs-ha-multi-sid-wsfc-azure-shared-disk.md)
- 08/25/2020： [通过 WSFC 和 Azure NetApp 文件 (SMB) 中的 SAP ASCS/SCS 的 HA 指南进行 ](./high-availability-guide-windows-netapp-files-smb.md)更改使用 wsfc 和 [文件共享为 SAP ASCS/scs 准备 Azure 基础结构](./sap-high-availability-infrastructure-wsfc-file-share.md)、 [包含 WSFC 和共享磁盘的 sap ASCS/scs 的多 sid ha 指南](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 、包含 WSFC 和 [SOFS 文件共享的 sap ASCS/scs](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 的多 sid ha 指南，作为使用 WFC 和共享磁盘的 sap ASCS/scs 的 HA 指南 
- 08/21/2020：将新的操作系统版本添加到适用于 [HANA 大型实例的兼容操作系统](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance) 中，为类型为 I 和 II 的
- 08/18/2020： [在 RHEL 上发布和的 HA SAP HANA 向上扩展](./sap-hana-high-availability-netapp-files-red-hat.md)
- 08/17/2020：添加有关使用 Azure Site Recovery 将 SAP NetWeaver 系统从本地迁移到 Azure 的信息，请阅读 [Azure 虚拟机计划和实施 Sap NetWeaver](./planning-guide.md)
- 08/14/2020：为[SAP 工作负荷的 IBM Db2 Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)一文中的 Db2 添加磁盘配置建议
- 08/11/2020：将 RHEL 7.6 添加到适用于[HANA 大型实例的操作系统](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance)
- 08/10/2020： SAP HANA 存储配置引入 [SAP HANA azure 虚拟机存储](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 配置中，并对 [Azure 上的 SAP 工作负荷进行一些更新：规划和部署清单](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 08/04/2020：更改在 [azure 中的 SLES 上设置 Pacemaker](./high-availability-guide-suse-pacemaker.md) ，并在 [AZURE 中设置 RHEL 上的 Pacemaker](./high-availability-guide-rhel-pacemaker.md) ，以强调 Pacemaker 群集的可靠名称解析的重要性
- 08/04/2020：在 WFCS 中更改 [SAP NW ha with 文件共享](./sap-high-availability-installation-wsfc-file-share.md)， [WFCS 上的 sap Nw Ha](./sap-high-availability-installation-wsfc-shared-disk.md)， [azure vm 上的](./high-availability-guide.md)sap nw ha，azure [VM 上](./high-availability-guide-suse.md)的 Sap nw ha，和上的 [azure Vm 上的 sap NW ha](./high-availability-guide-suse-netapp-files.md)，适用于 azure 上的 [sap nw 的 ha 多 sid 指南](./high-availability-guide-suse-multi-sid.md)，azure vm 上的 sap NW [高可用性](./high-availability-guide-rhel.md)，RHEL 上的 azure vm 上的 sap [nw](./high-availability-guide-rhel-netapp-files.md) 高可用性，rhel [多 sid 指南](./high-availability-guide-rhel-multi-sid.md) 中的 azure vm 上的 sap nw 高可用性，用于阐明参数的使用 `enque/encni/set_so_keepalive`
- 07/23/2020： [通过 Azure 预订](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md) 添加了 "保存到 SAP HANA 大型实例" 一文，其中介绍了在购买 SAP HANA 大型实例预约之前需要了解的内容以及如何进行购买
- 07/16/2020：介绍如何使用 Azure PowerShell 在[部署指南](deployment-guide.md)中安装适用于 SAP 的新 VM 扩展
- 7/04/2020：发布  [适用于 SAP 的 Azure monitor 解决方案 (预览) ](./azure-monitor-overview.md)
- 07/01/2020：根据文档[SAP HANA azure 虚拟机存储配置](./hana-vm-operations-storage.md)中的 azure 高级存储突发功能，建议更昂贵的存储配置 
- 06/24/2020：在 [azure 中的 SLES 上设置 Pacemaker](./high-availability-guide-suse-pacemaker.md) 以发布新改进的 Azure 隔离代理，并基于 Azure 隔离代理为设备提供更具弹性的 STONITH 配置 
- 06/24/2020：在 [Azure 中将 Pacemaker 设置](./high-availability-guide-rhel-pacemaker.md) 为发布更具弹性的 STONITH 配置
- 06/23/2020：针对 sap [NetWeaver 的 Azure 虚拟机计划和实施的更改规划和实施](./planning-guide.md) 指南和 [azure 存储类型](./planning-guide-storage.md) 指南简介
- 06/22/2020：将适用于 SAP 的新 VM 扩展的安装步骤添加到 [部署指南](deployment-guide.md)
- 06/16/2020： [在 SAP HA 方案中，使用 Azure 标准 ILB 为 vm 更改公共终结点连接](./high-availability-guide-standard-load-balancer-outbound-connections.md) ，以添加指向 SUSE 公有云基础结构101文档的链接 
- 06/10/2020：将新的 B-HLI Sku 添加到 [可用 sku](./hana-available-skus.md) 中，并 [SAP HANA (大型实例) 存储体系结构](./hana-storage-architecture.md)
- 2020/05/21：更改[在 Azure 中的 SLES 上设置 Pacemaker](./high-availability-guide-suse-pacemaker.md) 和[在 Azure 中的 RHEL 上设置 Pacemaker](./high-availability-guide-rhel-pacemaker.md) 中的内容，以添加指向 [SAP HA 方案中使用 Azure 标准 ILB 的 VM 的公共终结点连接](./high-availability-guide-standard-load-balancer-outbound-connections.md)的链接  
- 2020/05/19：在 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)中添加一条重要消息，指示在将 LVM 用于与 HANA 相关的卷时，不要使用根卷组
- 05/19/2020：在[Hana 大型实例的兼容操作系统](/- azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance)中为 Hana 大型实例类型 II 添加新的受支持的 OS
- 2020/05/12：更改 [SAP HA 方案中使用 Azure 标准 ILB 的 VM 的公共终结点连接](./high-availability-guide-standard-load-balancer-outbound-connections.md)中的内容，以更新链接并添加有关第三方防火墙配置的信息
- 2020/05/11：更改 [SLES 上 Azure VM 中的 SAP HANA 的高可用性](./sap-hana-high-availability.md)中的内容，以将 netcat 资源的资源粘性设置为 0，因为这样可以简化故障转移 
- 2020/05/05：更改[适用于 SAP NetWeaver 的 Azure 虚拟机规划和实施](./planning-guide.md)中的内容，以表明 Gen2 部署可用于 Mv1 VM 系列
- 2020/04/24：更改[在 SLES 上的 Azure VM 中通过 ANF 使用备用节点横向扩展 SAP HANA](./sap-hana-scale-out-standby-netapp-files-suse.md)、[在 RHEL 上的 Azure VM 中通过 ANF 使用备用节点横向扩展 SAP HANA](./sap-hana-scale-out-standby-netapp-files-rhel.md)、[在 SLES 上的 Azure VM 中通过 ANF 实现 SAP NetWeaver 的高可用性](./high-availability-guide-suse-netapp-files.md)和[在 RHEL 上的 Azure VM 中通过 ANF 实现 SAP NetWeaver 的高可用性](./high-availability-guide-rhel-netapp-files.md)中的内容，以添加系统自动为 ANF 卷分配 IP 地址的说明
- 2020/04/22：更改 [SLES 上 Azure VM 中的 SAP HANA 的高可用性](./sap-hana-high-availability.md)中的内容，以从指令中删除元属性 `is-managed`，因为它与使群集进入或退出维护模式相冲突
- 2020/04/21：在文章 [Azure 部署支持的 SAP 软件](./sap-supported-product-on-azure.md)和[在 Microsoft Azure 上运行的 SAP 认证和配置](./sap-certifications.md)中，添加了 SQL Azure DB 作为 SAP (Hybris) Commerce Platform 1811 和更高版本支持的 DBMS
- 2020/04/16：在文章 [Azure 部署支持的 SAP 软件](./sap-supported-product-on-azure.md)和[在 Microsoft Azure 上运行的 SAP 认证和配置](./sap-certifications.md)中，添加了 SAP HANA 作为 SAP (Hybris) Commerce Platform 支持的 DBMS
- 2020/04/13：在[适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署](./dbms_guide_sapase.md)中更正为确切的 SAP ASE 版本号
- 2020/04/07：更改[在 Azure 中的 SLES 上设置 Pacemaker](./high-availability-guide-suse-pacemaker.md) 中的内容，以阐明 cloud-netconfig-azure 指令
- 2020/04/06：更改[在 SLES 上的 Azure VM 中通过 Azure NetApp 文件使用备用节点横向扩展 SAP HANA](./sap-hana-scale-out-standby-netapp-files-suse.md) 和[在 RHEL 上的 Azure VM 中通过 Azure NetApp 文件使用备用节点横向扩展 SAP HANA](./sap-hana-scale-out-standby-netapp-files-rhel.md) 中的内容，以删除对 NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf)（由 [TR-4746](https://www.netapp.com/us/media/tr-4746.pdf) 代替）的引用
- 2020年3月31日：在 Azure vm 上更改[SAP HANA 高可用性](./sap-hana-high-availability.md)，在[RHEL 上，azure Vm 上的高可用性 SAP HANA](./sap-hana-high-availability-rhel.md)
- 2020年3月27日： [和 FOR sap 应用程序的 Azure vm 上的 SAP NW 的高可用性](./high-availability-guide-suse-netapp-files.md) 更改，使文件系统装载选项与 NetApp TR- (4746 保持同步，删除同步装载选项) 
- 2020年3月26日：在 [SLES 多 SID 4746 指南上，Azure vm 上的 SAP NetWeaver 的高可用性](./high-availability-guide-suse-multi-sid.md) 更改
- 2020年3月26日，azure Vm 上的 [Sap NetWeaver 的高可用性](./high-availability-guide-suse.md)更改，适用于 sap 应用程序的 azure vm 上的 sap NetWeaver 的高可用性，适用于 [sap 应用程序的 azure NetApp 文件](./high-availability-guide-suse-netapp-files.md)，在 rhel 上，Azure Vm 上的 [azure vm 的高可用性](./high-availability-guide-suse-nfs.md)，在 [rhel 多 SID 指南](./high-availability-guide-suse-multi-sid.md)上，azure vm 上的 sap NetWeaver 高可用性，适用于 sap 的 azure vm 上的 sap [NetWeaver 的高可用性](./high-availability-guide-rhel.md) ，适用于 sap 应用程序的 sap 应用程序和用于 sap [应用程序的高可用性](./high-availability-guide-rhel-netapp-files.md) 的 sap NetWeaver
- 2020年3月19日：文档快速入门的主要修订版 [：在 Azure 虚拟机上手动安装单实例 SAP HANA](./hana-get-started.md) ，以 [在 azure 虚拟机上安装 SAP HANA](./hana-get-started.md)
- 2020年3月17日：更改在 [Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker](./high-availability-guide-suse-pacemaker.md) ，以删除不再需要的 SBD 配置设置
- 16 2020 年3月：在[Azure 部署支持的 SAP 软件](./sap-supported-product-on-azure.md)的 SAP HANA IaaS 认证平台中阐明列认证方案
- 2020/03/11：更改 [Azure 虚拟机上的 SAP 工作负荷支持的方案](./sap-planning-supported-configurations.md)中的内容，以阐明每个 DBMS 实例多个数据库支持
- 2020年3月11日， [SAP NetWeaver 的 Azure 虚拟机规划和实施中的](./planning-guide.md) 更改说明第1代和第2代 vm
- 2020年3月10日：在 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md) 中进行更改，以明确和的实际吞吐量限制
- 2020年3月09日：在 sap [应用程序 SUSE Linux Enterprise Server 上，Azure vm 上 Sap NetWeaver 的高可用性](./high-availability-guide-suse.md)变化，azure [vm SUSE LINUX ENTERPRISE SERVER 上的 sap NetWeaver 的高可用性，适用于 sap 应用程序的 azure NetApp 文件](./high-availability-guide-suse-netapp-files.md)， [SUSE Linux Enterprise Server 上的 azure vm 上的 NFS 的高](./high-availability-guide-suse-nfs.md)可用性，在 azure 上的 SUSE Linux Enterprise Server 上设置 Pacemaker [，Azure](./high-availability-guide-suse-pacemaker.md)vm 上的 IBM [Db2 LUW 具有 Pacemaker、高](./dbms-guide-ha-ibm.md)可用性 SAP HANA，azure vm 上的 azure vm 上的高可用性，高可用性 [azure](./sap-hana-high-availability.md) [vm 上的](./high-availability-guide-suse-multi-sid.md) azure vm SUSE Linux Enterprise Server SUSE Linux Enterprise Server 
- 2020年3月5日，azure 虚拟机中的 Azure 区域和 Azure 虚拟机的结构更改和内容更改， [SAP NetWeaver 的规划和实施](./planning-guide.md)
- 2020/03/03：更改[在 SLES for SAP Applications 上的 Azure VM 中通过 ANF 实现 SAP NW 的高可用性](./high-availability-guide-suse-netapp-files.md)中的内容，以更改为更有效的 ANF 卷布局
- 2020年3月1日： [有关 Azure 虚拟机上的 SAP HANA 的改编备份指南](./sap-hana-backup-guide.md) ，包括 azure 备份服务。 减少和压缩了[文件级别的 SAP HANA Azure 备份](./sap-hana-backup-file-level.md)中的内容，并删除了有关通过磁盘快照处理备份的第三个文档。 处理了《Azure 虚拟机上的 SAP HANA 备份指南》中的内容 
- 2020年2月27日：对于 Azure 上的 SAP NW，适用于 sap [应用程序的高可用性](./high-availability-guide-suse.md)，适用于 sap 的 azure vm 上的 sap nw 的高可用性， [和 for sap 应用程序](./high-availability-guide-suse-netapp-files.md) 的高可用性和 [Azure vm](./high-availability-guide-suse-multi-sid.md) 上的 sap NetWeaver 的高可用性
- 2020年2月26日：在 [SAP HANA azure 虚拟机存储配置](./hana-vm-operations-storage.md) 中进行更改，以便在 azure 上阐明 HANA 的文件系统选择
- 2020年2月26日：对 [高可用性体系结构和方案进行更改，](./sap-high-availability-architecture-scenarios.md) 以便在 RHEL 多 SID 指南
- 2020年2月26日：对于 Azure 上的 SAP NW，适用于 sap 应用程序的[高可用性](./high-availability-guide-suse.md)，适用于 sap 应用程序的 azure vm 上的 sap nw 的高可用性，适用于 sap 应用程序的 azure vm [Azure VMs high availability for SAP NetWeaver on RHEL with Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md)上的 sap nw 具有高可用性，在 rhel[上，azure](./high-availability-guide-rhel.md) vm 高可用性适用于[和 for](./high-availability-guide-suse-netapp-files.md)sap
- 2020年2月26日，  [Azure vm 上的 SAP NetWeaver 的高可用性发布在 RHEL 多 sid 指南](./high-availability-guide-rhel-multi-sid.md) 中，添加指向 SUSE 多 sid 群集指南的链接
- 2020/02/25：更改[适用于 SAP 的高可用性体系结构和方案](./sap-high-availability-architecture-scenarios.md)中的内容，以添加指向较新 HA 文章的链接
- 2020年2月25日：在 [使用 Pacemaker 的 SUSE Linux Enterprise Server 上的 Azure vm 上使用 IBM DB2 LUW 的高可用性，通过](./dbms-guide-ha-ibm.md) 标准 Azure 负载均衡器指向介绍访问公共终结点的文档
- 2020年2月21日：文章[SAP ASE Azure 虚拟机 DBMS 部署（适用于 sap 工作负荷](./dbms_guide_sapase.md)）
- 2020年2月21日：在 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md) 中更改以表示/hana/data 的条带大小的新建议以及添加 i/o 计划程序的设置
- 2020年2月21日： HANA 大型实例文档中的更改，用于表示 S224 和 S224m 的新认证 Sku
- 2020年2月21日： azure [vm 中的 Sap NetWeaver 高可用性](./high-availability-guide-rhel.md) ，Azure 虚拟机上的 sap NetWeaver 的高可用性，以及 Azure [NetApp 文件](./high-availability-guide-rhel-netapp-files.md) ，用于调整排队服务器复制2体系结构的群集约束 (ENSA2) 
- 2020年2月20日：在 [SLES 多 sid 指南上，Azure vm 上 SAP NetWeaver 的高可用性](./high-availability-guide-suse-multi-sid.md) 变化添加了 SUSE 多 sid 群集指南的链接
- 2020年2月13日： [针对 SAP NetWeaver 的 Azure 虚拟机规划和实施的](./planning-guide.md) 更改，以实现新文档的链接
- 2020年2月13日： [在 Azure 虚拟机支持的方案中增加了新的文档 SAP 工作负荷](./sap-planning-supported-configurations.md)
- 2020年2月13日：添加 [了新文档 Azure 部署支持的 SAP 软件](./sap-supported-product-on-azure.md)
- 2020年2月13日：在 [Red Hat Enterprise Linux 服务器上的 Azure vm 上更改 IBM DB2 LUW 的高可用性](./high-availability-guide-rhel-ibm-db2-luw.md) ，以指向介绍使用标准 Azure 负载均衡器访问公用终结点的文档
- 2020年2月13日：将新的 VM 类型添加到 [Microsoft Azure 上运行的 SAP 认证和配置](./sap-certifications.md)
- 2020年2月13日：添加新的 SAP 支持说明 [Azure 上的 sap 工作负荷：规划和部署清单](./sap-deployment-checklist.md)
- 2020年2月13日： azure [vm 的高可用性 Azure vm 中的 Sap NetWeaver 的高可用性](./high-availability-guide-rhel.md)，以及 Azure[虚拟机上的 sap NetWeaver](./high-availability-guide-rhel-netapp-files.md)
- 2020年2月11日：发布 [Azure 大型实例上的 SAP HANA 迁移到 Azure 虚拟机](./hana-large-instance-virtual-machine-migration.md)
- 2020年2月7日： [使用 Azure STANDARD ILB 在 SAP HA 方案中更改 vm 的公共终结点连接](./high-availability-guide-standard-load-balancer-outbound-connections.md) ，以更新示例 NSG 屏幕快照
- 2020年2月3日：对于 Azure 上的 [sap nw，Azure vm 上的 sap nw 的高可用性，适用](./high-availability-guide-suse.md) 于 sap 应用程序的 azure vm 上的 sap Nw 的高可用性和 [高可用性](./high-availability-guide-suse-netapp-files.md) ，用于在 sles 上的群集节点的主机名中消除有关使用短划线的警告
- 2020年1月28日：在 [RHEL 上的 Azure vm 上更改 SAP HANA 的高可用性](./sap-hana-high-availability-rhel.md) ，以将 SAP HANA 群集资源超时调整为 Red Hat 超时建议
- 2020年1月17日：更改 [Azure 邻近位置组以获得最佳网络延迟，以便 SAP 应用程序](./sap-proximity-placement-scenarios.md) 更改将现有 vm 移到邻近位置组的部分
- 2020年1月17日：更改 [SAP 工作负荷配置，并提供 Azure 可用性区域](./sap-ha-availability-zones.md) 以指向在可用性区域之间自动测定延迟的过程
- 2020年1月16日： [如何安装和配置在 Azure 上) SAP HANA (大型实例](./hana-installation.md) ，以将操作系统版本调整为 HANA IaaS 硬件目录
- 2020年1月16日：有关 [Azure vm 上的 Sap NetWeaver 的高可用性](./high-availability-guide-suse-multi-sid.md) 更改，请使用队列服务器2体系结构 (ENSA2) ，为 sap 系统添加说明
- 2020年1月10日：在 Azure Vm 上的 azure 虚拟机上，通过 azure [虚拟机上的备用节点 SAP HANA 扩展](./sap-hana-scale-out-standby-netapp-files-suse.md) ，以及 SAP HANA 在 RHEL 上通过 azure [netapp 文件扩展](./sap-hana-scale-out-standby-netapp-files-rhel.md) 到 azure 虚拟机上的 azure netapp 文件，以添加有关如何 `nfs4_disable_idmapping` 永久更改的说明。
- 2020年1月10日：在 SLES 上， [Azure vm 上的 Sap NetWeaver 的高可用性更改与 sap 应用程序的 Azure Netapp 文件](./high-availability-guide-suse-netapp-files.md) 以及 [azure 虚拟机中 sap NetWeaver 的高可用性，适用于 sap 应用程序的 azure netapp 文件](./high-availability-guide-rhel-netapp-files.md) ，用于添加有关如何装载 azure netapp 文件 NFSv4 卷的说明。
- 2019 年 12 月 23 日：发布 [SLES 上 Azure VM 中的 SAP NetWeaver 的高可用性多 SID 指南](./high-availability-guide-suse-multi-sid.md)
- 2019 年 12 月 18 日：发布[在 RHEL 上的 Azure VM 中通过 Azure NetApp 文件使用备用节点横向扩展 SAP HANA](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- 2019 年 11 月 21 日：更改[在 SUSE Linux Enterprise Server 上的 Azure VM 中通过 Azure NetApp 文件使用备用节点横向扩展 SAP HANA](./sap-hana-scale-out-standby-netapp-files-suse.md) 中的内容，以简化 NFS ID 映射的配置，并更改建议的主网络接口以简化路由。
- 2019 年 11 月 15 日：对[在 SUSE Linux Enterprise Server for SAP Applications 上通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](high-availability-guide-suse-netapp-files.md)和[在 Red Hat Enterprise Linux for SAP Applications 上通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](high-availability-guide-rhel-netapp-files.md)中的内容进行一些小改动，以阐明容量池大小限制并删除仅支持 NFSv3 版本的声明。
- 2019 年 11 月 12 日：发布[在 Windows 上通过 Azure NetApp 文件 (SMB) 实现 SAP NetWeaver 的高可用性](high-availability-guide-windows-netapp-files-smb.md)
- 2019 年 11 月 8 日：更改 [SUSE Linux Enterprise Server 上 Azure VM 中的 SAP HANA 的高可用性](sap-hana-high-availability.md)、[在 Azure 虚拟机 (VM) 上设置 SAP HANA 系统复制](sap-hana-high-availability-rhel.md)、[SUSE Linux Enterprise Server for SAP Applications 上 Azure 虚拟机中的 SAP NetWeaver 的高可用性](high-availability-guide-suse.md)、[在 SUSE Linux Enterprise Server 上的 Azure 虚拟机中通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](high-availability-guide-suse-netapp-files.md)、[Red Hat Enterprise Linux 上 Azure 虚拟机中的 SAP NetWeaver 的高可用性](high-availability-guide-rhel.md)、[在 Red Hat Enterprise Linux 上的 Azure 虚拟机中通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](high-availability-guide-rhel-netapp-files.md)、[SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性](high-availability-guide-suse-nfs.md)和[适用于 SAP NetWeaver 的 Red Hat Enterprise Linux 上的 Azure VM 中的 GlusterFS](high-availability-guide-rhel-glusterfs.md) 中的内容，以推荐 Azure 标准负载均衡器  
- 2019 年 11 月 8 日：更改 [SAP 工作负荷规划和部署清单](sap-deployment-checklist.md)中的内容，以阐明加密建议  
- 2019 年 11 月 4 日：更改[在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker](high-availability-guide-suse-pacemaker.md) 中的内容，以直接使用单播配置创建群集  
