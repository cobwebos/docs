---
title: 开始在 Azure VM 上使用 SAP |微软文档
description: 了解在 Microsoft Azure 中的虚拟机 （VM） 上运行的 SAP 解决方案
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
ms.date: 04/06/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: df7461443fc9e89209545e8a2dfb9ad0addf3f3e
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757149"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>使用 Azure 托管和运行 SAP 工作负载方案

使用 Microsoft Azure 时，可以在可扩展、合规且经过企业验证的平台上可靠地运行任务关键型 SAP 工作负载和方案。 您可以获得 Azure 的可伸缩性、灵活性和成本节约。 通过 Microsoft 和 SAP 之间扩展的伙伴关系，您可以在 Azure 中跨开发、测试和生产方案运行 SAP 应用程序，并得到充分支持。 从 SAP NetWeaver 到 SAP S/4HANA，从 Linux 上的 SAP BI 到 Windows，从 SAP HANA 到 SQL，我们为您提供服务。

除了在 Azure 上托管具有不同 DBMS 的 SAP NetWeaver 方案外，您还可以托管其他 SAP 工作负载方案，如 Azure 上的 SAP BI。 

SAP HANA Azure 的独特性是使 Azure 与众不同的一个产品/ 为了启用托管更多涉及 SAP HANA 的内存和 CPU 资源要求苛刻的 SAP 方案，Azure 提供了客户专用裸机硬件的使用。 使用此解决方案可运行 SAP HANA 部署，这些部署需要高达 24 TB（120 TB 扩展）内存才能用于 S/4HANA 或其他 SAP HANA 工作负载。 

在 Azure 中托管 SAP 工作负载方案还可以创建标识集成和单一登录的要求。 当您使用 Azure 活动目录 （Azure AD） 连接不同的 SAP 组件和 SAP 软件即服务 （SaaS） 或平台即服务 （PaaS） 产品时，可能会出现这种情况。 与 Azure AD 和 SAP 实体的此类集成和单一登录方案的列表在"AAD SAP 标识集成和单一登录"一节中描述和记录。

## <a name="changes-to-the-sap-workload-section"></a>对 SAP 工作负荷部分的更改
本文末尾列出了对 Azure 工作负荷上的 SAP 中文档的更改。 更改日志中的条目将保留大约 180 天。

## <a name="you-want-to-know"></a>你想知道
如果您有具体问题，我们将在起始页的这一部分中指向特定文档或流。 您想知道：

- 支持哪些 SAP 软件版本和哪些操作系统版本发布的 Azure VM 和 HANA 大型实例单元。 阅读文档[Azure 部署支持哪些 SAP 软件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)以查找答案以及查找信息的过程
- Azure VM 和 HANA 大型实例支持哪些 SAP 部署方案。 有关支持的方案的信息，请参阅文档：
    - [Azure 虚拟机支持的方案上的 SAP 工作负载](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [HANA 大型实例受支持的方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- 哪些 Azure 服务、Azure VM 类型和 Azure 存储服务在不同的 Azure 区域中可用，请[按区域](https://azure.microsoft.com/global-infrastructure/services/)检查站点产品 

 
## <a name="sap-hana-on-azure-large-instances"></a>Azure 上的 SAP HANA（大型实例）

一系列文档引导您通过 Azure（大型实例）上的 SAP HANA，或者对于简短的 HANA 大型实例。 有关 HANA 大型实例的信息，从[Azure（大型实例）上的 SAP HANA 文档概述和体系结构开始，](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)然后浏览 HANA 大型实例部分中的相关文档



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 虚拟机上的 SAP HANA
文档的此部分介绍 SAP HANA 的不同方面。 作为先决条件，您应该熟悉 Azure 提供 Azure IaaS 基本服务的主要服务。 因此，您需要了解 Azure 计算、存储和网络。 其中许多主题在 SAP NetWeaver 相关的 Azure[规划指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中处理。 

有关 Azure 上的 HANA 的信息，请参阅以下文章及其子文章：

- [Azure 上的 SAP HANA 基础结构配置和操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure 虚拟机的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Azure 虚拟机上的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure 虚拟机上的 SAP HANA 备份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>部署在 Azure 虚拟机上的 SAP NetWeaver
本节列出了 AZURE 上的 SAP NetWeaver 和业务一体的规划和部署文档。 本文档重点介绍 Azure 上具有 SAP 工作负荷的非 HANA 数据库的基础知识和使用。 高可用性的文档和文章也是 Azure 中 HANA 高可用性的基础，例如：

- [Azure 规划指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)。 
- [Azure 虚拟机上的 SAP 业务一号](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [使用站点恢复保护多层 SAP NetWeaver 应用程序部署](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [适用于 Azure 的 SAP LaMa 连接器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

有关 Azure 上的 SAP 工作负荷下的非 HANA 数据库的信息，请参阅：

- [部署适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 的注意事项](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [适用于 SAP NetWeaver 的 SQL Server Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [适用于 SAP 工作负荷的 Oracle Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [适用于 SAP 工作负荷的 IBM DB2 Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [在 Azure VM 上部署 SAP MaxDB、实时缓存和内容服务器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

有关 Azure 上的 SAP HANA 数据库的信息，请参阅"Azure 虚拟机上的 SAP HANA"一节。

有关 Azure 上 SAP 工作负载的高可用性的信息，请参阅：

- [Azure 虚拟机上 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

本文档指向各种其他体系结构和方案文档。 在以后的方案文档中，提供了指向详细技术文档的链接，这些文档解释了不同高可用性方法的部署和配置。 演示如何为 SAP NetWeaver 工作负载建立和配置高可用性的不同文档涵盖 Linux 和 Windows 操作系统。


有关 Azure 活动目录 （Azure AD） 和 SAP 服务和单一登录之间的集成的信息，请参阅：

- [教程：将 Azure Active Directory 与 SAP Cloud for Customer 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP Cloud Platform Identity Authentication 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP Cloud Platform 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP NetWeaver 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP Business ByDesign 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP HANA 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [您的 S/4HANA 环境：Fiori 启动板 SAML 单一登录 Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

有关将 Azure 服务集成到 SAP 组件的信息，请参阅：

- [在 Power BI Desktop 中使用 SAP HANA](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 和 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [在 Power BI Desktop 中使用 SAP BW 连接器](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure 数据工厂提供 SAP HANA 和业务数据仓库数据集成](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>更改日志
- 04/06/2020： [SAP HANA 横向扩展与 Azure VM 上的备用节点与 Azure NetApp 文件在 SLES 上](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)和 SAP [HANA 横向扩展与 Azure VM 上的备用节点与 AZURE NetApp 文件在 RHEL 上](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)删除对 NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf)的引用 （替换为[TR-4746）](https://www.netapp.com/us/media/tr-4746.pdf)
- 03/31/2020：在[SLES 上的 Azure VM 上更改 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)，在[RHEL 上的 Azure VM 上更改 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)，以添加在创建条带卷时如何指定条带大小的说明
- 2020 年 3 月 27 日：[在 SLES 上的 Azure VM 上 SAP NW 的高可用性变化与 SAP 应用程序进行 ANF，](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)以便将文件系统装载选项与 NetApp TR-4746 对齐（删除同步安装选项）
- 2020/03/26：在[SLES 多 SID 指南上的 Azure VM 上 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)变化，以添加对 NetApp TR-4746 的引用
- 2020 年 3 月 26 日[：SAP 应用程序在 SLES 上的 Azure VM 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)变化[、SLES 上的 Azure VM 上的 SAP NetWeaver 的高可用性以及用于 SAP 应用程序的 Azure NetApp 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)[、SLES 上 Azure VM 上的 NFS 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)[、SAP Net 的高可用性 SLES 多 SID 指南上的 Azure VM 上的 Weaver、SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)应用程序[RHEL 上 Azure VM 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)以及[RHEL 上的 Azure VM 上的 SAP NetWeaver 的高可用性，以及用于 SAP 应用程序的 Azure NetApp 文件以](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)更新图表并阐明 Azure 负载均衡器后端池创建说明
- 03/19/2020：文档[快速入门的主要修订：在 Azure 虚拟机上手动安装单实例 SAP HANA，](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)以[在 Azure 虚拟机上安装 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- 03/17/2020：在[Azure 中的 SUSE Linux 企业服务器上设置起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)以删除不再需要的 SBD 配置设置
- 03/16/2020：在[Azure 部署支持哪些 SAP 软件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)的 SAP HANA IaaS 认证平台中澄清列认证方案
- 03/11/2020：Azure[虚拟机支持的方案中 SAP 工作负载](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)的更改，以澄清每个 DBMS 实例支持的多个数据库
- 03/11/2020：SAP NetWeaver 解释第 1 代和第 2 代 VM 的[Azure 虚拟机规划和实现](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)的变化
- 03/10/2020：更改[SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)，以明确 ANF 的实际现有吞吐量限制
- 03/09/2020：SAP [NetWeaver 在 SUSE Linux 企业服务器上的 Azure VM 上为 SAP 应用程序](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)而高[可用性的变化，SUSE Linux 企业服务器上的 Azure VM 上的 SAP NetWeaver 的高可用性，以及用于 SAP 应用程序的 Azure NetApp 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)，在[SUSE Linux 企业服务器上的 Azure VM 上的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)、[设置步调 在 Azure 中的 SUSE Linux 企业服务器上](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)[，IBM Db2 LUW 在 SUSE Linux 企业服务器上的 Azure VM 上高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)，[在 SUSE Linux 企业服务器上的 Azure VM 上高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)，以及[SLES 多 SID 指南上的 SAP NetWeaver 在 Azure VM 上的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)，以便使用资源代理 azure-lb 更新群集资源 
- 03/05/2020：Azure[虚拟机规划和实现的 AZURE NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中的 Azure 区域和 Azure 虚拟机的结构更改和内容更改
- 03/03/2020：SLES[上的 Azure VM 上的 SAP NW 高可用性变化，ANF 使 SAP 应用程序](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)更改为更高效的 ANF 卷布局
- 03/01/2020：Azure[虚拟机上的 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)返工备份指南，以包括 Azure 备份服务。 在文件级别减少和压缩[SAP HANA Azure 备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)中的内容，并删除了处理通过磁盘快照进行备份的第三个文档。 内容在 Azure 虚拟机上的 SAP HANA 备份指南中处理 
- 2020 年 2 月 27 日[：SAP 应用程序在 SLES 上的 Azure VM 上的 SAP NW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)变化[、SLES 上的 Azure VM 上 SAP NW 的高可用性以及 SAP 应用程序的 ANF](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)和[SLES 多 SID 指南上的 Azure VM 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)，以调整"故障"群集参数
- 02/26/2020：更改[SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)，以阐明 Azure 上 HANA 的文件系统选择
- 02/26/2020：SAP[的高可用性体系结构和方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)的变化，以包括在 RHEL 多 SID 指南上的 Azure VM 上的 SAP NetWeaver 的 HA 链接
- 2020 年 2 月 26 日[：SAP 应用程序在 SLES 上的 Azure VM 上的 SAP NW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)变化[、SLES 上 Azure VM 上的 SAP NW 高可用性以及 SAP 应用程序的 ANF、RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)[上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)以及[RHEL 上 SAP NetWeaver 的 Azure NetWeaver 高可用性以及 Azure NetApp 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)，以删除不支持多 SID ASCS/ERS 群集的声明
- 02/26/2020：在[RHEL 多 SID 指南上的 Azure VM 上发布 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)版本，以添加指向 SUSE 多 SID 群集指南的链接
- 02/25/2020：[更改高可用性体系结构和方案，以便 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)添加指向较新的 HA 文章的链接
- 02/25/2020：IBM [Db2 LUW 在 SUSE Linux 企业服务器上的 Azure VM 上的高可用性变化，使用起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)指向描述使用标准 Azure 负载均衡器访问公共终结点的文档
- 02/21/2020：完成对文章[SAP ASE Azure 虚拟机 DBMS 部署的 SAP 工作负荷](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)的完整修订
- 02/21/2020：更改[SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)，以表示 /hana/数据条带大小的新建议，并添加 I/O 计划程序的设置
- 02/21/2020：HANA 大型实例文档的更改，以表示新认证的 S224 和 S224m SKU
- 2020 年 2 月 21 日：使用 Azure NetApp 文件[在 RHEL 上更改 SAP NetWeaver 的 AZURE NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)和[Azure VM 的高可用性，用于使用 Azure NetApp 文件在 RHEL 上进行 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)，以调整排队服务器复制 2 体系结构 （ENSA2） 的群集约束
- 02/20/2020：在[SLES 多 SID 指南上的 Azure VM 上 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)变化，以添加指向 SUSE 多 SID 群集指南的链接
- 02/13/2020：对[Azure 虚拟机的更改规划和实现，以便 SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)实施指向新文档的链接
- 02/13/2020：[在 Azure 虚拟机支持的方案上](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)添加了新的文档 SAP 工作负载
- 02/13/2020： 添加了新文档[Azure 部署支持哪些 SAP 软件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 02/13/2020： [IBM Db2 LUW 在红帽企业 Linux 服务器上的 Azure VM 上的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)变化，以指向描述使用标准 Azure 负载均衡器访问公共终结点的文档
- 02/13/2020：将新的 VM 类型添加到[在 Microsoft Azure 上运行的 SAP 认证和配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 02/13/2020： 添加新的 SAP 支持说明[Azure 上的 SAP 工作负载：规划和部署检查表](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 02/13/2020： Azure VM 中的更改在[RHEL 上对 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)，Azure VM 在[RHEL 上具有 AZURE NetApp 文件，使](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)群集资源超时与红帽超时建议保持一致
- 02/11/2020：在[Azure 大型实例迁移到 Azure 虚拟机时发布 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 02/07/2020：在[SAP HA 方案中使用 Azure 标准 ILB 更改 VM 的公共终结点连接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)，以更新示例 NSG 屏幕截图
- 02/03/2020：SAP[应用程序在 SLES 上的 Azure VM 上的 SAP NW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)变化，[在 SLES 上的 Azure VM 上对 SAP NW 的高可用性，用于 SAP 应用程序，](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)用于删除有关在 SLES 上的群集节点主机名中使用破折号的警告
- 01/28/2020：在[RHEL 上的 Azure VM 上更改 SAP HANA 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)，以使 SAP HANA 群集资源超时与红帽超时建议保持一致
- 01/17/2020：使用[SAP 应用程序更改 Azure 接近放置组以获得最佳网络延迟](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)，以更改将现有 VM 移动到邻近放置组的部分
- 01/17/2020：使用[Azure 可用性区域更改 SAP 工作负载配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones)，以指向自动测量可用性区域之间的延迟的过程
- 01/16/2020：更改[如何在 Azure 上安装和配置 SAP HANA（大型实例），](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)使操作系统版本适应 HANA IaaS 硬件目录
- 2020 年 1 月 16 日：使用排队服务器 2 体系结构 （ENSA2） 在[SLES 多 SID 指南上的 Azure VM 上的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)更改，以添加 SAP 系统的说明
- 2020 年 10 月 10 日[：SAP HANA 横向扩展中，在 Azure VM 上使用具有 Azure NetApp 文件的备用节点](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)，在[SAP HANA 横向扩展中，在 Azure VM 上使用具有 AZURE NetApp 文件，在 RHEL 上](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)添加有关如何永久进行更改`nfs4_disable_idmapping`的说明。
- 2020 年 10 月 10 日[：SAP NetWeaver 在 SLES 上的 Azure VM 上具有用于 SAP 应用程序的 Azure NetApp 文件，](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)在 Azure 虚拟机中，SAP [NetWeaver 在 RHEL 上具有高可用性，并且使用 AZURE NetApp 文件为 SAP 应用程序](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)添加如何装载 Azure NetApp 文件 NFSv4 卷的说明。
- 2019/12/23：在[SLES 多 SID 指南上的 Azure VM 上发布 SAP NetWeaver 的高可用性版本](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 2019/12/18：在[Azure VM 上发布 SAP HANA 横向扩展，在 RHEL 上使用 Azure NetApp 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 2019 年 11 月 21 日[：SAP HANA 扩展中具有 Azure VM 上的备用节点的更改，在 SUSE Linux 企业服务器上具有 Azure NetApp 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)，以简化 NFS ID 映射的配置，并更改建议的主网络接口以简化路由。
- 2019 年 11 月 15 日[：SUSE Linux 企业服务器上 SAP NetWeaver 的高可用性略有变化，其中 Azure NetApp 文件适用于 SAP 应用程序](high-availability-guide-suse-netapp-files.md)，而[在红帽企业 Linux 上，SAP NetWeaver 具有用于 SAP 应用程序的 Azure NetApp 文件](high-availability-guide-rhel-netapp-files.md)，以澄清容量池大小限制并删除仅支持 NFSv3 版本的声明。
- 2019/11/12：使用[Azure NetApp 文件 （SMB） 在 Windows 上发布 SAP NetWeaver 的高可用性版本](high-availability-guide-windows-netapp-files-smb.md)
- 2019/11/08：在[SUSE Linux 企业服务器上的 Azure VM 上设置 SAP HANA](sap-hana-high-availability.md)系统高可用性的变化，在 Azure 虚拟机[（VM） 上设置 SAP HANA 系统复制](sap-hana-high-availability-rhel.md)，[在 SUSE Linux 企业服务器上为 SAP NetWeaver 提供高可用性的 Azure](high-availability-guide-suse.md)虚拟机，以及[SUSE Linux 企业服务器上 SAP NetWeaver 的 Azure 虚拟机高可用性。 Azure NetApp 文件](high-availability-guide-suse-netapp-files.md)、 Azure 虚拟机在[红帽企业 Linux 上为 SAP NetWeaver 提供高可用性](high-availability-guide-rhel.md)， [Azure 虚拟机在红帽企业 Linux 上高可用性 SAP NetWeaver 与 Azure NetApp 文件](high-availability-guide-rhel-netapp-files.md)、 [SUSE Linux 企业服务器上的 Azure VM 上的 NFS 的高可用性](high-availability-guide-suse-nfs.md)、[红帽企业 Linux 上 Azure VM 上的 GlusterFS， SAP NetWeaver](high-availability-guide-rhel-glusterfs.md)推荐 Azure 标准负载均衡器  
- 2019/11/08：SAP[工作负载规划和部署清单](sap-deployment-checklist.md)的更改，以澄清加密建议  
- 2019/11/04：在[Azure 中的 SUSE Linux 企业服务器上设置起搏器](high-availability-guide-suse-pacemaker.md)以直接使用单播配置创建群集的更改  
- 2019 年 10 月 29 日：[在 SAP 高可用性方案中使用 Azure 标准负载均衡器释放虚拟机的公共终结点连接](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 2019 年 10 月 25 日[：SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)的更改和[SAP HANA 扩展，在 Azure VM 上使用 Azure NetApp 文件在 SUSE Linux 企业服务器上使用备用节点](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)，以阐明 /hana/共享卷的 NFS 协议
- 2019/10/22：SAP [NetWeaver 在 SUSE Linux 企业服务器上的 Azure VM 上为 SAP 应用程序的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)变化[，SUSE Linux 企业服务器上的 Azure VM 上的 SAP NetWeaver 的高可用性，以及用于 SAP 应用程序的 Azure NetApp 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)[、Azure VM 上的 NFS 高可用性 SUSE Linux 企业服务器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)：[在 Azure 中的 SUSE Linux 企业服务器上设置起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker);[在 SUSE Linux 企业服务器上的 Azure VM 上使用起搏器的高可用性 IBM Db2 LUW"，](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)以及[SUSE Linux 企业服务器上的 Azure VM 上用于](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)Azure 负载均衡器检测强化的高可用性 SAP HANA
- 在[SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中更改 ANF 部分和标头部分
- 2019/10/21：在[Azure VM 上发布 SAP HANA 横向扩展，在 SLES 上具有 Azure NetApp 文件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 2019/10/16：修复[备份和恢复](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)中断开的链接
- 2019/10/16：将推荐的最小操作系统从 SLES 12 SP3 更改为 SLES 12 SP4，在[SUSE Linux 企业服务器上的 Azure VM 上使用起搏器的 Azure VM 上的 IBM Db2 LUW 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 2019 年 10 月 11 日：更改超磁盘存储配置，并在[SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中引入 ANF
- 2019 年 10 月 10 日：使用 SAP 应用程序更改[Azure 接近放置组的图形，以实现最佳的网络延迟](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)，以更清晰
- 2019 年 10 月 10 日：更改[Azure 上的 SAP HANA 基础结构配置和操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)，以更正有关 /hana/共享的高可用性 NFS 共享的声明。 
- 09/28/2019：在[Azure 中的红帽企业 Linux 上设置起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)以澄清 SBD，因为 RHEL 群集不支持屏蔽机制  
- 09/17/2019：NetWeaver 规划和部署指南的更改，以统一 SAP 的 VM 扩展术语  
- 08/22/2019：在[Azure 中的 SUSE Linux 企业服务器上设置起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)以更新自定义角色创建的 URL 的更改  
- 08/16/2019：在[Azure 中的红帽企业 Linux 上设置起搏器的](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)更改，以提醒客户在自定义角色中更新操作（如果更新到新版本的 Azure 围栏代理）  
- 08/15/2019：SAP [HANA Azure 虚拟机存储配置的变化](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)，以反映超磁盘（以前超 SSD）的通用可用性
- 08/01/2019：在[Azure 中的 SUSE Linux 企业服务器上设置起搏器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)的更改，以集成专为 SLES 15 所做的更改 


