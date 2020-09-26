---
title: 将 Microsoft Azure 与 Oracle 云基础结构集成 | Microsoft Docs
description: 了解将 Microsoft Azure 上运行的 Oracle 应用与 Oracle 云基础结构 (OCI) 中的数据库集成的解决方案。
services: virtual-machines-linux
documentationcenter: ''
author: dbakevlar
manager: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/01/2020
ms.author: kegorman
ms.custom: ''
ms.openlocfilehash: a73d327ad2a251b5043da7e58c14e5f6f311e334
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91274497"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>集成 Microsoft Azure 和 Oracle 云基础结构的 Oracle 应用程序解决方案

Microsoft 和 Oracle 合作提供低延迟、高吞吐量的跨云连接，使你能够充分利用这两个云。 

使用此跨云连接，可以对多层应用程序进行分区，以便在 Oracle 云基础结构 (OCI) 上运行数据库层，在 Microsoft Azure 上运行应用程序和其他层。 该体验类似于在单个云中运行整个解决方案堆栈。 

如果你有兴趣在 Azure 基础结构上运行中间件（包括 WebLogic Server），但在 OCI 中运行 Oracle 数据库，请参阅 [WebLogic Server Azure 应用程序](oracle-weblogic.md)。

如果你有兴趣完全在 Azure 基础结构上部署 Oracle 解决方案，请参阅 [Microsoft Azure 上的 Oracle VM 映像及其部署](oracle-vm-solutions.md)。

## <a name="scenario-overview"></a>方案概述

跨云连接为你提供了一种解决方案，用于在 Azure 虚拟机上运行 Oracle 业界领先的应用程序和你自己的自定义应用程序，同时享受 OCI 中托管数据库服务的权益。 

从5月2020，以下应用程序通过跨云配置进行认证：

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle Retail 应用程序
* Oracle Hyperion 财务管理

下图高度概括了互连解决方案。 为简单起见，关系图只显示应用层和数据层。 根据应用程序的体系结构，你的解决方案可能包括其他层，如 Azure 中的 WebLogic Server 群集或 Web 层。 有关详细信息，请参阅以下部分。

![Azure OCI 解决方案概述](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>区域可用性 

跨云连接仅限于以下区域：
* Azure 美国东部 (EastUS) 和 OCI 弗吉尼亚阿什本（美国东部）
* Azure 英国南部 (UKSouth) 和 OCI 伦敦（英国南部）
* Azure 加拿大中部 (CanadaCentral) 和 OCI 多伦多（加拿大东南部）
* Azure 西欧 (WestEurope) 和 OCI 阿姆斯特丹（荷兰西北部）
* Azure 日本东部 (JapanEast) 和 OCI 东京（日本东部）

## <a name="networking"></a>网络

企业客户常常出于各种业务和操作原因选择多样化的工作负载，并将它们部署到多个云上。 为实现多样化，客户需要通过 Internet、IPSec VPN 或通过本地网络使用云提供商的直接连接解决方案来互连云网络。 互连云网络可能需要时间、资金、设计、采购、安装、测试和操作方面的大量投资。 

为了解决这些客户面临的难题，Oracle 和 Microsoft 已经实现了集成的多云体验。 通过将 Microsoft Azure 中的 [ExpressRoute](../../../expressroute/expressroute-introduction.md) 线路与 OCI 中的 [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) 线路连接，可以建立跨云网络。 当 Azure ExpressRoute 对等互连位置与 OCI FastConnect 相邻或在同一对等互连位置时，可以进行此连接。 此设置可实现在两个云之间进行安全的快速连接，而无需使用中间服务提供商。

使用 ExpressRoute 和 FastConnect，客户可以将 Azure 中的虚拟网络与 OCI 中的虚拟云网络对等互连，前提是专用 IP 地址空间不重叠。 将这两种网络对等互连后，虚拟网络中的资源可以与 OCI 虚拟云网络中的资源通信，就好像它们都在同一网络中。

## <a name="network-security"></a>网络安全

网络安全是任何企业应用程序的重要组成部分，是这种多云解决方案的核心。 经由 ExpressRoute 和 FastConnect 的任何流量通过专用网络传递。 此配置可实现 Azure 虚拟网络与 Oracle 虚拟云网络的安全通信。 无需向 Azure 中的任何虚拟机提供公共 IP 地址。 同样，在 OCI 中不需要 Internet 网关。 所有通信都通过计算机的专用 IP 地址进行。

此外，还可以在 OCI 虚拟云网络和安全规则（附加到 Azure [网络安全组](../../../virtual-network/security-overview.md)）上设置[安全列表](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm)。 使用这些规则来控制虚拟网络中计算机之间的流量流。 可以在计算机级别、子网级别以及虚拟网络级别添加网络安全规则。

每个 [WebLogic Server Azure 应用程序](oracle-weblogic.md)都会创建预配置为使用 WebLogic Server 端口配置的网络安全组。
 
## <a name="identity"></a>标识

标识是 Microsoft 与 Oracle 维系合作关系的一个核心支柱。 已完成将 [Oracle 标识云服务](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) 与 [Azure Active Directory](../../../active-directory/index.yml) (Azure AD) 集成的大量工作。 Azure AD 是 Microsoft 推出的基于云的标识和访问管理服务。 用户可以借助 Azure AD 登录和访问各种资源。 Azure AD 还允许管理用户及其权限。

目前借助这种集成，可以在一个中心位置进行管理，即 Azure Active Directory。 Azure AD 将目录中的任何更改与相应的 Oracle 目录同步，并借助 Azure AD 实现到跨云 Oracle 解决方案的单一登录。

## <a name="next-steps"></a>后续步骤

开始使用 Azure 和 OCI 之间的[跨云网络](configure-azure-oci-networking.md)。 

有关 OCI 的详细信息和白皮书，请参阅 [Oracle 云](https://docs.cloud.oracle.com/iaas/Content/home.htm)文档。
