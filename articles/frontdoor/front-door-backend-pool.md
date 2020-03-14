---
title: Azure 前门服务中的后端和后端池 |Microsoft Docs
description: 本文介绍前门配置中的后端和后端池。
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
ms.openlocfilehash: b764799d3f40cef24a0412ac950026af650d4ec7
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272846"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Azure Front Door 服务中的后端和后端池
本文介绍有关如何将应用部署与 Azure 前门服务进行映射的概念。 它还说明了应用后端的前端配置中的不同术语。

## <a name="backends"></a>后端
后端等于某个区域中的应用部署实例。 前门服务支持 Azure 和非 Azure 后端，因此此区域不仅限于 Azure 区域。 此外，它还可以是本地数据中心或其他云中的应用程序实例。

前门服务后端引用可为客户端请求提供服务的应用的主机名或公共 IP。 不应将后端与数据库层、存储层等混淆。 应将后端视为应用后端的公共终结点。 在前门后端池中添加后端时，还必须添加以下内容：

- **后端主机类型**。 要添加的资源的类型。 前门服务支持从应用服务、云服务或存储中自动发现应用后端。 如果需要 Azure 中的其他资源，甚至是非 Azure 后端，请选择 "**自定义主机**"。

    >[!IMPORTANT]
    >在配置期间，如果不能从前门环境访问后端，则 Api 不会验证。 请确保前门可以访问后端。

- **订阅和后端主机名**。 如果尚未选择 "**自定义主机**作为后端主机类型"，请选择相应的订阅，并在 UI 中选择相应的后端主机名。

- **后端主机标头**。 为每个请求发送到后端的主机标头值。 有关详细信息，请参阅[后端主机标头](#hostheader)。

- **优先级**。 如果要对所有流量使用主服务后端，请将优先级分配给不同的后端。 此外，如果主副本或备份后端不可用，请提供备份。 有关详细信息，请参阅[Priority](front-door-routing-methods.md#priority)。

- **权重**。 将权重分配给不同的后端，以将流量分配到一组后端上，不管是平均分配还是根据权重系数分配流量。 有关详细信息，请参阅[权重](front-door-routing-methods.md#weighted)。

### <a name = "hostheader"></a>后端主机标头

由前门转发到后端的请求包括一个主机标头字段，后端使用它来检索目标资源。 此字段的值通常来自后端 URI，具有主机和端口。

例如，为 www\.contoso.com 发出的请求将具有主机标头 www\.contoso.com。 如果使用 Azure 门户配置后端，则此字段的默认值为后端的主机名。 如果后端为 contoso-westus.azurewebsites.net，则在 Azure 门户中，后端主机标头的会自动填充值将为 contoso-westus.azurewebsites.net。 但是，如果使用 Azure 资源管理器模板或其他方法而不显式设置此字段，则前门服务将发送传入的主机名作为主机标头的值。 如果对 www\.contoso.com 发出请求，并且后端 contoso-westus.azurewebsites.net 具有空的标头字段，则前门服务会将主机标头设置为 www\.contoso.com。

大多数应用后端（Azure Web 应用、Blob 存储和云服务）都需要主机标头来匹配后端的域。 但是，路由到后端的前端主机将使用不同的主机名，例如 www\.contoso.azurefd.net。

如果后端需要主机标头来匹配后端主机名，请确保后端主机标头包含主机名后端。

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>配置后端的后端主机标头

在后端池部分为后端配置**后端主机标头**字段：

1. 打开前门资源，并选择后端要配置的后端池。

2. 如果尚未执行此操作，请添加后端，或编辑现有后端。

3. 将 "后端主机标头" 字段设置为自定义值，或将其留空。 传入请求的主机名将用作主机标头值。

## <a name="backend-pools"></a>后端池
前门服务中的后端池是指为其应用接收类似流量的一组后端。 换句话说，它是世界各地的应用程序实例的逻辑分组，可接收相同的流量并响应预期的行为。 这些后端部署在不同的区域或同一区域中。 所有后端可以处于主动/主动部署模式下，也可以定义为主动/被动配置。

后端池定义了如何通过运行状况探测来计算不同的后端。 它还定义了负载平衡之间的发生方式。

### <a name="health-probes"></a>运行状况探测
前门服务将定期 HTTP/HTTPS 探测请求发送到每个已配置的后端。 探测请求会确定每个后端的邻近性和运行状况以对最终用户请求进行负载平衡。 后端池的运行状况探测设置定义我们如何轮询 app 后端的运行状况状态。 以下设置可用于负载平衡配置：

- **Path**。 用于对后端池中所有后端的探测请求的 URL。 例如，如果你的某个后端为 contoso-westus.azurewebsites.net，并且路径设置为/probe/test.aspx，则在将协议设置为 HTTP 时，前门服务环境（假定该协议设置为 HTTP）将向 http\://contoso-westus.azurewebsites.net/probe/test.aspx 发送运行状况探测请求。

- **协议**。 定义是否通过 HTTP 或 HTTPS 协议将前端服务的运行状况探测请求发送到后端。

- **间隔（秒）** 。 定义后端的运行状况探测频率，或每个前端环境发送探测的间隔。

    >[!NOTE]
    >为了更快地进行故障转移，请将间隔设置为较小的值。 值越低，后端接收到的运行状况探测量就越高。 例如，如果将时间间隔设置为30秒，并显示90前门环境或全局弹出，则每个后端每秒收到大约3-5 探测请求。

有关详细信息，请参阅[运行状况探测](front-door-health-probes.md)。

### <a name="load-balancing-settings"></a>负载平衡设置
后端池的负载平衡设置定义我们评估运行状况探测的方式。 这些设置确定后端是否正常运行。 它们还检查如何在后端池中的不同后端之间对流量进行负载均衡。 以下设置可用于负载平衡配置：

- **样本大小**。 标识为后端运行状况评估需要考虑的运行状况探测示例数。

- **样本大小成功**。 定义前面提到的示例大小，即调用后端所需的成功样本的数目。 例如，假定前门运行状况探测间隔为30秒，样本大小为5，成功的样本大小为3。 每次评估后端的运行状况探测时，我们都会查看过去5个样本，超过150秒（5 x 30）。 需要至少三个成功的探测，才能将后端声明为正常。

- **延迟敏感度（附加延迟）** 。 定义是否希望前门在延迟测量敏感度范围内将请求发送到后端，或将请求转发到最近的后端。

有关详细信息，请参阅[基于延迟的最小路由方法](front-door-routing-methods.md#latency)。

## <a name="next-steps"></a>后续步骤

- [创建前门配置文件](quickstart-create-front-door.md)
- [前门的工作方式](front-door-routing-architecture.md)