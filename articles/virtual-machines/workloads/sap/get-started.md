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
ms.date: 05/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eab9db77dee5420ddc5baa9f71bde98fc46ca3f6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196845"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>使用 Azure 托管和运行 SAP 工作负荷方案

当你使用 Microsoft Azure 时，你可以在可扩展、相容和企业验证的平台上可靠地运行任务关键型 SAP 工作负荷和方案。 你可以获得 Azure 的可伸缩性、灵活性和成本节约。 通过 Microsoft 与 SAP 之间的扩展合作关系，你可以在 Azure 中的开发和测试和生产方案中运行 SAP 应用程序，并完全受支持。 从 SAP NetWeaver 到 SAP S/4HANA、在 Linux 上的 SAP BI 到 Windows 以及 SAP HANA 到 SQL，我们都能满足你的需求。

除了在 Azure 上托管不同 DBMS 的 SAP NetWeaver 方案，你还可以托管其他 SAP 工作负荷方案，如 Azure 上的 SAP BI。 

Azure for SAP HANA 的唯一性是将 Azure 分开的一个产品/服务。 为了能够承载涉及 SAP HANA 的更多内存和 CPU 资源要求苛刻的 SAP 方案，Azure 提供了客户专用的裸机硬件的使用。 使用此解决方案来运行 SAP HANA 的部署，这些部署需要多达 24 120 TB 的内存用于 S/4HANA 或其他 SAP HANA 工作负荷。 

在 Azure 中托管 SAP 工作负荷方案还可以创建标识集成和单一登录的要求。 当你使用 Azure Active Directory （Azure AD）来连接不同的 SAP 组件和 SAP 软件即服务（SaaS）或平台即服务（PaaS）产品/服务（PaaS）产品/服务时，可能会出现这种情况。 "AAD SAP 标识集成和单一登录" 一节中介绍并记录了包含 Azure AD 和 SAP 实体的此类集成和单一登录方案的列表。

## <a name="changes-to-the-sap-workload-section"></a>SAP 工作负荷部分的更改
本文末尾列出了 "Azure 上的 SAP 工作负荷" 部分中文档的更改。 更改日志中的条目保存时间大约为180天。

## <a name="you-want-to-know"></a>你想知道
如果有特定的问题，我们将指向 "开始" 页的此部分中的特定文档或流。 您想知道：

- 支持哪些 Azure Vm 和 HANA 大型实例单元的 SAP 软件版本以及哪些操作系统版本。 阅读本文档，了解[Azure 部署支持的 SAP 软件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)的答案和查找信息的过程
- Azure Vm 和 HANA 大型实例支持的 SAP 部署方案。 可在以下文档中找到有关支持的方案的信息：
    - [Azure 虚拟机支持的方案中的 SAP 工作负荷](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [HANA 大型实例支持的方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- Azure 服务、Azure VM 类型和 Azure 存储服务在不同的 Azure 区域中提供，查看[区域提供](https://azure.microsoft.com/global-infrastructure/services/)的站点产品 

 
## <a name="sap-hana-on-azure-large-instances"></a>Azure 上的 SAP HANA（大型实例）

一系列文档将引导你完成 Azure （大型实例）上的 SAP HANA，或用于简短的 HANA 大型实例。 有关 HANA 大型实例的信息，请参阅[Azure 上的 SAP HANA 文档概述和体系结构（大型实例）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) ，并浏览 HANA 大型实例部分的相关文档



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 虚拟机上的 SAP HANA
文档的此部分介绍 SAP HANA 的不同方面。 作为先决条件，你应该熟悉 Azure 的主要服务，提供 Azure IaaS 的基本服务。 因此，你需要了解 Azure 计算、存储和网络。 其中的许多主题在与 SAP NetWeaver 相关的[Azure 规划指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中进行了处理。 

有关 Azure 上的 HANA 的信息，请参阅以下文章及其 subarticles：

- [Azure 上的 SAP HANA 基础结构配置和操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure 虚拟机的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Azure 虚拟机上的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure 虚拟机上 SAP HANA 的备份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure 虚拟机上部署的 SAP NetWeaver
本部分列出了 Azure 上的 SAP NetWeaver 和企业版的规划和部署文档。 本文档重点介绍 Azure 上的 SAP 工作负荷的基本知识和使用情况。 高可用性的文档和文章也是 Azure 中 HANA 高可用性的基础，例如：

- [Azure 规划指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)。 
- [Azure 虚拟机上的 SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
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

- 05/11/2020：在[SLES 上的 Azure vm 上更改 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)，将 netcat 资源的资源粘性设置为0，这将导致更精简的故障转移 
- 05/05/2020： [SAP NetWeaver 的 Azure 虚拟机规划和实施](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中的更改，用于表示 Gen2 部署可用于 Mv1 VM 系列
- 04/24/2020：在[Azure vm 上具有和 ON SLES 的备用节点 SAP HANA 向外扩展的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)更改，请参阅 SAP HANA 在 azure vm 上通过[和 on rhel 向外扩展，其中包含 on rhel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)的 azure vm 上的和高可用性，以及在 rhel 上通过 AZURE Vm 上的[sap NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)实现[高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- 04/22/2020：在[SLES 上的 Azure vm 上更改 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) `is-managed` ，以从说明中删除元属性，因为它与将群集置于或退出维护模式冲突
- 04/21/2020：已将 SQL Azure DB 添加为适用于 SAP 的 DBMS （Hybris） Commerce 平台1811和更高版本，本文介绍[了 Azure 部署支持的 sap 软件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)以及[在 Microsoft Azure 上运行的 sap 认证和配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 04/16/2020：在文章中添加了 SAP HANA 作为 SAP （Hybris）商务平台的受支持 DBMS，其中包含了[Azure 部署支持的 sap 软件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)以及[在 Microsoft Azure 上运行的 sap 认证和配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 04/13/2020：更正 sap [Ase Azure 虚拟机 DBMS 部署中适用于 sap 工作负荷的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)确切 sap ase 发行版编号
- 04/07/2020：在[Azure 中的 SLES 上设置 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)以阐明 netconfig-azure 说明
- 04/06/2020：在 Azure Vm 上使用具有[备用节点的 Azure vm 上的 Azure Netapp 文件 SAP HANA 扩展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)中的更改，并[SAP HANA 在 azure vm](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)上使用 AZURE netapp 文件在 RHEL 上使用备用节点扩展[4746](https://www.netapp.com/us/media/tr-4746.pdf) [4435](https://www.netapp.com/us/media/tr-4746.pdf)到
- 03/31/2020：在 RHEL 上的 azure vm 上的[SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)和在[RHEL 上的 azure vm 上 SAP HANA 的高](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)可用性方面进行更改，以添加在创建带区卷时如何指定条带大小的说明
- 03/27/2020：对[和 FOR sap 应用程序的 Azure vm 上的 SAP NW 的高可用性进行了](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)更改，以将文件系统装载选项与 NetApp TR-4746 （删除同步装载选项）进行对齐
- 03/26/2020：在[SLES 多 SID 指南上4746，Azure vm 上 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)更改
- 03/26/2020：在 SLES 上针对 sap[应用程序的 Azure vm 上的 Sap NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)发生变化，适用于[sap 应用程序的 azure NetApp 文件的 azure VM 上的 sap NetWeaver 提供高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)，Azure Vm 上的[azure vm 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)，在[sles 多 SID 指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)上，azure vm 上的 sap NetWeaver 高可用性，在 Rhel 上，azure vm 上的 sap [NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)，适用于 sap 应用程序的 sap 应用程序和[高可用性 azure vm](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
- 03/19/2020：文档快速入门的主要修订版[：在 Azure 虚拟机上手动安装单实例 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started) ，以[在 azure 虚拟机上安装 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- 03/17/2020：更改在[Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) ，以删除不再需要的 SBD 配置设置
- 03/16/2020：在[Azure 部署支持的 SAP 软件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)的 SAP HANA IaaS 认证平台中阐明列认证方案
- 03/11/2020：在[Azure 虚拟机支持的方案中更改 SAP 工作负荷](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)，以澄清每个 DBMS 实例支持的多个数据库
- 03/11/2020：[针对 SAP NetWeaver 的 Azure 虚拟机规划和实施中的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)更改说明第1代和第2代 vm
- 03/10/2020：在[SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中进行更改，以明确和的实际吞吐量限制
- 03/09/2020：在 sap[应用程序 SUSE Linux Enterprise Server 上更改 Sap NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)，azure vm 上的 Sap [SUSE Linux Enterprise Server NetWeaver 的高可用性，适用于 sap 应用程序的 azure NetApp 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)， [SUSE Linux Enterprise Server 上的 azure vm 上的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)，在 azure 上的[SUSE Linux Enterprise Server 上设置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)PACEMAKER，AZURE vm 上的 IBM [Db2 LUW 具有 Pacemaker、高](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)可用性 SAP HANA，azure vm 上的 azure vm 上的高可用性，高可用性[azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) [vm 上的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)azure vm SUSE Linux Enterprise Server SUSE Linux Enterprise Server 
- 03/05/2020：为[SAP NetWeaver 的 Azure 虚拟机规划和实施](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中的 azure 区域和 azure 虚拟机构造更改和内容更改
- 03/03/2020：对[和 FOR sap 应用程序的 Azure vm 上的 SAP NW 的高可用性进行了](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)更改，使其更高效和卷布局
- 03/01/2020：[有关 Azure 虚拟机上 SAP HANA 的改编备份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)，包括 azure 备份服务。 [SAP HANA 在文件级别上的 Azure 备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)中缩减和紧缩的内容，并删除了通过磁盘快照处理备份的第三篇文档。 在 Azure 虚拟机上 SAP HANA 的备份指南中处理内容 
- 02/27/2020：在 sles [FOR sap 应用程序](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)上，azure vm 上的 sap Nw 的高可用性，在 sles 上的 azure vm 上，sap nw 的高可用性，适用于[和 for sap 应用程序](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)的高可用性和[Azure vm](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)上的 sap NetWeaver 的高可用性。
- 02/26/2020：在[SAP HANA azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中进行更改，以便在 azure 上阐明 HANA 的文件系统选择
- 02/26/2020：在 RHEL 多 SID 指南上，在[高可用性体系结构和方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)中更改 sap 以包括 Azure vm 上的 HA For SAP NetWeaver 的链接
- 02/26/2020：在 SLES 上针对 sap[应用程序的 Azure 虚拟机上的 SAP nw](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)实现高可用性，在适用[于 sap 应用程序的和](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)上，azure VM 上的 sap nw 高可用性，适用于 sap 应用程序的 azure vm 的高可用性，Rhel 上的 Sap [NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)和 Azure Vm 的高可用性 azure [NETAPP 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)用于删除不支持多 SID ASCS/ERS 群集的语句
- 02/26/2020：在[RHEL 多 sid 指南上，Azure vm 上 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)发布，添加指向 SUSE 多 sid 群集指南的链接
- 02/25/2020：在[高可用性体系结构和方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)中更改 SAP 以添加到较新的 HA 文章的链接
- 02/25/2020：在[使用 Pacemaker 的 SUSE Linux Enterprise Server 上的 Azure vm 上更改 IBM DB2 LUW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)，以指向介绍使用标准 Azure 负载均衡器访问公用终结点的文档
- 02/21/2020：全面介绍 sap [ASE Azure 虚拟机 DBMS 部署的 sap 工作负荷](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 02/21/2020：在[SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中更改以表示/hana/data 的条带大小的新建议以及添加 i/o 计划程序的设置
- 02/21/2020： HANA 大型实例文档中的更改，用于表示 S224 和 S224m 的新认证 Sku
- 02/21/2020：在[Azure vm 中更改 Sap NetWeaver ON rhel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)和 azure vm 高可用性 azure vm 的高可用性 Azure [NETAPP 文件-RHEL 上的 sap NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
- 02/20/2020：在[SLES 多 sid 指南上，Azure vm 上 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)变化，添加指向 SUSE 多 sid 群集指南的链接
- 02/13/2020：更改[SAP NetWeaver 的 Azure 虚拟机计划和实施](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)，实现新文档的链接
- 02/13/2020：[在 Azure 虚拟机支持的方案中添加了新的文档 SAP 工作负荷](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
- 02/13/2020：添加了新文档[Azure 部署支持的 SAP 软件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 02/13/2020：在[Red Hat Enterprise Linux 服务器上的 Azure vm 上更改 IBM DB2 LUW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)，以指向介绍使用标准 Azure 负载均衡器访问公用终结点的文档
- 02/13/2020：将新的 VM 类型添加到[Microsoft Azure 上运行的 SAP 认证和配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 02/13/2020：添加新的 SAP 支持说明[Azure 上的 sap 工作负荷：规划和部署清单](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 02/13/2020：在[rhel 上更改 Azure vm 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)和 azure vm 上的 sap NetWeaver 高可用性 Azure [NetApp 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)，用于将群集资源超时与 Red Hat 超时建议相匹配
- 02/11/2020：发布[Azure 大型实例上的 SAP HANA 迁移到 Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 02/07/2020：[在 SAP HA 方案中使用 Azure 标准 ILB 更改 vm 的公共终结点连接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)，以更新示例 NSG 屏幕快照
- 02/03/2020：在 sles 上针对 sap 应用程序的[Azure vm 上的 SAP nw 进行高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)更改，并在[SLES 上使用和 for sap 应用程序的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)与 sap nw 的高可用性一起消除有关在 sles 上的群集节点的主机名中使用破折号的警告
- 01/28/2020：在[RHEL 上的 Azure vm 上更改 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)，以将 SAP HANA 群集资源超时调整为 Red Hat 超时建议
- 01/17/2020：更改[Azure 邻近位置组以获得最佳网络延迟，以便 SAP 应用程序](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)更改将现有 vm 移到邻近位置组的部分
- 01/17/2020：在[SAP 工作负荷配置中更改，并提供 Azure 可用性区域](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)以指向在可用性区域之间自动测定延迟的过程
- 01/16/2020：更改[如何在 Azure 上安装和配置 SAP HANA （大型实例）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) ，以将操作系统版本调整为 HANA IaaS 硬件目录
- 01/16/2020： [SLES 多 SID 指南中的 Azure vm 上 Sap NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)变化，可使用排队 server 2 体系结构（ENSA2）添加有关 SAP 系统的说明
- 01/10/2020：在 Azure Vm 上的 Azure 虚拟机上，通过 azure[虚拟机上的备用节点 SAP HANA 扩展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)，并 SAP HANA 在 azure vm 上包含 Azure netapp 文件，并在 azure vm 中添加具有[备用节点](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)的备用节点 `nfs4_disable_idmapping` 。
- 01/10/2020：在 SLES 上，azure Vm 上的[Sap NetWeaver 的高可用性更改与适用于 sap 应用程序的 Azure Netapp 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)以及[azure 虚拟机中 sap NetWeaver 的高可用性，适用于 sap 应用程序的 azure netapp 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)，用于添加有关如何装载 azure netapp 文件 NFSv4 卷的说明。
- 12/23/2019：[在 SLES 多 SID 指南上，Azure vm 上 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)发布
- 12/18/2019：在[RHEL 上通过 Azure NetApp 文件在 Azure vm 上通过备用节点 SAP HANA 扩展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)的发布
- 11/21/2019：在[Azure vm 上使用备用节点 SAP HANA 扩展，并在 SUSE Linux Enterprise Server 上使用 Azure NetApp 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)来简化 NFS ID 映射配置，并更改建议的主网络接口以简化路由。
- 11/15/2019：与 sap 应用程序的 azure [Netapp 文件一起 SUSE Linux Enterprise Server 上的 sap 应用程序的高可用性](high-availability-guide-suse-netapp-files.md)和 Sap [Red Hat Enterprise Linux NetWeaver 的高可用性](high-availability-guide-rhel-netapp-files.md)（sap 应用程序的 azure Netapp 文件）的高可用性的高可用性变化，用于阐明容量池大小限制并删除仅支持 NFSv3 版本的语句。
- 11/12/2019：[在 Windows 上发布 SAP NetWeaver On Azure NetApp 文件（SMB）的高可用性](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019： SUSE Linux Enterprise Server 上[的 Azure vm 上 SAP HANA 的高可用性](sap-hana-high-availability.md)变化，[在 azure 虚拟机（vm）上设置 SAP HANA 系统复制](sap-hana-high-availability-rhel.md)， [Sap NetWeaver 的 azure 虚拟机高可用性，适用于 sap 应用程序的 SUSE Linux Enterprise Server](high-availability-guide-suse.md)AZURE[虚拟机上的 Sap NetWeaver 高可用性 azure netapp 文件，](high-availability-guide-suse-netapp-files.md)azure 虚拟机高可用性-Red Hat Enterprise Linux[上的 sap NetWeaver，azure 虚拟](high-availability-guide-rhel.md)机高可用性，适用于 azure netapp 文件，azure vm 上的 azure 虚拟机高[可用性 SUSE Linux Enterprise Server](high-availability-guide-rhel-netapp-files.md)SUSE Linux Enterprise Server， [GlusterFS](high-availability-guide-suse-nfs.md)上[的 azure vm](high-availability-guide-rhel-glusterfs.md)上的 azure vm，建议 azure 标准负载均衡器 Red Hat Enterprise Linux Red Hat Enterprise Linux  
- 11/08/2019： [SAP 工作负荷规划和部署核对清单](sap-deployment-checklist.md)中的更改阐明了加密建议  
- 11/04/2019：在 Azure 中的[SUSE Linux Enterprise Server 上设置 Pacemaker](high-availability-guide-suse-pacemaker.md) ，以直接通过单播配置创建群集  


