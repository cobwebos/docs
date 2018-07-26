---
title: Azure 虚拟机上的 SAP Business One | Microsoft Docs
description: Azure 上的 SAP Business One。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0960f569f2a582d9712473081f66205272cfe31a
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116957"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>Azure 虚拟机上的 SAP Business One
本文档提供在 Azure 虚拟机上部署 SAP Business One 的指南。 本文档不能替代 SAP Business One 的安装文档。 本文档涵盖运行 Business One 应用程序所在的 Azure 基础结构的基本规划和部署指南。

Business One 支持两种不同的数据库：
- SQL Server - 请参阅 [SAP 说明 #928839 - Microsoft SQL Server 发布计划](https://launchpad.support.sap.com/#/notes/928839)
- SAP HANA - 有关面向 SAP HANA 的确切 SAP Business One 支持矩阵，请查看 [SAP 产品可用性矩阵](https://support.sap.com/pam)

对于 SQL Server，可参阅 [SAP NetWeaver 的 Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/dbms-guide)中记录的基本部署注意事项。 对于 SAP HANA，本文档中提到了相关注意事项。

## <a name="prerequisites"></a>先决条件
要使用本指南，需要具备以下 Azure 组件的基础知识：

- [Windows 上的 Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- [Linux 上的 Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [使用 PowerShell 执行 Azure 网络创建和虚拟网络管理](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-virtual-network)
- [使用 CLI 创建 Azure 网络和虚拟网络](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [使用 Azure CLI 2.0 管理 Azure 磁盘](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

即使只对 Business one 感兴趣，[NetWeaver 的 Azure 虚拟机规划和实施](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/planning-guide)文档也是很好的信息源。

对于部署 SAP Business One，本文假定你：

- 熟悉在给定的基础结构（如 VM）上安装 SAP HANA
- 熟悉在基础结构（如 Azure VM）上安装 SAP Business One 应用程序
- 熟悉 SAP Business One 操作和所选的 DBMS 系统
- 熟悉在 Azure 中部署基础结构

所有这些方面在本文档中均不会介绍。

除了 Azure 文档外，还应该了解主要的 SAP 说明，包括 Business One 说明或者是来自 SAP 的 Business One 核心说明：

- [528296 - SAP Business One 发布和相关产品的常规概述说明](https://launchpad.support.sap.com/#/notes/528296)
- [2216195 - 适用于 SAP HANA 的 SAP Business One 9.2 版本的发布更新说明](https://launchpad.support.sap.com/#/notes/2216195)
- [2483583 - SAP Business One 9.3 的核心说明](https://launchpad.support.sap.com/#/notes/2483583)
- [2483615 - SAP Business One 9.3 的发布更新说明](https://launchpad.support.sap.com/#/notes/2483615)
- [2483595 - SAP Business One 9.3 常规问题的总体说明](https://launchpad.support.sap.com/#/notes/2483595)
- [2027458 - 适用于 SAP HANA 的 SAP Business One 版本的 SAP HANA 相关主题总体咨询说明](https://launchpad.support.sap.com/#/notes/2027458)


## <a name="business-one-architecture"></a>Business One 体系结构
Business One 是一个具有两层的应用程序：

- 具有“fat”客户端的客户端层
- 包含租户数据库架构的数据库层

[SAP Business One 管理员指南](https://help.sap.com/http.svc/rc/879bd9289df34a47af838e67d74ea302/9.3/en-US/AdministratorGuide_SQL.pdf)中更详尽地概述了哪些组件在客户端部分运行，哪些部分在服务器部分运行 

由于客户端层和 DBMS 层之间存在严重的延迟关键交互，因此在 Azure 中部署时，这两个层都需要位于 Azure 中。 通常，用户随后将通过 RDS 进入为 Business One 客户端组件运行 RDS 服务的一个或多个 VM。

### <a name="sizing-vms-for-sap-business-one"></a>为 SAP Business One 调整 VM 大小

有关客户端 VM 的大小调整，资源要求由 SAP 记录在文档 [SAP Business One 硬件要求指南](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf)中。 对于 Azure，需要关注文档第 2.4 章中所述的要求并按照这些要求计算。

作为用于托管 Business One 客户端组件和 DBMS 主机的 Azure 虚拟机，只允许使用支持 SAP NetWeaver 的 VM。 若要查找支持 SAP NetWeaver 的 Azure VM 列表，请阅读 [SAP 说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)。

若将 SAP HANA 作为 Business One 的 DBMS 后端运行，则 HANA 仅支持在 [HANA 认证的 IaaS 平台列表](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One)中针对 HANA 上的 Business 列出的 VM。 Business One 客户端组件不受 SAP HANA 作为 DBMS 系统的这一更强限制的影响。

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>用于 SAP Business One 的操作系统版本

原则上，最好始终使用最新的操作系统版本。 特别是在 Linux 领域，新的 Azure 功能随 Suse 和 Red Hat 的不同最近次要版本引入。 对于 Windows，强烈建议使用 Windows Server 2016。


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>在 Azure 中为 SAP Business One 部署基础结构
接下来的几章将介绍对部署 SAP 至关重要的基础结构部分。

### <a name="azure-network-infrastructure"></a>Azure 网络基础结构
需要在 Azure 中部署的网络基础结构取决于你是否为自己部署了单个 Business One 系统。 或者，你是否是为客户托管数十个 Business One 系统的主机托管服务提供商。 设计中可能会有关于是否连接到 Azure 的细微变化。 仔细考虑不同的可能性，一种设计是建立到 Azure 的 VPN 连接，并将 Active Directory 通过 [VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-plan-design) 或 [ExpressRoute](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction) 扩展到 Azure 中。

![Business One 的简单网络配置](./media/business-one-azure/simple-network-with-VPN.PNG)

提供的简化配置中引入了允许控制和限制路由的多个安全实例。 这首先需要 

- 客户本地端上的路由器/防火墙。
- 下一个实例是 [Azure 网络安全组](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview)，可以使用该安全组为在其中运行 SAP Business one 配置的 Azure VNet 引入路由和安全规则。
- 为了避免 Business One 客户端的用户也可以看到运行 Business One 服务器（该服务器运行数据库）的服务器，应该将托管 Business One 客户端的 VM 和 Business One 服务器分离在 VNet 内的两个不同子网中。
- 再次使用分配给两个不同子网的 Azure NSG，以便限制对 Business one 服务器的访问。

还有一种更复杂的 Azure 网络配置版本，它基于 Azure [记录的中心辐射型体系结构的最佳做法](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)。 中心辐射型体系结构模式会将第一个简化配置更改为如下配置：


![Business One 的中心辐射型配置](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

对于用户通过 Internet 连接而没有任何专用连接进入 Azure 的情况，Azure 中的网络设计应符合 Azure 参考体系结构中记录的 [Azure 和 Internet 之间的 DMZ](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-dmz) 原则。

### <a name="business-one-database-server"></a>Business One 数据库服务器
对于数据库类型，可以使用 SQL Server 和 SAP HANA。 除了解 DBMS 外，应阅读[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)文档，大致了解 Azure VM 中的 DBMS 部署以及相关的网络和存储主题。

已在特定和通用的数据库文档中强调过，你需要熟悉：

- [在 Azure 中管理 Windows 虚拟机的可用性](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)和[在 Azure 中管理 Linux 虚拟机的可用性](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)
- [虚拟机的 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

这些文档应该对选择存储类型和高可用性配置有所帮助。

原则上应该：

- 使用 [Azure 高级存储](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)，而不是[Azure 标准存储](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- 使用 Azure 托管磁盘而不是非托管磁盘
- 确保通过磁盘配置来配置足够的 IOPS 和 I/O 吞吐量
- 组合使用 /hana/data 和 /hana/log 卷，以便拥有具有成本效益的存储配置


#### <a name="sql-server-as-dbms"></a>SQL Server 作为 DBMS
有关将 SQL Server 部署为 Business One 的 DBMS 的信息，请阅读文档[适用于 SAP NetWeaver 的 SQL Server Azure 虚拟机 DBMS 部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)。 

SQL Server 的 DBMS 端的粗略大小估算值为：

| 用户数 | vCPU | 内存 | VM 类型示例 |
| --- | --- | --- | --- |
| 最多 20 个 | 4 | 16 GB | D4s_v3、E4s_v3 |
| 最多 40 个 | 8 | 32 GB | D8s_v3、E8s_v3 |
| 最多 80 个 | 16 | 64 GB | D16s_v3、E16s_v3 |
| 最多 150 个 | 32 | 128 GB | D32s_v3、E32s_v3 |

通过上面列出的大小，你应该对从何处开始有了一些概念。 你可能需要更少或更多的资源，取决于在哪种情况下可在 Azure 上轻松进行调整。 只需重启 VM，便可在 VM 类型之间进行更改。


#### <a name="sap-hana-as-dbms"></a>SAP HANA 作为 DBMS
若将 SAP HANA 用作 DBMS，则应该在以下各节遵循文档 [Azure 上的 SAP HANA 操作指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)中的注意事项。

对于围绕着 SAP HANA 作为 Azure 中 Business One 数据库的高可用性和灾难恢复配置，应该阅读文档 [Azure 虚拟机的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)以及该文档中提到的文档。

对于 SAP HANA 备份和还原策略，应阅读文档 [Azure 虚拟机上的 SAP HANA 备份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)以及该文档中提到的文档。

 
### <a name="business-one-client-server"></a>Business One 客户端服务器
对于这些组件，存储注意事项不是主要关注的问题。 不过，你需要有一个可靠的平台。 因此，应该对此 VM 使用 Azure 高级存储，即使对于基本 VHD 也是如此。 使用 [SAP Business One 硬件需求指南](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf)中给出的数据调整 VM 的大小。 对于 Azure，需要关注文档第 2.4 章中所述的要求并按照这些要求计算。 在计算要求时，需要将它们针对以下文档进行比较，以便找到理想的 VM：

- [Azure 中 Windows 虚拟机的大小](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes)
- [SAP 说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)

将所需的 CPU 数和内存量与 Microsoft 记录的数量进行比较。 选择 VM 时也要考虑网络吞吐量。








