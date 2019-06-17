---
title: 将与 Oracle 云基础结构集成 Microsoft Azure |Microsoft Docs
description: 了解将与数据库在 Oracle 云基础结构 (OCI) 在 Microsoft Azure 上运行的 Oracle 应用集成的解决方案。
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 8827feaf1d541edc7c0b54d8af951ddab79c5aaa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808621"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>集成 Microsoft Azure 和 Oracle 云基础结构 （预览版） 的 oracle 应用程序解决方案

Microsoft 和 Oracle 合作提供低延迟、 高吞吐量跨云连接，让您充分利用最佳的两个云。 

使用此跨云连接，可以分区的多层应用程序运行在 Oracle 云基础结构 (OCI) 的数据库层和应用程序和 Microsoft Azure 上的其他层。 体验是类似于单一云中运行整个解决方案堆栈。 

> [!IMPORTANT]
> 此跨云功能目前处于预览状态，而有些[限制](#preview-limitations)。 需同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

如果想在部署完全在 Azure 基础结构上的 Oracle 解决方案，请参阅[Oracle VM 映像和 Microsoft Azure 上的部署](oracle-vm-solutions.md)。

## <a name="scenario-overview"></a>方案概述

跨云连接提供了有关您在享受 OCI 中的托管的数据库服务的优势的 Azure 虚拟机上运行 Oracle 的行业领先的应用程序和你自己的自定义应用程序，解决方案。 

可以在跨云配置中运行的应用程序包括：

* 电子商务套件
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle 零售应用程序
* Oracle Hyperion 财务管理

下图是已连接解决方案的高级概述。 为简单起见，该图显示仅应用层和数据层。 具体取决于应用程序体系结构，解决方案可以包括在 Azure 中的更多层，如 web 层。 有关详细信息，请参阅以下部分。

![Azure OCI 解决方案概述](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>预览版限制

* 在预览版中的跨云连接被限制为 Azure 美国东部 (eastus) 区域和 OCI 阿什本 (我们-阿什本-1) 区域。

## <a name="networking"></a>网络

企业客户通常选择多元化并通过各种业务和操作的原因的多个云部署工作负荷。 若要使多样化，客户互连云网络使用 internet，IPSec VPN 或使用通过你的本地网络的云提供程序的直接连接解决方案。 实现互连云网络可以在时间、 资金、 设计、 采购、 安装、 测试和操作中需要大量投资。 

若要解决这些客户面临的挑战，Oracle 和 Microsoft 已启用集成多云体验。 跨云网络建立的连接[ExpressRoute](../../../expressroute/expressroute-introduction.md)线路在 Microsoft Azure 中使用[FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) OCI 中的线路。 此连接是可能的 Azure ExpressRoute 对等互连位置所在接近或 OCI FastConnect 与相同的对等互连位置中。 此设置可以安全、 快速之间进行连接而不需要中间服务提供程序的两个云。

使用 ExpressRoute 和 FastConnect，客户可以对等互连使用 OCI 中的虚拟云网络在 Azure 中的虚拟网络的专用 IP 地址空间不重叠。 对等互连两个网络允许虚拟网络进行通信 OCI 虚拟云网络中的资源，就好像它们是在同一网络中的资源。

## <a name="network-security"></a>网络安全

网络安全是任何企业应用程序中，一个重要组件，并是此多云解决方案的核心。 通过 ExpressRoute 和 FastConnect 任何流量通过专用网络上。 此配置允许 Azure 虚拟网络与 Oracle 虚拟云网络之间安全通信。 不需要提供 Azure 中的任何虚拟机的公共 IP 地址。 同样，无需在 OCI 的 internet 网关。 所有通信都都通过计算机的专用 IP 地址。

此外，您可以设置[安全列表](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm)OCI 虚拟云网络和安全规则 (附加到 Azure[网络安全组](../../../virtual-network/security-overview.md))。 使用这些规则来控制虚拟网络中的计算机之间的流量。 计算机级别、 子网级别，以及虚拟网络级别，可以添加网络安全规则。
 
## <a name="identity"></a>标识

标识是关系的一个 Microsoft 和 Oracle 数据库之间的关键所在。 完成了大量的工作集成[Oracle 标识云服务](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html)(IDC) 与[Azure Active Directory](../../../active-directory/index.yml) (Azure AD)。 Azure AD 是 Microsoft 的基于云的标识和访问管理服务。 它可帮助你的用户登录并访问各种资源。 Azure AD 还可以管理你的用户及其权限。

目前，这种集成，可在一个中心位置，这是 Azure Active Directory 中管理。 Azure AD 与相应的 Oracle directory 同步目录中的任何更改，并用于实现单一登录以跨云的 Oracle 解决方案。

## <a name="next-steps"></a>后续步骤

有关详细信息和有关 OCI 白皮书，请参阅[Oracle 云](https://docs.cloud.oracle.com/iaas/Content/home.htm)文档。
