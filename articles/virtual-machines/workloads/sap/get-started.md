---
title: Azure VM 上的 SAP 入门 | Microsoft Docs
description: 了解在 Microsoft Azure 中的虚拟机 (VM) 上运行的 SAP 解决方案
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 40ed06bef45948068e3845e728d9c1d63ed62e71
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762797"
---
# <a name="using-azure-for-hosting-and-running-sap-workload-scenarios"></a>使用 Azure 承载和运行 SAP 工作负荷方案

选择 Microsoft Azure，能够在可缩放、符合要求且经企业认证的平台上可靠运行任务关键型 SAP 工作负荷和方案。  可利用 Azure 的可伸缩性、灵活性和低成本特性。 由于 Microsoft 和 SAP 之间扩大了合作伙伴关系，因此，可以在 Azure 的各个开发/测试和生产方案中运行 SAP 应用程序 - 并且获得完全支持。 从 SAP NetWeaver 到 SAP S/4HANA、SAP BI，Linux 到 Windows，SAP HANA 到 SQL，我们都能满足客户的需求。

除了在 Azure 上承载具有不同 DBMS 的 SAP NetWeaver 方案，可以在 Azure 上承载其他不同的 SAP 工作负荷方案，如 SAP BI。 

Azure for SAP HANA 的独特性在于它是使 Azure 在竞争中脱颖而出的产品/服务。 为了可以承载更多对内存和 CPU 资源要求苛刻的涉及 SAP HANA 的 SAP 方案，Azure 提供客户专用裸机硬件的使用，以便运行对于 S/4HANA 或其他 SAP HANA 工作负荷需要多达 24 TB（可扩大到 120 TB）内存的 SAP HANA 部署。 Azure 上的 SAP HANA（大型实例）这种独特 Azure 解决方案使你可以在专用裸机硬件上运行 SAP HANA，其 SAP 应用层或工作负荷中间软件层在本机 Azure 虚拟机中承载。 此解决方案在“Azure 上的 SAP HANA（大型实例）”部分中的几个文档中进行了记录。   

在 Azure 中承载 SAP 工作负荷方案还可能会导致需要使用 Azure Activity Directory 到不同 SAP 组件以及 SAP SaaS 或 PaaS 产品/服务的标识集成和单一登录。 在“AAD SAP 标识集成和单一登录”部分中介绍和记录了具有 Azure Active Directory (AAD) 和 SAP 实体的这类集成和单一登录方案的列表。

## <a name="latest-changes"></a>最新的更改

新版[Azure HANA 大型实例控制通过 Azure 门户](hana-li-portal.md)

新版[使用 Azure NetApp 文件的 SAP 应用程序的 SUSE Linux Enterprise Server 上的 Azure Vm 上的 SAP NetWeaver 的高可用性](high-availability-guide-suse-netapp-files.md)

Clarification on 澄清**Linux OS 参数 net.ipv4.tcp_timestamps**设置与 Azure 结合使用负载均衡器

发布[使用 Azure 可用性区域的 SAP 工作负荷配置](sap-ha-availability-zones.md)

发布 [SAP 工作负荷规划和部署清单](sap-deployment-checklist.md)




## <a name="sap-hana-on-sap-hana-on-azure-large-instances"></a>Azure 上 SAP HANA 上的 SAP HANA（大型实例）

一系列文档可引导你了解 Azure 上的 SAP HANA（大型实例），简称 HANA 大型实例。 这些文档介绍了列出的 HANA 大型实例领域：

- [Azure 上的 SAP HANA（大型实例）的概述](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure 上的 SAP HANA（大型实例）体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Azure 上的 SAP HANA（大型实例）的基础结构和连接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [在 Azure 上的 SAP HANA（大型实例）上安装 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Azure 上的 SAP HANA（大型实例）的故障排除与监视](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

后续步骤:

- 阅读[Azure 上的 SAP HANA（大型实例）概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 虚拟机上的 SAP HANA
文档的此部分介绍 SAP HANA 的不同方面。 作为先决条件，应熟悉 Azure 的主体服务，它们提供 Azure IaaS 的基本服务，因此主要是 Azure 计算、存储和网络的知识。 在与 SAP NetWeaver 相关的 [Azure 规划指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)中介绍了许多这类主题。 

特定于 Azure 上的 HANA 的文档包括以下列出的文章及其子文章：

- [快速入门：在 Azure VM 上手动安装单实例 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [在 Azure 上部署 SAP S/4HANA 或 BW/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Azure 上的 SAP HANA 基础结构配置和操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Azure 虚拟机的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [一个 Azure 区域内的 SAP HANA 可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [跨 Azure 区域的 SAP HANA 可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Azure 虚拟机上的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Azure 虚拟机上的 SAP HANA 备份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [文件级别的 SAP HANA Azure 备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [基于存储快照的 SAP HANA 备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure 虚拟机上部署的 SAP NetWeaver
在此部分中，可找到有关 Azure 上的 SAP NetWeaver 和 Business One 的规划和部署文档。 本章中的文档主要围绕 Azure 上的非 HANA 数据库及 SAP 工作负荷的基础知识和使用。 而适用于 HA 的文档和文章也是了解 Azure 中的 HANA 高可用性的基础。 文章如下：

- [Azure 虚拟机上的 SAP Business One](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [在 Azure 上为 SAP ERP 6.0 部署 SAP IDES EHP7 SP3](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [运行 Microsoft Azure SUSE Linux VM 上的 SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [SAP NetWeaver 的 Azure 虚拟机规划和实施指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [SAP NetWeaver 的 Azure 虚拟机部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [使用 Site Recovery 保护多层 SAP NetWeaver 应用程序部署](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [适用于 Azure 的 SAP LaMa 连接器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

关于 Azure 上的 SAP 工作负荷下的非 HANA 数据库，文档如下：

- [部署适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 的注意事项](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [适用于 SAP NetWeaver 的 SQL Server Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [适用于 SAP 工作负荷的 Oracle Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [适用于 SAP 工作负荷的 IBM DB2 Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Azure VM 上的 SAP MaxDB、liveCache 和内容服务器部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

对于 Azure 上的 SAP HANA 数据库，查看“Azure 虚拟机上的 SAP HANA”部分。

对于 Azure 上的 SAP 工作负荷的高可用性，入门文档为：

- [Azure 虚拟机上 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

入门文档指向各种其他体系结构和方案文档。 在后续方案文档中，提供了指向介绍不同高可用性方法部署和配置的详细技术文档的链接。 有关建立和配置 SAP NetWeaver 工作负荷高可用性的不同文档涵盖 Linux 以及 Windows 操作系统。


对于 Azure Active Directory (AAD) 与 SAP 服务之间的集成和单一登录，文档如下：

- [教程：将 Azure Active Directory 与 SAP Cloud for Customer 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP Cloud Platform Identity Authentication 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP Cloud Platform 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP NetWeaver 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP Business ByDesign 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [教程：将 Azure Active Directory 与 SAP HANA 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [S/4HANA 环境 — 通过 Azure AD 执行的 Fiori Launchpad SAML 单一登录](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

对于 Azure 服务与 SAP 组件的 集成，文档列表如下所示：

- [在 Power BI Desktop 中使用 SAP HANA](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery 和 SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [在 Power BI Desktop 中使用 SAP BW 连接器](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure 数据工厂提供 SAP HANA 和业务数据仓库数据集成](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




