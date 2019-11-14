---
title: Azure Vm 上的 SAP 入门 |Microsoft Docs
description: 了解 Microsoft Azure 中的虚拟机（Vm）上运行的 SAP 解决方案
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
ms.date: 11/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f9d1198a0faca8eed2ca5d3f3ee89ac9019d657
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073910"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>使用 Azure 托管和运行 SAP 工作负荷方案

当你使用 Microsoft Azure 时，你可以在可扩展、相容和企业验证的平台上可靠地运行任务关键型 SAP 工作负荷和方案。 你可以获得 Azure 的可伸缩性、灵活性和成本节约。 通过 Microsoft 与 SAP 之间的扩展合作关系，你可以在 Azure 中的开发和测试和生产方案中运行 SAP 应用程序，并完全受支持。 从 SAP NetWeaver 到 SAP S/4HANA、在 Linux 上的 SAP BI 到 Windows 以及 SAP HANA 到 SQL，我们都能满足你的需求。

除了在 Azure 上托管不同 DBMS 的 SAP NetWeaver 方案，你还可以托管其他 SAP 工作负荷方案，如 Azure 上的 SAP BI。 

Azure for SAP HANA 的唯一性是将 Azure 分开的一个产品/服务。 为了能够承载涉及 SAP HANA 的更多内存和 CPU 资源要求苛刻的 SAP 方案，Azure 提供了客户专用的裸机硬件的使用。 使用此解决方案来运行 SAP HANA 的部署，这些部署需要多达 24 120 TB 的内存用于 S/4HANA 或其他 SAP HANA 工作负荷。 

在 Azure 中托管 SAP 工作负荷方案还可以创建标识集成和单一登录的要求。 当你使用 Azure Active Directory （Azure AD）来连接不同的 SAP 组件和 SAP 软件即服务（SaaS）或平台即服务（PaaS）产品/服务（PaaS）产品/服务时，可能会出现这种情况。 "AAD SAP 标识集成和单一登录" 一节中介绍并记录了包含 Azure AD 和 SAP 实体的此类集成和单一登录方案的列表。

## <a name="changes-to-the-sap-workload-section"></a>SAP 工作负荷部分的更改
本文末尾列出了 "Azure 上的 SAP 工作负荷" 部分中文档的更改。


## <a name="sap-hana-on-azure-large-instances"></a>Azure 上的 SAP HANA（大型实例）

一系列文档将引导你完成 Azure （大型实例）上的 SAP HANA，或用于简短的 HANA 大型实例。 有关 HANA 大型实例的以下区域的信息，请参阅：

- [Azure 上的 SAP HANA（大型实例）的概述](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure 上的 SAP HANA（大型实例）体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Azure 上的 SAP HANA （大型实例）的基础结构和连接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [在 Azure 上安装 SAP HANA （大型实例）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Azure 上的 SAP HANA （大型实例）的高可用性和灾难恢复](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Azure 上的故障排除和监视 SAP HANA （大型实例）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

后续步骤：

- 了解[Azure 上的 SAP HANA 的概述和体系结构（大型实例）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 虚拟机上的 SAP HANA
文档的此部分介绍 SAP HANA 的不同方面。 作为先决条件，你应该熟悉 Azure 的主要服务，提供 Azure IaaS 的基本服务。 因此，你需要了解 Azure 计算、存储和网络。 其中的许多主题在与 SAP NetWeaver 相关的[Azure 规划指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中进行了处理。 

有关 Azure 上的 HANA 的信息，请参阅以下文章及其 subarticles：

- [快速入门：在 Azure VM 上手动安装单实例 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [在 Azure 上部署 SAP S/4HANA 或 BW/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Azure 上的 SAP HANA 基础结构配置和操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure 虚拟机的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [一个 Azure 区域内的 SAP HANA 可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [跨 Azure 区域的 SAP HANA 可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Azure 虚拟机上的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure 虚拟机上 SAP HANA 的备份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [文件级别的 SAP HANA Azure 备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [基于存储快照的 SAP HANA 备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure 虚拟机上部署的 SAP NetWeaver
本部分列出了 Azure 上的 SAP NetWeaver 和企业版的规划和部署文档。 本文档重点介绍 Azure 上的 SAP 工作负荷的基本知识和使用情况。 高可用性的文档和文章也是 Azure 中 HANA 高可用性的基础，例如：

- [Azure 虚拟机上的 SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [在 Azure 上为 SAP ERP 6.0 部署 SAP IDES EHP7 SP3](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [在 Microsoft Azure SUSE Linux Vm 上运行 SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [SAP NetWeaver 的 Azure 虚拟机规划和实施指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [SAP NetWeaver 的 Azure 虚拟机部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [使用 Site Recovery 保护多层 SAP NetWeaver 应用程序部署](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [适用于 Azure 的 SAP LaMa 连接器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

有关 Azure 上的 SAP 工作负荷下的非 HANA 数据库的信息，请参阅：

- [部署适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 的注意事项](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [适用于 SAP NetWeaver 的 SQL Server Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [适用于 SAP 工作负荷的 Oracle Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [适用于 SAP 工作负荷的 IBM DB2 Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Azure Vm 上的 SAP MaxDB、实时缓存和内容服务器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

有关 Azure 上的 SAP HANA 数据库的信息，请参阅 Azure 虚拟机上的 SAP HANA 部分。

有关 Azure 上的 SAP 工作负荷的高可用性的信息，请参阅：

- [Azure 虚拟机上 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

本文档指向各种其他体系结构和方案文档。 在后面的方案文档中，提供了指向详细技术文档的链接，这些文档介绍了如何部署和配置不同的高可用性方法。 介绍如何为 SAP NetWeaver 工作负荷建立和配置高可用性的不同文档包括 Linux 和 Windows 操作系统。


有关 Azure Active Directory （Azure AD）与 SAP 服务和单一登录之间的集成的详细信息，请参阅：

- [教程：将 Azure Active Directory 与 SAP Cloud for Customer 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP Cloud Platform Identity Authentication 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP Cloud Platform 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP NetWeaver 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP Business ByDesign 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP HANA 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [S/4HANA 环境： Fiori 启动板 SAML 单一登录 Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

有关将 Azure 服务集成到 SAP 组件的信息，请参阅：

- [在 Power BI Desktop 中使用 SAP HANA](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 和 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [在 Power BI Desktop 中使用 SAP BW 连接器](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure 数据工厂提供 SAP HANA 和业务数据仓库数据集成](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>更改日志
- 11/12/2019：[在 Windows 上发布 SAP NetWeaver On Azure NetApp 文件（SMB）的高可用性](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019：在[SUSE Linux Enterprise Server 上的 Azure vm 上 SAP HANA 的高可用性](sap-hana-high-availability.md)变化，在[azure 虚拟机（vm）上设置 SAP HANA 系统复制](sap-hana-high-availability-rhel.md)，azure[虚拟机高可用性适用于 sap 应用程序的 SUSE Linux Enterprise Server](high-availability-guide-suse.md)，适用于 sap NetWeaver 的 Azure 虚拟机高可用性， [azure NetApp 文件](high-availability-guide-suse-netapp-files.md)，azure 虚拟机高可用性，适用于 SUSE Linux Enterprise Server[上的 sap NetWeaver](high-availability-guide-rhel.md)， [Azure 虚拟机 Red Hat Enterprise Linux 上的 sap NetWeaver 高可用性 Azure NetApp 文件](high-availability-guide-rhel-netapp-files.md)，azure vm 上的 azure 虚拟机[高可用性 SUSE Linux Enterprise Server](high-availability-guide-suse-nfs.md)上，azure Vm 上的 azure [Red Hat Enterprise Linux vm GlusterFS](high-availability-guide-rhel-glusterfs.md) ）建议 azure 标准负载均衡器  
- 11/08/2019： [SAP 工作负荷规划和部署核对清单](sap-deployment-checklist.md)中的更改阐明了加密建议  
- 11/04/2019：在 Azure 中的[SUSE Linux Enterprise Server 上设置 Pacemaker](high-availability-guide-suse-pacemaker.md) ，以直接通过单播配置创建群集  
- 10/29/2019：[使用 Azure 标准负载均衡器在 SAP 高可用性方案中发布虚拟机的公共终结点连接](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 10/25/2019： [SAP HANA azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中的更改，并[SAP HANA 在 azure vm 上用备用节点向外扩展，并在 SUSE Linux Enterprise Server 上提供 azure NetApp 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)来阐明/hana/shared 卷的 NFS 协议
- 10/22/2019：在 sap[应用程序 SUSE Linux Enterprise Server 上更改 Sap NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)，azure vm 上的 Sap [SUSE Linux Enterprise Server NetWeaver 的高可用性，适用于 sap 应用程序的 azure NetApp 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)，Azure [vm 上的 azure vm 的高可用性 SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)，在 azure 上的[SUSE Linux Enterprise Server 上设置 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)， [Azure SUSE LINUX ENTERPRISE SERVER vm 上的 IBM Db2 LUW 的高可用性与 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)，azure [vm 上的 SAP HANA 和高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)，适用于 Azure 负载平衡器检测强化 SUSE Linux Enterprise Server
- [SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中更改和节和标头部分
- 10/21/2019：通过在[SLES 上包含 Azure NetApp 文件的 Azure vm 上的备用节点 SAP HANA 扩展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)的发布
- 10/16/2019：修复[备份和还原](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)中的损坏链接
- 10/16/2019：[通过 Pacemaker SUSE Linux Enterprise Server 上的 Azure vm 上的 Azure vm 中的 Azure vm 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)，将建议的最低操作系统版本更改为 SLES 12 SP4
- 10/11/2019：对和[Azure 虚拟机存储配置 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中的 Ultra 磁盘存储配置和引入的更改
- 10/01/2019：更改[Azure 邻近位置组的图形，以实现与 SAP 应用程序的最佳网络延迟](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)，使其更清晰
- 10/01/2019：更改[Azure 上 SAP HANA 基础结构配置和操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)，以便更正适用于/hana/shared. 的高度可用 NFS 共享的声明 
- 09/28/2019：在[Azure 中的 Red Hat Enterprise Linux 上设置 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)以将 SBD 阐明为在 RHEL 群集上不支持防护机制  
- 09/17/2019：在 NetWeaver 规划和部署指南中进行更改，以统一围绕适用于 SAP 的 VM 扩展术语  
- 08/22/2019：在[Azure 中 SUSE Linux Enterprise Server 设置 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)以更新自定义角色创建的 url  
- 08/16/2019：在[azure 中设置 Pacemaker Red Hat Enterprise Linux 的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)更改，以提醒客户更新自定义角色中的操作（如果更新到新版本的 azure 隔离代理）  
- 08/15/2019： [SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中的更改，以反映超磁盘（以前称为超级 SSD）的公开上市
- 08/01/2019：在[Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)以将更改专用于 SLES 15 的更改 
- 07/23/2019：[通过在 Azure 中使用文件共享在 Windows 故障转移群集上对 SAP ASCS/SCS 实例进行群集](sap-high-availability-guide-wsfc-file-share.md)更改，以反映 Azure Site Recovery 服务对存储空间直通的支持
- 07/14/2019：发布[Azure 邻近组，以实现 SAP 应用程序的最佳网络延迟](sap-proximity-placement-scenarios.md)
- 07/11/2019：涵盖 HANA 大型实例的各种文档中的更改包含 HANA 大型实例的修订版4
- 07/09/2019：[在 Red Hat Enterprise Server 中发布 IBM DB2 HADR](high-availability-guide-rhel-ibm-db2-luw.md)的新指南
- 06/13/2019：[与 sap 应用程序的 Azure NetApp 文件 Red Hat Enterprise Linux 上的 Sap NetWeaver 的高可用性](high-availability-guide-rhel-netapp-files.md)版本


