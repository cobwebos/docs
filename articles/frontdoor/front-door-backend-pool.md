---
title: 在 Azure 第一道防线服务中的后端和后端池 |Microsoft Docs
description: 本文介绍哪些后端和后端池是在 Front 门的配置。
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
ms.openlocfilehash: 543e237a4a8390a8ebf74d0eb2a1f4be41dcd911
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193704"
---
# <a name="backends-and-backend-pools-in-azure-front-door-service"></a>Azure Front Door 服务中的后端和后端池
本文介绍有关如何将应用部署使用 Azure 第一道防线服务映射的概念。 它还介绍了不同的术语，围绕应用后端的第一道防线配置中。

## <a name="backends"></a>后端
后端相当于区域中的应用程序的部署实例。 第一道防线服务支持 Azure 和非 Azure 后端，因此，在区域不仅限制到 Azure 区域。 此外，它可以是本地数据中心或其他云应用程序实例。

第一道防线服务后端引用主机名或您的应用程序，可以为客户端请求提供服务的公共 IP。 后端不应混淆和数据库层、 存储层中，依次类推。 后端应被视为应用后端的公共终结点。 当在第一道防线后端池中添加后端时，还必须添加以下：

- **后端主机类型**。 你想要添加的资源的类型。 第一道防线服务支持从应用服务、 云服务或存储在应用后端的自动发现。 如果您希望不同的资源在 Azure 或甚至非 Azure 后端中，选择**自定义主机**。

    >[!IMPORTANT]
    >在配置期间，如果从第一道防线环境无法访问后端不验证 Api。 请确保第一道防线，可以联系你的后端。

- **订阅和后端主机名**。 如果你尚未选择**自定义主机**对于后端主机类型，选择你的后端通过在 UI 中选择相应的订阅和相应的后端主机名称。

- **后端主机标头**。 发送到每个请求的后端的主机标头值。 有关详细信息，请参阅[后端主机标头](#hostheader)。

- **优先级**。 当你想要对所有流量使用主服务后端时，请将优先级分配给不同的后端。 此外，提供备份，如果主或备份后端都不可用。 有关详细信息，请参阅[优先级](front-door-routing-methods.md#priority)。

- **权重**。 将权重分配到不同后端以将流量分配到后端，一组，是平均分配还是根据权重系数。 有关详细信息，请参阅[权重](front-door-routing-methods.md#weighted)。

### <a name = "hostheader"></a>后端主机标头

第一道防线转发到后端的请求包括后端使用要检索其目标的资源的主机标头字段。 此字段的值通常来自后端 URI，具有主机和端口。

例如，为 www 发出的请求\.contoso.com 将具有主机标头 www\.contoso.com。 如果使用 Azure 门户来配置你的后端，此字段的默认值是后端的主机名。 如果你的后端是 contoso westus.azurewebsites.net，在 Azure 门户中，会自动填充为后端主机标头将 contoso westus.azurewebsites.net。 但是，如果没有显式设置此字段中使用 Azure 资源管理器模板或另一种方法，第一道防线服务将发送传入的主机名作为值主机标头。 如果已请求为 www\.contoso.com 和你的后端是 contoso westus.azurewebsites.net 具有空标头字段，第一道防线服务会将主机标头设置为 www\.contoso.com。

大多数应用后端 （Azure Web 应用、 Blob 存储和云服务） 需要与后端的域匹配的主机标头。 但是，将路由到后端的前端主机将使用不同的主机名，例如 www\.contoso.azurefd.net。

如果你的后端需要与后端主机名匹配的主机标头，请确保后端主机标头，包括主机名称后端。

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>配置后端的后端主机标头

若要配置**后端主机标头**字段中为后端池部分中的后端：

1. 打开你的第一道防线资源，并选择与后端配置的后端池。

2. 如果尚未执行此操作，或编辑现有，添加后端。

3. 将后端主机标头字段设置为自定义值或将其留空。 传入请求的主机名用作主机标头值。

## <a name="backend-pools"></a>后端池
后端池在 Front 门服务是指接收为其应用程序的类似流量的后端的组。 换而言之，它是接收相同的流量，并使用预期的行为进行响应在世界各地的应用程序实例的逻辑分组。 这些后端将部署跨不同区域或相同区域内。 所有后端可以是主动/主动部署模式或主动/被动配置为定义的内容。

后端池定义应如何通过运行状况探测评估不同的后端。 它还定义负载平衡它们之间的发生方式。

### <a name="health-probes"></a>运行状况探测
第一道防线服务将定期 HTTP/HTTPS 探测请求发送到每个配置后端。 探测请求确定邻近和运行状况的每个后端加载平衡最终用户请求。 运行状况探测设置的后端池定义我们如何轮询的应用后端运行状况状态。 以下设置都可用于负载平衡配置：

- **路径**。 用于后端池中的所有后端的探测请求的 URL。 例如，如果其中一个后端是 contoso westus.azurewebsites.net 并将路径设置为 /probe/test.aspx，然后第一道防线服务环境中，假定将协议设置为 HTTP，将运行状况探测将请求发送到 http\:/ / contoso-westus.azurewebsites.net/probe/test.aspx。

- **协议**。 定义是否将从第一道防线服务的运行状况探测请求发送到后端使用 HTTP 或 HTTPS 协议。

- **间隔 （秒）**。 定义后端或在其中每个第一道防线环境发送探测的时间间隔运行状况探测的频率。

    >[!NOTE]
    >为故障转移更加快速，将间隔设置为较低的值。 越低的值，更高版本的运行状况探测卷后端接收。 例如，如果间隔全局设置为 90 的第一道防线环境或 Pop 30 秒，则每个后端将收到有关 3-5 探测每秒请求数。

有关详细信息，请参阅[运行状况探测](front-door-health-probes.md)。

### <a name="load-balancing-settings"></a>负载平衡设置
后端池的负载平衡设置定义我们如何评估运行状况探测。 这些设置确定在后端是否正常运行或不正常。 它们还可检查如何不同后端中的后端池之间的流量负载均衡。 以下设置都可用于负载平衡配置：

- **样本大小**。 标识我们需要考虑进行后端运行状况评估的运行状况探测的多少样本。

- **成功的样本大小**。 定义样本大小按前面所述，成功调用后端的正常运行所需的样本数。 例如，假定第一道防线运行状况探测间隔为 30 秒、 样本大小为 5，且成功的样本大小为 3。 每次我们评估运行状况探测后端，我们看一下最后五个样本超过 150 秒 (5 x 30)。 至少三个成功探测所需声明为正常的后端。

- **延迟敏感性 （额外延迟）**。 定义您是否希望第一道防线，以将请求发送到后端延迟度量敏感度范围内或将请求转发到最接近的后端。

有关详细信息，请参阅[最少的延迟基于路由方法](front-door-routing-methods.md#latency)。

## <a name="next-steps"></a>后续步骤

- [创建的第一道防线配置文件](quickstart-create-front-door.md)
- [第一道防线的工作原理](front-door-routing-architecture.md)