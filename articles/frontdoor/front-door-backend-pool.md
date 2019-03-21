---
title: Azure Front Door 服务 - 后端和后端池 | Microsoft Docs
description: 本文可帮助你了解后端和后端池在 Front Door 配置中的作用。
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 228ed5c54a382db7b47d19adacf9e5db398c53ae
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123685"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Azure Front Door 服务中的后端和后端池
本文介绍了有关如何将应用程序部署与 Front Door 相对应的各种概念。 我们还将介绍不同的术语在 Front 应用程序后端各地门配置意味着什么。

## <a name="backend-pool"></a>后端池
Front Door 中的后端池是指可为其应用程序接收相同流量类型的同等后端集。 换而言之，它是世界各地应用程序实例的逻辑分组，这些实例可以接收相同的流量，并以预期行为做出响应。 这些后端通常跨不同区域或在同一区域内部署。 此外，这些后端可以全部处于主动-主动部署模式，或者定义为主动/被动配置。

后端池还定义如何通过运行状况探测来评估不同后端的运行状况，以及后端之间的负载平衡会相应地采取什么操作。

### <a name="health-probes"></a>运行状况探测
Front Door 会将定期 HTTP/HTTPS 探测请求发送到配置的每个后端，确定每个后端的接近度和运行状况，以便对最终用户请求进行负载平衡。 后端池的运行状况探测设置会定义我们如何轮询应用程序后端的运行状况状态。 以下设置可用于配置负载平衡：

1. **路径**：其中探测请求将发送到后端池中的所有后端的 URL 路径。 例如，如果你的其中一个后端是 `contoso-westus.azurewebsites.net`，并且路径设置为 `/probe/test.aspx`，则 Front Door 环境中（假设协议设置为 HTTP）会将运行状况探测请求发送到 http://contoso-westus.azurewebsites.net/probe/test.aspx。 
2. **协议**：定义是否将通过 HTTP 或 HTTPS 协议发送到后端运行状况探测请求从第一道防线。
3. **间隔 （秒）**:此字段定义后端，也就是说，每个第一道防线环境将在其中发送探测的时间间隔运行状况探测的频率。 注意 - 如果你正在寻求更快的故障转移，请将此字段设置为较低值。 但是，值越低，你的后端将收到越多的运行状况探测量。 若要了解 Front Door 将在后端生成的探测量，让我们来看一个示例。 例如，间隔设置为 30 秒，并且在全球有大约 90 个 Front Door 环境或 POP。 因此，每个后端大约每秒收到 3-5 个探测请求。

阅读[运行状况探测](front-door-health-probes.md)了解详细信息。

### <a name="load-balancing-settings"></a>负载均衡设置
后端池的负载平衡设置定义我们如何评估用于确定后端正常还是不正常的运行状况探测，还定义我们需要如何在后端池中的不同后端之间对流量进行负载平衡。 以下设置可用于配置负载平衡：

1. **样本大小**:此属性标识我们需要考虑进行后端运行状况评估的运行状况探测的多少样本。
2. **成功的样本大小**:此属性定义的示例大小的如上所述，多少样本我们需要检查成功调用为正常运行后的端。 
</br>例如，假设在 Front Door 中将运行状况探测“间隔”设置为 30 秒，将“样本大小”设置为“5”，将“成功样本大小”设置为“3”。 则此配置的含义是，我们每次评估后端的运行状况探测时，都会检查过去 150 秒内的最后五个样本 (= 5 * 30 s)，除非这些探测中有 3 个或更多探测成功，否则我们将声明该后端不正常。 假设只有两个成功的探测，我们会将该后端标记为不正常。 下次我们运行评估时，如果在最后五个探测中有 3 个成功，则我们会将该后端标记为正常。
3. **延迟敏感性 （额外延迟）**:延迟敏感字段定义您是否希望将请求发送到后端延迟的度量值或将请求转发到最接近的后端根据敏感度范围内的第一道防线。 阅读 Front Door 的[基于最少延迟的路由方法](front-door-routing-methods.md#latency)了解详细信息。

## <a name="backend"></a>后端
后端相当于区域中的应用程序部署实例。 Front Door 支持 Azure 和非 Azure 后端，因此此处的区域不仅限于 Azure 区域，也可以是本地数据中心或其他云中的应用程序实例。

在涉及 Front Door 时，后端是指应用程序的主机名或公共 IP，该应用程序可发出客户端请求。 因此，不应将后端与数据库层或存储层等混淆，而应视为应用程序后端的公共终结点。

在 Front Door 的后端池中添加后端时，需要填写以下详细信息：

1. **后端主机类型**:你想要添加的资源的类型。 如果是从应用服务、云服务或存储操作，Front Door 支持自动发现应用程序后端。 如果需要 Azure 中的不同资源，甚至非 Azure 后端，请选择“自定义主机”。 注意 - 在配置期间，API 不会验证后端是否可从 Front Door 环境访问，你需要确保可从 Front Door 访问后端。 
2. **订阅和后端主机名**:如果尚未选择自定义主机后端主机类型，则需要作用域下，选择你的后端通过从用户界面中选择相应的订阅和相应的后端主机名称。
3. **后端主机标头**:发送到每个请求的后端的主机标头值。 阅读[后端主机标头](#hostheader)了解详细信息。
4. **优先级**：如果想要使用主服务后端来处理所有流量，并提供备份来防范主后端或备份后端不可用的情况，可为不同的后端分配优先级。 阅读有关[优先级](front-door-routing-methods.md#priority)的详细信息。
5. **权重**:如果想要在一组后端之间分布流量，可以为不同后端分配权重，可以平均分布，也可以根据权重系数分布。 阅读有关[权重](front-door-routing-methods.md#weighted)的详细信息。


### <a name = "hostheader"></a>后端主机标头

Front Door 转发到后端的请求必须具有后端用来检索目标资源的主机标头字段。 此字段的值通常来自后端 URI，具有主机和端口。 例如，发送给 `www.contoso.com` 的请求将具有主机标头 `www.contoso.com`。 如果使用 Azure 门户配置后端，则为此字段填充的默认值为后端的主机名。 例如，如果后端为 `contoso-westus.azurewebsites.net`，则在 Azure 门户中，后端主机标头的自动填充值为 `contoso-westus.azurewebsites.net`。 
</br>但是，如果使用资源管理器模板或其他机制，并且未显式设置此字段，则 Front Door 会发送传入主机名称作为主机标头的值。 例如，如果请求是发送给 `www.contoso.com`，并且后端为 `contoso-westus.azurewebsites.net`，而“后端主机标头”字段为空，则 Front Door 会将“主机标头”设置为 `www.contoso.com`。

大多数应用程序后端（如 Web 应用、Blob 存储和云服务）要求主机标头与后端的域相匹配。 但是，将路由到后端前端主机将具有不同的主机名，如 www\.contoso.azurefd.net。 如果正在设置的后端要求主机标头与后端的主机名相匹配，应确保“后端主机标头”也具有后端的主机名。

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>配置后端的后端主机标头
可以在后端池部分为后端配置“后端主机标头”字段。

1. 打开 Front Door 资源，然后单击具有要配置的后端的后端池。

2. 如果未添加任何后端，请进行添加，或编辑现有后端。 “后端主机标头”字段可设置为自定义值或留空，这意味着将使用传入请求的主机名作为“主机标头”值。



## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的工作原理](front-door-routing-architecture.md)。