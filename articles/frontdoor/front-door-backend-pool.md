---
title: Azure 前门中的后端和后端池*微软文档
description: 本文介绍了前门配置中的后端池和后端池。
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
ms.openlocfilehash: 18b165d83bfa154348842542bd8323a40330aa2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293467"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Azure 前门的后端和后端池
本文介绍有关如何使用 Azure 前门映射应用部署的概念。 它还解释了前门配置中有关应用后端的不同术语。

## <a name="backends"></a>后端
后端等于应用中在区域中的部署实例。 前门同时支持 Azure 和非 Azure 后端，因此该区域不仅仅限于 Azure 区域。 此外，它可以是本地数据中心或另一个云中的应用实例。

前门后端是指应用程序的主机名或公共 IP，它可以为客户端请求提供服务。 后端不应与数据库层、存储层等混淆。 后端应被视为应用后端的公共终结点。 在前门后端池中添加后端时，还必须添加以下内容：

- **后端主机类型**。 要添加的资源类型。 前门支持自动发现应用服务、云服务或存储的应用后端。 如果想要 Azure 中的不同资源，甚至需要非 Azure 后端，请选择 **"自定义主机**"。

    >[!IMPORTANT]
    >在配置期间，API 不会验证后端是否从前门环境无法访问。 确保前门可以到达您的后端。

- **订阅和后端主机名**。 如果尚未为后端主机类型选择**自定义主机**，请在 UI 中选择相应的订阅和相应的后端主机名来选择后端。

- **后端主机标头**。 为每个请求发送到后端的主机标头值。 有关详细信息，请参阅[后端主机标头](#hostheader)。

- **优先级**。 当您要对所有流量使用主服务后端时，将优先级分配给不同的后端。 此外，如果主后端或备份后端不可用，请提供备份。 有关详细信息，请参阅[优先级](front-door-routing-methods.md#priority)。

- **重量**. 将权重分配给不同的后端，以便均匀或根据权重系数在一组后端分配流量。 有关详细信息，请参阅[权重](front-door-routing-methods.md#weighted)。

### <a name="backend-host-header"></a><a name = "hostheader"></a>后端主机标头

前门转发到后端的请求包括后端用于检索目标资源的主机标头字段。 此字段的值通常来自后端 URI，具有主机和端口。

例如，为发出`www.contoso.com`的请求将具有主机标头www.contoso.com。 如果使用 Azure 门户配置后端，则此字段的默认值是后端的主机名。 如果后端contoso-westus.azurewebsites.net，则在 Azure 门户中，后端主机标头的自动填充值将contoso-westus.azurewebsites.net。 但是，如果使用 Azure 资源管理器模板或其他方法而不显式设置此字段，前门将发送传入主机名作为主机标头的值。 如果请求是 contoso.com 的\.www contoso.com，并且后端contoso-westus.azurewebsites.net具有空标头字段，则前门将主机标头设置为 www\.contoso.com。

大多数应用后端（Azure Web 应用、Blob 存储和云服务）都需要主机标头与后端的域匹配。 但是，路由到后端的前端主机将使用其他主机名，如www.contoso.net。

如果后端需要主机标头与后端主机名匹配，请确保后端主机标头包含主机名后端。

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>配置后端的后端主机标头

要为后端池部分中的后端配置**后端主机头**字段，请进行以下工作：

1. 打开前门资源，选择后端池，然后配置后端池。

2. 如果尚未添加后端，请添加后端，或编辑现有后端。

3. 将后端主机标头字段设置为自定义值或将其留空。 传入请求的主机名将用作主机标头值。

## <a name="backend-pools"></a>后端池
前门的后端池是指为其应用接收类似流量的后端集。 换句话说，它是全世界应用实例的逻辑分组，这些实例接收相同的流量，并响应预期的行为。 这些后端部署在不同区域或同一区域内。 所有后端都可以处于主动/主动部署模式或定义为主动/被动配置。

后端池定义如何通过运行状况探测器评估不同的后端。 它还定义了它们之间的负载平衡方式。

### <a name="health-probes"></a>运行状况探测
前门定期向每个配置的后端发送 HTTP/HTTPS 探测请求。 探测请求确定每个后端的接近性和运行状况，以平衡最终用户请求的负载。 后端池的运行状况探测设置定义我们如何轮询应用后端的运行状况。 以下设置可用于负载平衡配置：

- **路径**：用于后台池中所有后端的探测请求的 URL。 例如，如果其中一个后端contoso-westus.azurewebsites.net并且路径设置为 /probe/test.aspx，则前门环境（假设协议设置为 HTTP）将向 http\://contoso-westus.azurewebsites.net/probe/test.aspx发送运行状况探测请求。

- **协议**：定义是否使用 HTTP 或 HTTPS 协议将运行状况探测请求从前门发送到后端。

- **方法**：用于发送运行状况探测器的 HTTP 方法。 选项包括 GET 或 HEAD（默认）。
    > [!NOTE]
    > 为了降低后端的负载和成本，前门建议使用 HEAD 请求进行运行状况探头。

- **间隔（秒）：** 定义运行状况探测器到后端的频率，或每个前门环境发送探测器的间隔。

    >[!NOTE]
    >要加快故障转移速度，将间隔设置为较低的值。 值越低，后端接收的运行状况探测卷就越高。 例如，如果间隔设置为 30 秒，假设为 100 个前门持久性有机污染物，则每个后端每分钟将收到大约 200 个探测请求。

有关详细信息，请参阅[运行状况探测器](front-door-health-probes.md)。

### <a name="load-balancing-settings"></a>负载平衡设置
后端池的负载平衡设置定义我们如何评估运行状况探测器。 这些设置确定后端是正常还是不正常。 它们还会检查如何在后端池中的不同后端之间负载平衡流量。 以下设置可用于负载平衡配置：

- **样本大小**。 确定需要考虑多少健康探测样本进行后端运行状况评估。

- **成功的样本大小**。 定义如前面提到的样本大小，调用后端正常运行所需的成功样本数。 例如，假设前门运行状况探测间隔为 30 秒，样本大小为 5，成功样本大小为 3。 每次评估后端的健康探测器时，我们都会查看最后 5 个样本，超过 150 秒（5 x 30）。 至少需要三个成功的探测器才能声明后端为健康。

- **延迟敏感度（额外延迟）。** 定义是希望前门将请求发送到延迟测量敏感范围内的后端，还是将请求转发到最近的后端。

有关详细信息，请参阅[基于延迟的最小路由方法](front-door-routing-methods.md#latency)。

## <a name="next-steps"></a>后续步骤

- [创建 Front Door 配置文件](quickstart-create-front-door.md)
- [前门的工作原理](front-door-routing-architecture.md)