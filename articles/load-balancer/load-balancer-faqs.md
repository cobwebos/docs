---
title: 常见问题-Azure 负载均衡器
description: 有关 Azure 负载均衡器的常见问题的解答。
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3be8ce241817b3b2fa03976eebe3147c1dc9c877
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005153"
---
# <a name="frequently-asked-questions"></a>常见问题

## <a name="what-types-of-load-balancer-exist"></a>存在哪些类型的负载均衡器？
内部负载均衡器，用于平衡 VNET 和外部负载均衡器中的流量，这会平衡进出 internet 连接的终结点的流量。 有关详细信息，请参阅[负载均衡器类型](components.md#frontend-ip-configurations)。 

对于这两种类型，Azure 提供基本 SKU 和标准 SKU，具有不同的功能、性能、安全性和运行状况跟踪功能。 我们的[SKU 比较](skus.md)一文中介绍了这些差异。

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>如何从基本版升级到标准负载均衡器？
请参阅[从基本到标准版升级](upgrade-basic-standard.md)一文，以获取自动脚本和升级负载平衡器 SKU 的指南。

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Azure 中有哪些不同的负载平衡选项？
请参阅适用于负载平衡服务的[负载均衡器技术指南](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)和每个服务的推荐用法。

## <a name="where-can-i-find-load-balancer-arm-templates"></a>在哪里可以找到负载均衡器 ARM 模板？
请参阅常见部署的 ARM 模板的[Azure 负载均衡器快速入门模板列表](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers#quickstart-templates)。

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>入站 NAT 规则与负载均衡规则有何不同？
NAT 规则用于指定要将流量路由到的后端资源。 例如，将特定的负载均衡器端口配置为将 RDP 流量发送到特定的 VM。 负载均衡规则用于指定将流量路由到的后端资源池，平衡每个实例之间的负载。 例如，负载均衡器规则可将负载均衡器的端口80上的 TCP 数据包路由到 web 服务器池。

## <a name="next-steps"></a>后续步骤
如果上面未列出你的问题，请与你的问题一起发送有关本页的反馈。 这会为产品团队创建 GitHub 问题，以确保所有有价值的客户问题都得到了回答。
