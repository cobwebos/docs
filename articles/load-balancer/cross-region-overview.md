---
title: '跨区域负载均衡器 (预览) '
titleSuffix: Azure Load Balancer
description: Azure 负载均衡器的跨区域负载均衡器层的概述。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: d55f52b5e99a7a617e2bec8bea4d6e6ef687730a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336524"
---
# <a name="cross-region-load-balancer-preview"></a>跨区域负载均衡器 (预览) 

Azure 负载均衡器将到达负载均衡器前端的入站流量分配到后端池实例。

Azure 标准负载均衡器支持跨区域负载均衡，支持异地冗余 HA 方案，例如：

* 来自多个区域的传入流量。
* [即时全局故障转移](#regional-redundancy) 到下一个最佳区域部署。
* 将跨区域的负载分布到最接近的 Azure 区域，具有 [超延迟](#ultra-low-latency)。
* 能够在单个终结点后 [扩展/缩减](#ability-to-scale-updown-behind-a-single-endpoint) 。
* [静态 IP](#static-ip)
* [客户端 IP 保留](#client-ip-preservation)
* 在无学习曲线的[现有负载均衡器解决方案上构建](#build-cross-region-solution-on-existing-azure-load-balancer)

> [!IMPORTANT]
> 跨区域负载均衡器目前处于预览阶段，而不是公开发布。  若要访问跨区域负载均衡器的预览，请联系： [crossregionlb@microsoft.com](mailto:crossregionlb@microsoft.com) 。 </br> </br>
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

跨区域负载均衡提供与区域标准负载均衡器相同的高性能和低延迟的优点。 

跨区域负载均衡器的前端 IP 配置是静态的，在 [大多数 Azure 区域](#participating-regions)中公布。

:::image type="content" source="./media/cross-region-overview/cross-region-load-balancer.png" alt-text="跨区域负载均衡器关系图。" border="true":::

> [!NOTE]
> 跨区域负载均衡器的负载均衡规则的后端端口应与区域标准负载均衡器上的负载均衡规则/入站 nat 规则的前端端口匹配。 

### <a name="regional-redundancy"></a>区域冗余

通过将全局前端公共 IP 地址添加到现有负载均衡器来配置区域冗余。 

如果一个区域出现故障，则会将流量路由到下一个最近的最接近正常的区域负载均衡器。  

跨区域负载均衡器的运行状况探测每隔20秒收集有关可用性的信息。 如果一个区域负载均衡器将其可用性降到0，则跨区域负载均衡器将检测到失败。 然后，区域负载均衡器会脱离旋转。 

:::image type="content" source="./media/cross-region-overview/global-region-view.png" alt-text="全局区域流量视图图示。" border="true":::

### <a name="ultra-low-latency"></a>超高延迟

邻近感应负载平衡算法基于用户和区域部署的地理位置。 

从客户端启动的流量将进入最近的参与区域，并通过 Microsoft 全球网络主干到达最近的区域部署。 

例如，你有一个跨区域负载均衡器，其中包含 Azure 区域中的标准负载均衡器：

* 美国西部
* 北欧

如果流从西雅图开始，流量将进入美国西部。 此区域是西雅图的最近参与区域。 流量被路由到最近的区域负载均衡器，这是美国西部。

Azure 跨区域负载均衡器使用异地邻近性负载平衡算法来做出路由决策。 

区域负载均衡器的已配置负载分配模式用于在多个区域负载均衡器用于地域邻近性时做出最终路由决策。

有关详细信息，请参阅[配置 Azure 负载均衡器的分配模式](https://docs.microsoft.com/azure/load-balancer/load-balancer-distribution-mode)。


### <a name="ability-to-scale-updown-behind-a-single-endpoint"></a>能够在单个终结点后扩展/缩减

向客户公开跨区域负载均衡器的全局终结点时，可以在全局终结点后面添加或删除区域部署，而不会影响客户。 

<!---To learn about how to add or remove a regional deployment from the backend, read more [here](TODO: Insert CLI doc here).--->

### <a name="static-ip"></a>静态 IP
跨区域负载均衡器附带了静态公共 IP，可确保 IP 地址保持不变。 若要了解有关静态 IP 的详细信息，请参阅 [此处](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#allocation-method)

### <a name="client-ip-preservation"></a>客户端 IP 保留
跨区域负载均衡器是第4层直通网络负载均衡器。 此传递将保留数据包的原始 IP。  原始 IP 可用于在虚拟机上运行的代码。 此保存使你可以应用特定于 IP 地址的逻辑。

## <a name="build-cross-region-solution-on-existing-azure-load-balancer"></a>在现有的 Azure 负载均衡器上构建跨区域解决方案
跨区域负载均衡器的后端池包含一个或多个区域负载均衡器。 

将现有的负载均衡器部署添加到跨区域负载均衡器，以实现高度可用的跨区域部署。

**Home 区域** 是部署跨区域负载均衡器的位置。 此区域不影响流量的路由方式。 如果 home 区域出现故障，则不会影响流量流。

### <a name="home-regions"></a>家庭区域
* 美国东部 2
* 美国西部
* 西欧
* Southeast Asia
* 美国中部
* 北欧
* 东亚

> [!NOTE]
> 你只能在上述8个区域之一中部署你的跨区域负载均衡器。

**参与区域**是负载均衡器的全局公共 IP 可用的位置。 

用户启动的流量将通过 Microsoft core 网络传递到最近的参与区域。 

跨区域负载均衡器将流量路由到相应的区域负载均衡器。

### <a name="participating-regions"></a>参与区域
* 美国东部 
* 西欧 
* 美国中部 
* 美国东部 2 
* 美国西部 
* 北欧 
* 美国中南部 
* 美国西部 2 
* 英国南部 
* Southeast Asia 
* 美国中北部 
* Japan East 
* 东亚 
* 美国中西部 
* Australia Southeast 
* 澳大利亚东部 
* 印度中部 

## <a name="limitations"></a>限制

* 跨区域前端 IP 配置仅是公共的。 当前不支持内部前端。

* 无法将私有或内部负载均衡器添加到跨区域负载均衡器的后端池 

* 不支持跨区域 IPv6 前端 IP 配置。 

* 当前无法配置运行状况探测。 默认运行状况探测每隔20秒自动收集有关区域负载均衡器的可用性信息。 

## <a name="pricing-and-sla"></a>定价和 SLA
跨区域负载均衡器共享标准负载均衡器的 [SLA](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/ ) 。

 
## <a name="next-steps"></a>后续步骤

- 请参阅 [创建公共标准负载均衡器](quickstart-load-balancer-standard-public-portal.md) ，开始使用负载均衡器。
- 详细了解 [Azure 负载均衡器](load-balancer-overview.md)。
- 负载均衡器 [常见问题](load-balancer-faqs.md)
