---
title: Azure 专用链接常见问题解答 (FAQ)
description: 了解 Azure 专用链接。
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: 91823ff0d324cd30566948fecd86cc441342f14e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91757038"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure 专用链接常见问题解答 (FAQ)

## <a name="private-link"></a>专用链接

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>什么是 Azure 专用终结点和 Azure 专用链接服务？

- **[Azure 专用终结点](private-endpoint-overview.md)** ：Azure 专用终结点是一个网络接口，可以将你通过专用且安全的方式连接到 Azure 专用链接支持的服务。 可以使用专用终结点连接到支持专用链接的 Azure PaaS 服务，或者连接到你自己的专用链接服务。
- **[Azure 专用链接服务](private-link-service-overview.md)** ：Azure 专用链接服务是服务提供商创建的服务。 目前，专用链接服务可以附加到标准负载均衡器的前端 IP 配置。 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>使用专用链接时流量是如何发送的？
使用 Microsoft 主干来私下发送流量。 它不会遍历 internet。 Azure 专用链接不存储客户数据。
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>服务终结点与专用终结点之间的区别是什么？
- 专用终结点向提供粒度分段的给定服务后面的特定资源授予网络访问权限。 流量可以从本地到达服务资源，而无需使用公共终结点。
- 服务终结点仍然是公共可路由的 IP 地址。  专用终结点是配置了专用终结点的虚拟网络的地址空间中的专用 IP。

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>专用链接服务与专用终结点之间的关系是什么？
多个专用链接资源类型支持通过专用终结点进行访问。 资源包括 Azure PaaS 服务和你自己的专用链接服务。 这是一种一对多关系。 

专用链接服务接收来自多个专用终结点的连接。 专用终结点连接到一个专用链接服务。    

## <a name="private-endpoint"></a>专用终结点 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>是否可在同一 VNet 中创建多个专用终结点？ 它们可以连接到不同的服务吗？ 
是。 可以在同一 VNet 或子网中创建多个专用终结点。 它们可以连接到不同的服务。  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>专用终结点是否需要专用子网？ 
不是。 专用终结点不需要专用子网。 可以从部署了你的服务的 VNet 的任何子网中选择专用终结点 IP。  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>专用终结点是否可以跨 Azure Active Directory 租户连接到专用链接服务？ 
是的。 专用终结点可以跨 AD 租户连接到专用链接服务或 Azure PaaS。  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>专用终结点是否可以跨 Azure 区域连接到 Azure PaaS 资源？
是。 专用终结点可以跨 Azure 区域连接到 Azure PaaS 资源。

## <a name="private-link-service"></a>专用链接服务
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>创建专用链接服务的先决条件是什么？ 
服务后端应当位于虚拟网络中，并位于标准负载均衡器后面。
 
### <a name="how-can-i-scale-my-private-link-service"></a>如何缩放专用链接服务？ 
可以通过几种不同的方式缩放专用链接服务： 
- 向标准负载均衡器后面的池添加后端 VM 
- 向专用链接服务添加 IP。 每个专用链接服务最多允许 8 个 IP。  
- 向标准负载均衡器添加新的专用链接服务。 每个负载均衡器最多允许 8 个专用链接服务。   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>是否可以将服务连接到多个专用终结点？
是。 一个专用链接服务可以接收来自多个专用终结点的连接。 但是，一个专用终结点只能连接到一个专用链接服务。  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>应该如何控制我的专用链接服务的公开？
你可以使用专用链接服务上的可见性配置来控制公开。 可见性支持三种设置：

- **无** - 只有具有 RBAC 访问权限的订阅才能找到服务。 
- 仅限限制的受**限制**订阅和 RBAC 访问权限可以找到服务。 
- **全部** - 所有人都可以找到服务。 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>是否可以使用基本负载均衡器创建专用链接服务？ 
否。 不支持通过基本负载均衡器的专用链接服务。
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>专用链接服务是否需要专用子网？ 
不是。 专用链接服务不需要专用子网。 可以选择部署了你的服务的 VNet 中的任何子网。   

### <a name="im-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>我是使用 Azure 专用链接的服务提供商。 我是否需要确保所有客户都具有唯一的 IP 空间，而不会与我的 IP 空间重叠？ 
不是。 Azure 专用链接会为你提供此功能。 你不需要具有与客户的地址空间不重叠的地址空间。 

##  <a name="next-steps"></a>后续步骤

- 了解 [Azure 专用链接](private-link-overview.md)
