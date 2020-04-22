---
title: 将微软 Azure 与 Oracle 云基础架构集成 |微软文档
description: 了解将在 Microsoft Azure 上运行的 Oracle 应用与 Oracle 云基础架构 （OCI） 中的数据库集成的解决方案。
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: borisb
ms.custom: ''
ms.openlocfilehash: e70eedcfcdf548965b79e4a48a3a8bfa643f0396
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687433"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>集成微软 Azure 和 Oracle 云基础架构的 Oracle 应用程序解决方案

Microsoft 和 Oracle 已合作提供低延迟、高吞吐量的跨云连接，使您能够充分利用这两种云。 

使用此跨云连接，可以分区多层应用程序，以在 Oracle 云基础结构 （OCI） 以及 Microsoft Azure 上的应用程序和其他层上运行数据库层。 体验类似于在单个云中运行整个解决方案堆栈。 

如果您有兴趣完全在 Azure 基础结构上部署 Oracle 解决方案，请参阅[Oracle VM 映像及其在 Microsoft Azure 上的部署](oracle-vm-solutions.md)。

## <a name="scenario-overview"></a>方案概述

跨云连接为您提供了在 Azure 虚拟机上运行 Oracle 行业领先的应用程序和您自己的自定义应用程序的解决方案，同时享受 OCI 中托管数据库服务的优势。 

> [!IMPORTANT]
> 在 2020 年 5 月之前，Oracle 将在 Azure/Oracle 云互连解决方案中验证这些应用程序在 Azure 中运行。

可在跨云配置中运行的应用程序包括：

* 电子商务套件
* JD Edwards EnterpriseOne
* PeopleSoft
* 甲骨文零售应用程序
* 甲骨文海龙财务管理

下图是连接的解决方案的高级概述。 为简单起见，该关系图仅显示应用程序层和数据层。 根据应用程序体系结构，解决方案可以包括其他层，如 Azure 中的 Web 层。 有关详细信息，请参阅以下部分。

![Azure OCI 解决方案概述](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>区域可用性 

跨云连接仅限于以下区域：
* Azure 东美国（东部） & OCI 阿什本 （美国东部）
* Azure UK 南部（英国南部）& OCI 伦敦（英国南部）
* Azure 加拿大中部（加拿大中部）& OCI 多伦多（加拿大东南部）
* Azure 西欧（西欧） & OCI 阿姆斯特丹 （荷兰西北部）
* Azure 日本东部（日本东部） & OCI 东京 （日本东部）

## <a name="networking"></a>网络

出于各种业务和运营原因，企业客户通常选择在多个云上实现工作负载多样化和部署。 为了实现多样化，客户使用互联网、IPSec VPN 或使用云提供商的直接连接解决方案通过您的本地网络互连云网络。 互联云网络可能需要在时间、资金、设计、采购、安装、测试和运营方面进行大量投资。 

为了应对这些客户挑战，Oracle 和 Microsoft 实现了集成的多云体验。 跨云网络是通过将 Microsoft Azure 中的[ExpressRoute](../../../expressroute/expressroute-introduction.md)电路与 OCI 中的[FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)电路连接而建立的。 如果 Azure ExpressRoute 对等互连位置靠近或与 OCI FastConnect 位于同一对等位置，则可以进行这种连接。 此设置允许在两个云之间实现安全、快速的连接，而无需中间服务提供商。

使用 ExpressRoute 和 FastConnect，客户可以在 Azure 中与 OCI 中的虚拟云网络对等虚拟网络，前提是专用 IP 地址空间不重叠。 对等两个网络允许虚拟网络中的资源与 OCI 虚拟云网络中的资源进行通信，就像它们都在同一网络中一样。

## <a name="network-security"></a>网络安全

网络安全是任何企业应用程序的关键组件，也是此多云解决方案的核心。 通过 ExpressRoute 和 FastConnect 的任何流量都通过专用网络。 此配置允许在 Azure 虚拟网络和 Oracle 虚拟云网络之间进行安全通信。 不需要向 Azure 中的任何虚拟机提供公共 IP 地址。 同样，您不需要 OCI 中的互联网网关。 所有通信都通过计算机的专用 IP 地址进行。

此外，您还可以在 OCI 虚拟云网络和安全规则（附加到 Azure[网络安全组](../../../virtual-network/security-overview.md)）上设置[安全列表](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm)。 使用这些规则控制虚拟网络中计算机之间的流量。 可以在计算机级别、子网级别以及虚拟网络级别添加网络安全规则。
 
## <a name="identity"></a>标识

身份是微软和甲骨文之间伙伴关系的核心支柱之一。 在将[Oracle 标识云服务](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html)（IDCS） 与[Azure 活动目录](../../../active-directory/index.yml)（Azure AD） 集成方面做了大量工作。 Azure AD 是 Microsoft 基于云的标识和访问管理服务。 它可以帮助您的用户登录并访问各种资源。 Azure AD 还允许您管理用户及其权限。

目前，此集成允许您在一个中心位置进行管理，即 Azure 活动目录。 Azure AD 将目录中的任何更改与相应的 Oracle 目录同步，并用于单次登录到跨云 Oracle 解决方案。

## <a name="next-steps"></a>后续步骤

开始使用 Azure 和 OCI 之间的[跨云网络](configure-azure-oci-networking.md)。 

有关 OCI 的详细信息和白皮书，请参阅[Oracle 云](https://docs.cloud.oracle.com/iaas/Content/home.htm)文档。
