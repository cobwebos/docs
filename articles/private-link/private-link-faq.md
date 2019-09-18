---
title: Azure 专用链接常见问题（FAQ）
description: 了解 Azure 专用链接。
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 7e08c91636089abf25e46e1303097fc27b2d75b1
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037508"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Azure 专用链接常见问题（FAQ）

## <a name="private-link"></a>专用链接

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>什么是 Azure 专用终结点和 Azure Private Link Service？

- **[Azure 专用终结点](private-endpoint-overview.md)** ：Azure 专用终结点是一个网络接口，该接口将你私下并安全地连接到由 Azure 专用链接提供支持的服务。 你可以使用专用终结点连接到支持专用链接或你自己的专用链接服务的 Azure PaaS 服务。
- **[Azure 专用链接服务](private-link-service-overview.md)** ：Azure 专用链接服务是服务提供商创建的服务。 目前，专用链接服务可附加到标准负载均衡器的前端 IP 配置。 

### <a name="how-is-traffic-being-send-when-using-private-link"></a>使用专用链接时如何发送通信？
流量是使用 Microsoft 主干进行私下发送的。 它不会遍历 internet。  
 
### <a name="what-is-the-difference-between-a-service-endpoints-and-a-private-endpoints"></a>服务终结点和专用终结点之间的区别是什么？
- 使用专用终结点时，会将网络访问权限授予给定服务后面提供粒度分段的特定资源，同时流量可以从本地到达服务资源，而无需使用公共终结点。

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>专用链接服务和专用终结点之间的关系是什么？
专用终结点提供对多个专用链接资源类型（包括 Azure PaaS 服务和你自己的专用链接服务）的访问权限。 这是一种一对多关系。 一个专用链接服务可以接收来自多个专用终结点的连接。 另一方面，一个专用终结点只能连接到一个专用链接服务。    

## <a name="private-endpoint"></a>私有终结点 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>能否在同一 VNet 中创建多个专用终结点？ 他们可以连接到不同的服务吗？ 
是。 同一 VNet 或子网中可以有多个专用终结点。 它们可以连接到不同的服务。  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>是否需要专用终结点专用子网？ 
否。 不需要专用终结点专用子网。 可以从部署服务的 VNet 中的任何子网中选择专用终结点 IP。  
 
### <a name="can-private-endpoint-connect-to-private-link-service-across-azure-active-directory-tenants"></a>专用终结点是否可跨 Azure Active Directory 租户连接到专用链接服务？ 
是。 专用终结点可跨 AD 租户连接到专用链接服务或 Azure PaaS。  
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>专用终结点是否可以跨 Azure 区域连接到 Azure PaaS 资源？
是。 专用终结点可以跨 Azure 区域连接到 Azure PaaS 资源。

## <a name="private-link-service"></a>专用链接服务
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>创建专用链接服务的先决条件是什么？ 
服务后端应位于虚拟网络中，并位于标准负载均衡器后面。
 
### <a name="how-can-i-scale-my-private-link-service"></a>如何缩放专用链接服务？ 
可以通过几种不同的方式缩放专用链接服务： 
- 将后端 Vm 添加到标准负载均衡器后面的池 
- 将 IP 添加到专用链接服务。 每个专用链接服务最多允许8个 Ip。  
- 将新的私有链接服务添加到标准负载均衡器。 每个负载均衡器最多允许8个专用链接服务。   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>是否可以将服务连接到多个专用终结点？
是。 一个专用链接服务可以接收来自多个专用终结点的连接。 不过，一个专用终结点只能连接到一个专用链接服务。  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>我应该如何控制我的私有链接服务的公开？
你可以使用私有链接服务上的可见性配置来控制公开。 可见性支持三种设置：

- **无**-只有使用 RBAC 访问权限的订阅才能找到服务。 
- 仅限**限制**的已列入允许列表的订阅和 RBAC 访问权限可查找该服务。 
- **所有-所有**人都可以找到服务。 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>是否可以使用基本负载均衡器创建专用链接服务？ 
否。 不支持通过基本负载均衡器的专用链接服务。
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>专用链接服务是否需要专用子网？ 
否。 专用链接服务不需要专用子网。 可以在 VNet 中选择部署服务的任何子网。   

### <a name="i-am-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>我是使用 Azure 私有链接的服务提供商。 我是否需要确保所有客户都具有唯一的 IP 空间，而不会与我的 IP 空间重叠？ 
否。 Azure Private Link 为你提供此功能。 因此，您不需要具有与客户的地址空间不重叠的地址空间。 

##  <a name="next-steps"></a>后续步骤

- 了解[Azure 专用链接](private-link-overview.md)
