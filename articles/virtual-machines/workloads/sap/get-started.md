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
ms.date: 05/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b95112146c0003f3fc5ea563e5561dc26ea064e8
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800732"
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

- 哪些 SAP 软件版本和哪些操作系统版本支持哪些 Azure VM 和 HANA 大型实例单元。 请阅读文档 [Azure 部署支持的 SAP 软件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)，以获取答案和信息查找过程
- Azure VM 和 HANA 大型实例支持哪些 SAP 部署方案。 可在以下文档中找到有关受支持方案的信息：
    - [Azure 虚拟机上的 SAP 工作负荷支持的方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [HANA 大型实例支持的方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- 不同的 Azure 区域提供哪些 Azure 服务、Azure VM 类型和 Azure 存储服务，请查看站点[可用产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/) 

 
## <a name="sap-hana-on-azure-large-instances"></a>Azure 上的 SAP HANA（大型实例）

可通过阅读一系列文档来了解 Azure 上的 SAP HANA（大型实例），简称 HANA 大型实例。 有关 HANA 大型实例的信息，请先阅读文档 [Azure 上的 SAP HANA（大型实例）概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)，然后阅读 HANA 大型实例部分中的相关文档



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 虚拟机上的 SAP HANA
文档的此部分介绍 SAP HANA 的不同方面。 作为先决条件，应熟悉 Azure 的主体服务，它们提供 Azure IaaS 的基本服务。 因此，你需要了解 Azure 计算、存储和网络。 与 SAP NetWeaver 相关的 [Azure 规划指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)介绍了许多这类主题。 

有关 Azure 上的 HANA 的信息，请参阅以下文章及其子文章：

- [Azure 上的 SAP HANA 基础结构配置和操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure 虚拟机的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Azure 虚拟机上的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure 虚拟机上的 SAP HANA 备份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure 虚拟机上部署的 SAP NetWeaver
本部分列出了有关 Azure 上的 SAP NetWeaver 和 Business One 的规划和部署文档。 该文档重点介绍了 Azure 中 SAP 工作负荷下的非 HANA 数据库的基本知识和使用情况。 有关高可用性的文档和文章也是了解 Azure 中 HANA 高可用性的基础，例如：

- [Azure 规划指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)。 
- [Azure 虚拟机上的 SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [使用 Site Recovery 保护多层 SAP NetWeaver 应用程序部署](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [适用于 Azure 的 SAP LaMa 连接器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

有关 Azure 中 SAP 工作负荷下的非 HANA 数据库的信息，请参阅：

- [部署适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 的注意事项](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [适用于 SAP NetWeaver 的 SQL Server Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [适用于 SAP 工作负荷的 Oracle Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [适用于 SAP 工作负荷的 IBM DB2 Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Azure VM 上的 SAP MaxDB、Live Cache 和内容服务器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

有关 Azure 中的 SAP HANA 数据库的信息，请参阅“Azure 虚拟机上的 SAP HANA”部分。

有关 Azure 中 SAP 工作负荷的高可用性的信息，请参阅：

- [Azure 虚拟机上 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

此文档指向其他各种体系结构和方案文档。 后续方案文档提供了一些链接，指向介绍不同高可用性方法的部署和配置的详细技术文档。 介绍如何建立和配置 SAP NetWeaver 工作负荷高可用性的不同文档涵盖了 Linux 和 Windows 操作系统。


有关 Azure Active Directory (Azure AD) 与 SAP 服务之间的集成和单一登录的信息，请参阅：

- [教程：将 Azure Active Directory 与 SAP Cloud for Customer 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP Cloud Platform Identity Authentication 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP Cloud Platform 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP NetWeaver 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP Business ByDesign 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP HANA 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [S/4HANA 环境：通过 Azure AD 执行的 Fiori Launchpad SAML 单一登录](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

有关将 Azure 服务集成到 SAP 组件的信息，请参阅：

- [在 Power BI Desktop 中使用 SAP HANA](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 和 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [在 Power BI Desktop 中使用 SAP BW 连接器](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure 数据工厂提供 SAP HANA 和业务数据仓库数据集成](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>更改日志

- 2020/05/21：更改[在 Azure 中的 SLES 上设置 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) 和[在 Azure 中的 RHEL 上设置 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) 中的内容，以添加指向 [SAP HA 方案中使用 Azure 标准 ILB 的 VM 的公共终结点连接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)的链接  
- 2020/05/19：在 [SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中添加一条重要消息，指示在将 LVM 用于与 HANA 相关的卷时，不要使用根卷组
- 2020/05/19：在 [HANA 大型实例的兼容操作系统](https://docs.microsoft.com/
- azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance) 中，为 HANA 大型实例类型 II 添加新的受支持的 OS
- 2020/05/12：更改 [SAP HA 方案中使用 Azure 标准 ILB 的 VM 的公共终结点连接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)中的内容，以更新链接并添加有关第三方防火墙配置的信息
- 2020/05/11：更改 [SLES 上 Azure VM 中的 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)中的内容，以将 netcat 资源的资源粘性设置为 0，因为这样可以简化故障转移 
- 2020/05/05：更改[适用于 SAP NetWeaver 的 Azure 虚拟机规划和实施](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中的内容，以表明 Gen2 部署可用于 Mv1 VM 系列
- 2020/04/24：更改[在 SLES 上的 Azure VM 中通过 ANF 使用备用节点横向扩展 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)、[在 RHEL 上的 Azure VM 中通过 ANF 使用备用节点横向扩展 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)、[在 SLES 上的 Azure VM 中通过 ANF 实现 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)和[在 RHEL 上的 Azure VM 中通过 ANF 实现 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)中的内容，以添加系统自动为 ANF 卷分配 IP 地址的说明
- 2020/04/22：更改 [SLES 上 Azure VM 中的 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)中的内容，以从指令中删除元属性 `is-managed`，因为它与使群集进入或退出维护模式相冲突
- 2020/04/21：在文章 [Azure 部署支持的 SAP 软件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)和[在 Microsoft Azure 上运行的 SAP 认证和配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)中，添加了 SQL Azure DB 作为 SAP (Hybris) Commerce Platform 1811 和更高版本支持的 DBMS
- 2020/04/16：在文章 [Azure 部署支持的 SAP 软件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)和[在 Microsoft Azure 上运行的 SAP 认证和配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)中，添加了 SAP HANA 作为 SAP (Hybris) Commerce Platform 支持的 DBMS
- 2020/04/13：在[适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)中更正为确切的 SAP ASE 版本号
- 2020/04/07：更改[在 Azure 中的 SLES 上设置 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) 中的内容，以阐明 cloud-netconfig-azure 指令
- 2020/04/06：更改[在 SLES 上的 Azure VM 中通过 Azure NetApp 文件使用备用节点横向扩展 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) 和[在 RHEL 上的 Azure VM 中通过 Azure NetApp 文件使用备用节点横向扩展 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) 中的内容，以删除对 NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf)（由 [TR-4746](https://www.netapp.com/us/media/tr-4746.pdf) 代替）的引用
- 2020/03/31：更改 [SLES 上 Azure VM 中的 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)和 [RHEL 上 Azure VM 中的 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)中的内容，以添加有关如何在创建带区卷时指定条带大小的说明
- 2020/03/27：更改[在 SLES for SAP Applications 上的 Azure VM 中通过 ANF 实现 SAP NW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)中的内容，以根据 NetApp TR-4746 调整文件系统装载选项（删除同步装载选项）
- 2020/03/26：更改 [SLES 上 Azure VM 中的 SAP NetWeaver 的高可用性多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)中的内容，以添加对 NetApp TR-4746 的引用
- 2020/03/26：更改 [SLES for SAP Applications 上 Azure VM 中的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)、[在 SLES for SAP Applications 上的 Azure VM 中通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)、[SLES 上 Azure VM 中的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)、[SLES 上 Azure VM 中的 SAP NetWeaver 的高可用性多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)、[RHEL for SAP Applications 上 Azure VM 中的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)和[在 RHEL for SAP Applications 上的 Azure VM 中通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)中的内容，以更新图表并阐述有关 Azure 负载均衡器后端池创建的说明
- 2020/03/19：进行重大修订，将文档[快速入门：在 Azure 虚拟机上手动安装单实例 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started) 修改为[在 Azure 虚拟机上安装 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- 2020/03/17：更改[在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) 中的内容，以删除不再需要的 SBD 配置设置
- 2020/03/16：在 [Azure 部署支持的 SAP 软件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)中阐明 SAP HANA IaaS 认证平台中的列认证方案
- 2020/03/11：更改 [Azure 虚拟机上的 SAP 工作负荷支持的方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)中的内容，以阐明每个 DBMS 实例多个数据库支持
- 2020/03/11：更改[适用于 SAP NetWeaver 的 Azure 虚拟机规划和实施](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中的内容，以介绍第 1 代和第 2 代 VM
- 2020/03/10：更改 [SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中的内容，以阐明 ANF 的实际现有吞吐量限制
- 2020/03/09：更改 [SUSE Linux Enterprise Server for SAP Applications 上 Azure VM 中的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)、[在 SUSE Linux Enterprise Server for SAP Applications 上的 Azure VM 中通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)、[SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)、[在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)、[在 SUSE Linux Enterprise Server 上的 Azure VM 中通过 Pacemaker 实现 IBM Db2 LUW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)和 [SUSE Linux Enterprise Server 上 Azure VM 中的 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)和 [SLES 上 Azure VM 中的 SAP NetWeaver 的高可用性多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)中的内容，以使用资源代理 azure-lb 更新群集资源 
- 2020/03/05：对[适用于 SAP NetWeaver 的 Azure 虚拟机规划和实施](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中的 Azure 区域和 Azure 虚拟机进行结构更改和内容更改
- 2020/03/03：更改[在 SLES for SAP Applications 上的 Azure VM 中通过 ANF 实现 SAP NW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)中的内容，以更改为更有效的 ANF 卷布局
- 2020/03/01：改编了 [Azure 虚拟机上的 SAP HANA 备份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)，以包括 Azure 备份服务。 减少和压缩了[文件级别的 SAP HANA Azure 备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)中的内容，并删除了有关通过磁盘快照处理备份的第三个文档。 处理了《Azure 虚拟机上的 SAP HANA 备份指南》中的内容 
- 2020/02/27：更改 [SLES for SAP Applications 上 Azure VM 中的 SAP NW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)、[在 SLES for SAP Applications 上的 Azure VM 中通过 ANF 实现 SAP NW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)和 [SLES 上 Azure VM 中的 SAP NetWeaver 的高可用性多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)中的内容，以调整“on fail”群集参数
- 2020/02/26：更改 [SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中的内容，以阐明 Azure 上的 HANA 的文件系统选择
- 2020/02/26：更改[适用于 SAP 的高可用性体系结构和方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)中的内容，以包括指向《RHEL 上 Azure VM 中的 SAP NetWeaver 的 HA 多 SID 指南》的链接
- 2020/02/26：更改 [SLES for SAP Applications 上 Azure VM 中的 SAP NW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)、[在 SLES for SAP Applications 上的 Azure VM 中通过 ANF 实现 SAP NW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)、[RHEL 上 Azure VM 中的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)和[在 RHEL 上的 Azure VM 中通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)中的内容，以删除不支持多 SID ASCS/ERS 群集的声明
- 2020/02/26：发布 [RHEL 上 Azure VM 中的 SAP NetWeaver 的高可用性多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)，以添加指向 SUSE 多 SID 群集指南的链接
- 2020/02/25：更改[适用于 SAP 的高可用性体系结构和方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)中的内容，以添加指向较新 HA 文章的链接
- 2020/02/25：更改[在 SUSE Linux Enterprise Server 上的 Azure VM 中通过 Pacemaker 实现 IBM Db2 LUW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)中的内容，以指向介绍如何使用标准 Azure 负载均衡器访问公共终结点的文档
- 2020/02/21：完成[适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)一文的修订
- 2020/02/21：更改 [SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中的内容，以体现针对 /hana/data 的条带大小的新建议，并添加 I/O 计划程序的设置
- 2020/02/21：更改 HANA 大型实例文档中的内容，以体现新认证的 S224 和 S224m SKU
- 2020/02/21：更改 [RHEL 上 Azure VM 中的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)和[在 RHEL 上的 Azure VM 中通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)中的内容，以调整排队服务器复制 2 体系结构 (ENSA2) 的群集约束
- 2020/02/20：更改 [SLES 上 Azure VM 中的 SAP NetWeaver 的高可用性多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)，以添加指向 SUSE 多 SID 群集指南的链接
- 2020/02/13：更改[适用于 SAP NetWeaver 的 Azure 虚拟机规划和实施](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中的内容，以实现指向新文档的链接
- 2020/02/13：添加了新文档 [Azure 虚拟机上的 SAP 工作负荷支持的方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
- 2020/02/13：添加了新文档 [Azure 部署支持的 SAP 软件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 2020/02/13：更改 [Red Hat Enterprise Linux Server 上 Azure VM 中的 IBM Db2 LUW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)中的内容，以指向介绍如何使用标准 Azure 负载均衡器访问公共终结点的文档
- 2020/02/13：向[在 Microsoft Azure 上运行的 SAP 认证和配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)添加新的 VM 类型。
- 2020/02/13：添加新的 SAP 支持说明 [Azure 上的 SAP 工作负荷：规划和部署清单](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 2020/02/13：更改 [RHEL 上 Azure VM 中的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)和[在 RHEL 上的 Azure VM 中通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)中的内容，以根据 Red Hat 超时建议调整群集资源超时
- 2020/02/11：发布[将 Azure 大型实例上的 SAP HANA 迁移到 Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 2020/02/07：更改 [SAP HA 方案中使用 Azure 标准 ILB 的 VM 的公共终结点连接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)中的内容，以更新示例 NSG 屏幕截图
- 2020/02/03：更改 [SLES for SAP Applications 上 Azure VM 中的 SAP NW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)和[在 SLES for SAP Applications 上的 Azure VM 中通过 ANF 实现 SAP NW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)中的内容，以删除有关在 SLES 上群集节点的主机名中使用破折号的警告
- 2020/01/28：更改 [RHEL 上 Azure VM 中的 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)中的内容，以根据 Red Hat 超时建议调整 SAP HANA 群集资源超时
- 2020/01/17：更改[用于最大程度地降低 SAP 应用程序网络延迟的 Azure 邻近放置组](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)中的内容，以更改将现有 VM 移入邻近放置组的部分
- 2020/01/17：更改[使用 Azure 可用性区域的 SAP 工作负荷配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)中的内容，以指向自动测量可用性区域之间的延迟的过程
- 2020/01/16：更改[如何在 Azure 上安装和配置 SAP HANA（大型实例）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)中的内容，以根据 HANA IaaS 硬件目录调整 OS 版本
- 2020/01/16：更改 [SLES 上 Azure VM 中的 SAP NetWeaver 的高可用性多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)中的内容，以添加使用排队服务器 2 体系结构 (ENSA2) 的 SAP 系统的相关说明
- 2020/01/10：更改[在 SLES 上的 Azure VM 中通过 Azure NetApp 文件使用备用节点横向扩展 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) 和[在 RHEL 上的 Azure VM 中通过 Azure NetApp 文件使用备用节点横向扩展 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) 中的内容，以添加有关如何使 `nfs4_disable_idmapping` 更改永久生效的说明。
- 2020/01/10：更改[在 SLES for SAP Applications 上的 Azure VM 中通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)和[在 RHEL for SAP Applications 上的 Azure 虚拟机中通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)中的内容，以添加有关如何装载 Azure NetApp 文件 NFSv4 卷的说明。
- 2019 年 12 月 23 日：发布 [SLES 上 Azure VM 中的 SAP NetWeaver 的高可用性多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 2019 年 12 月 18 日：发布[在 RHEL 上的 Azure VM 中通过 Azure NetApp 文件使用备用节点横向扩展 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 2019 年 11 月 21 日：更改[在 SUSE Linux Enterprise Server 上的 Azure VM 中通过 Azure NetApp 文件使用备用节点横向扩展 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) 中的内容，以简化 NFS ID 映射的配置，并更改建议的主网络接口以简化路由。
- 2019 年 11 月 15 日：对[在 SUSE Linux Enterprise Server for SAP Applications 上通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](high-availability-guide-suse-netapp-files.md)和[在 Red Hat Enterprise Linux for SAP Applications 上通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](high-availability-guide-rhel-netapp-files.md)中的内容进行一些小改动，以阐明容量池大小限制并删除仅支持 NFSv3 版本的声明。
- 2019 年 11 月 12 日：发布[在 Windows 上通过 Azure NetApp 文件 (SMB) 实现 SAP NetWeaver 的高可用性](high-availability-guide-windows-netapp-files-smb.md)
- 2019 年 11 月 8 日：更改 [SUSE Linux Enterprise Server 上 Azure VM 中的 SAP HANA 的高可用性](sap-hana-high-availability.md)、[在 Azure 虚拟机 (VM) 上设置 SAP HANA 系统复制](sap-hana-high-availability-rhel.md)、[SUSE Linux Enterprise Server for SAP Applications 上 Azure 虚拟机中的 SAP NetWeaver 的高可用性](high-availability-guide-suse.md)、[在 SUSE Linux Enterprise Server 上的 Azure 虚拟机中通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](high-availability-guide-suse-netapp-files.md)、[Red Hat Enterprise Linux 上 Azure 虚拟机中的 SAP NetWeaver 的高可用性](high-availability-guide-rhel.md)、[在 Red Hat Enterprise Linux 上的 Azure 虚拟机中通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](high-availability-guide-rhel-netapp-files.md)、[SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性](high-availability-guide-suse-nfs.md)和[适用于 SAP NetWeaver 的 Red Hat Enterprise Linux 上的 Azure VM 中的 GlusterFS](high-availability-guide-rhel-glusterfs.md) 中的内容，以推荐 Azure 标准负载均衡器  
- 2019 年 11 月 8 日：更改 [SAP 工作负荷规划和部署清单](sap-deployment-checklist.md)中的内容，以阐明加密建议  
- 2019 年 11 月 4 日：更改[在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker](high-availability-guide-suse-pacemaker.md) 中的内容，以直接使用单播配置创建群集  


