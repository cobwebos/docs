---
title: 将 Microsoft Azure 与 Oracle 云基础结构集成 |Microsoft Docs
description: 了解将 Microsoft Azure 上运行的 Oracle 应用与 Oracle 云基础结构中的数据库 (OCI) 集成的解决方案。
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 4628955998ab8b289a429cdfb85e23f7f97b0b40
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101434"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>集成 Microsoft Azure 和 Oracle 云基础结构 (预览版) 的 oracle 应用程序解决方案

Microsoft 和 Oracle 合作提供低延迟、高吞吐量跨云连接, 使你能够充分利用这两个云。 

使用此跨云连接, 可以对多层应用程序进行分区, 以便在 Oracle 云基础结构 (OCI) 上运行数据库层, 并 Microsoft Azure 上的应用程序和其他层。 经验类似于在单个云中运行整个解决方案堆栈。 

> [!IMPORTANT]
> 此跨云功能目前处于预览阶段, 并且[限制适用](#preview-limitations)。 若要在 Azure 与 OCI 之间实现低延迟连接, 必须首先为此功能列出 Azure 订阅。 您必须完成此简短的[调查表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu), 才能注册预览。 注册订阅后，你会收到电子邮件。 只有在收到确认电子邮件后，才能使用该功能。 你还可以联系 Microsoft 代表, 为此预览版启用此功能。 对预览功能的访问权限仅限于由 Microsoft 自行决定的可用性和限制。 完成调查并不保证访问权限。 此预览版在提供时没有服务级别协议, 不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息, 请参阅 Microsoft Azure 预览版的[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

如果你有兴趣完全在 Azure 基础结构上部署 Oracle 解决方案, 请参阅[Microsoft Azure 上的 ORACLE VM 映像及其部署](oracle-vm-solutions.md)。

## <a name="scenario-overview"></a>方案概述

跨云连接为您提供了一种解决方案, 用于在 Azure 虚拟机上运行 Oracle 业界领先的应用程序和您自己的自定义应用程序, 同时在 OCI 中享受托管数据库服务的优势。 

可在跨云配置中运行的应用程序包括:

* 电子商务套件
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle 零售应用程序
* Oracle Hyperion 金融管理

下图是连接解决方案的高级概述。 为简单起见, 关系图只显示应用层和数据层。 根据应用程序的体系结构, 你的解决方案可以在 Azure 中包含其他层, 如 web 层。 有关详细信息，请参阅以下部分。

![Azure OCI 解决方案概述](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>预览版限制

* 预览版中的跨云连接仅限于 Azure 美国东部 (eastus) 区域和 OCI 阿什本 (美国-阿什本-1) 区域。

## <a name="networking"></a>网络

企业客户常常出于各种业务和操作原因选择多元化并部署多个云上的工作负载。 若要多元化, 客户需要通过 internet、IPSec VPN 或通过本地网络使用云提供商的直接连接解决方案来互连云网络。 互连云网络可能需要大量的时间、资金、设计、采购、安装、测试和操作方面的投资。 

为了解决这些客户面临的难题, Oracle 和 Microsoft 已经实现了集成的多云体验。 跨云网络是通过将 Microsoft Azure 中的[ExpressRoute](../../../expressroute/expressroute-introduction.md)线路与 OCI 中的[FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)线路连接起来建立的。 在 Azure ExpressRoute 对等互连位置位于与 OCI FastConnect 相同的对等位置中时, 可以进行此连接。 此设置允许在两个云之间进行安全的快速连接, 无需使用中间服务提供商。

使用 ExpressRoute 和 FastConnect, 客户可以将 Azure 中的虚拟网络与 OCI 中的虚拟云网络对等互连, 前提是专用 IP 地址空间不重叠。 对等互连这两种网络允许虚拟网络中的资源与 OCI 虚拟云网络中的资源通信, 就好像它们都在同一网络中。

## <a name="network-security"></a>网络安全

网络安全是任何企业应用程序的重要组成部分, 是此多云解决方案的核心。 经由 ExpressRoute 和 FastConnect 的任何流量通过专用网络传递。 此配置允许在 Azure 虚拟网络和 Oracle 虚拟云网络之间进行安全通信。 无需向 Azure 中的任何虚拟机提供公共 IP 地址。 同样, 在 OCI 中不需要 internet 网关。 所有通信都通过计算机的专用 IP 地址进行。

此外, 还可以在 OCI 虚拟云网络和安全规则 (附加到 Azure[网络安全组](../../../virtual-network/security-overview.md)) 上设置[安全列表](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm)。 使用这些规则来控制虚拟网络中的计算机之间的流量。 可以在计算机级别、子网级别以及虚拟网络级别添加网络安全规则。
 
## <a name="identity"></a>标识

标识是 Microsoft 与 Oracle 之间合作关系的核心支柱之一。 将[Oracle 标识云服务](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html)(IDCS) 与[Azure Active Directory](../../../active-directory/index.yml) (Azure AD) 集成的工作已完成。 Azure AD 是 Microsoft 的基于云的标识和访问管理服务。 它可帮助用户登录和访问各种资源。 Azure AD 还允许管理用户及其权限。

目前, 这种集成使你可以在一个中心位置进行管理, Azure Active Directory。 Azure AD 将目录中的任何更改与相应的 Oracle 目录同步, 并用于单一登录到跨云 Oracle 解决方案。

## <a name="next-steps"></a>后续步骤

开始在 Azure 与 OCI 之间使用[跨云网络](configure-azure-oci-networking.md)。 

有关 OCI 的详细信息和白皮书, 请参阅[Oracle 云](https://docs.cloud.oracle.com/iaas/Content/home.htm)文档。
