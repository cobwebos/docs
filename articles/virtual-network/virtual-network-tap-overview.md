---
title: Azure 虚拟网络 TAP 概述 | Microsoft 文档
description: 了解虚拟网络 TAP。 虚拟网络 TAP 为可以流式传输到数据包收集器的虚拟机网络流量提供了深层复制。
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2019
ms.author: kaanan
ms.openlocfilehash: 99cd9fc1da009660023a246c5210e7f54bdebcfd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177426"
---
# <a name="virtual-network-tap"></a>虚拟网络 TAP

通过 Azure 虚拟网络 TAP（终端接入点），可让你持续将虚拟机网络流量流式传输到网络数据包收集器或分析工具。 收集器或分析工具由[网络虚拟设备](https://azure.microsoft.com/solutions/network-appliances/)合作伙伴提供。 有关经验证可与虚拟网络 TAP 一起使用的合作伙伴解决方案列表，请参阅[合作伙伴解决方案](#virtual-network-tap-partner-solutions)。

> [!IMPORTANT]
> 虚拟网络 TAP 目前正在所有 Azure 区域中以预览版提供。 若要使用虚拟网络点击，你必须通过使用你的订阅 ID 发送电子邮件到 <azurevnettap@microsoft.com> 来注册预览。 注册订阅后，你会收到电子邮件。 只有在收到确认电子邮件后，才能使用该功能。 此预览版在提供时没有附带服务级别协议，不应用于生产工作负荷。 某些功能可能不受支持或受到约束，或者不一定在所有 Azure 位置都可用。 有关详细信息，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 。

## <a name="virtual-network-tap-partner-solutions"></a>虚拟网络 TAP 合作伙伴解决方案

### <a name="network-packet-brokers"></a>网络数据包中转站

- [Big Switch Big Monitoring Fabric](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)

### <a name="security-analytics-networkapplication-performance-management"></a>安全分析、网络/应用程序性能管理

- [Awake Security](https://awakesecurity.com/technology-partners/microsoft-azure/)
- [Cisco Stealthwatch Cloud](https://blogs.cisco.com/security/cisco-stealthwatch-cloud-and-microsoft-azure-reliable-cloud-infrastructure-meets-comprehensive-cloud-security)
- [Darktrace](https://www.darktrace.com/en/azure/)
- [ExtraHop Reveal(x)](https://www.extrahop.com/partners/tech-partners/microsoft/)
- [Fidelis Cybersecurity](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [NetFort LANGuardian](https://www.netfort.com/languardian/solutions/visibility-in-azure-network-tap/)
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Riverbed SteelCentral AppResponse]( https://www.riverbed.com/products/steelcentral/steelcentral-appresponse-11.html)
- [RSA NetWitness® 平台](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

下图显示虚拟网络 TAP 的工作原理。 可以在[网络接口](virtual-network-network-interface.md)（连接到虚拟网络中部署的虚拟机）上添加 TAP 配置。 目标是与受监视网络接口或[对等虚拟](virtual-network-peering-overview.md)网络位于同一虚拟网络中的虚拟网络 IP 地址。 虚拟网络 TAP 的收集器解决方案可以部署在 [Azure 内部负载均衡器](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#concepts)后面，以实现高可用性。 若要评估单个解决方案的部署选项，请参阅[合作伙伴解决方案](#virtual-network-tap-partner-solutions)。

![虚拟网络 TAP 的工作原理](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>先决条件

在创建虚拟网络 TAP 之前，必须已收到你注册预览版的确认邮件，并且已使用 [Azure 资源管理器](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)部署模型和合作伙伴解决方案创建了一个或多个虚拟机，以聚合同一 Azure 区域的 TAP 流量。 如果在虚拟网络中没有合作伙伴解决方案，请参阅[合作伙伴解决方案](#virtual-network-tap-partner-solutions)来部署一个解决方案。 你可以使用相同的虚拟网络 TAP 资源来聚合来自相同或不同订阅的多个网络接口的流量。 如果受监视的网络接口位于不同的订阅中，则订阅必须关联到同一 Azure Active Directory 租户。 此外，用于聚合 TAP 流量的受监视网络接口和目标终结点可以位于同一区域中的对等虚拟网络中。 如果你使用的是这种部署模型，请务必在配置虚拟网络 TAP 之前启用[虚拟网络对等互连](virtual-network-peering-overview.md)。

## <a name="permissions"></a>权限

用于在网络接口上应用 TAP 配置的帐户，必须被赋予[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配有下表中必要操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)：

| 操作 | 名称 |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | 在创建、更新、读取和删除虚拟网络 TAP 资源时需要 |
| Microsoft.Network/networkInterfaces/read | 在读取将配置 TAP 的网络接口资源时需要 |
| Microsoft.Network/tapConfigurations/* | 在创建、更新、读取和删除网络接口上的 TAP 配置时需要 |

## <a name="next-steps"></a>后续步骤

- 了解如何[创建虚拟网络 TAP](tutorial-tap-virtual-network-cli.md)。
