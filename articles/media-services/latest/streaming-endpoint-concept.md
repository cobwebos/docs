---
title: 流式处理终结点（源）
titleSuffix: Azure Media Services
description: 了解流式处理终结点 (源) ，这是一个动态打包和流式处理服务，可直接将内容传递给客户端播放器应用程序或 (CDN) 的内容交付网络。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: inhenkel
ms.openlocfilehash: 9f17e8a09715ce9ff51715f17a449ec0a5b3f770
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89297189"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Azure 媒体服务中的流式处理终结点（来源）

在 Microsoft Azure 媒体服务中， [流式处理终结点](/rest/api/media/streamingendpoints) 代表一种动态 (实时) 打包和源服务，它可以使用一种常见的流式媒体协议 (HLS 或破折号) 将实时内容和点播内容直接传送到客户端播放器应用。 此外，流式处理终结点  还为行业领先的 DRM 提供动态（实时）加密。 

用户创建媒体服务帐户时，将为用户创建一个处于“已停止”状态的默认  流式处理终结点。 无法删除**默认**流式处理终结点。 可以在帐户下创建更多的流式处理终结点（请参阅[配额和限制](limits-quotas-constraints.md)）。

> [!NOTE]
> 若要开始流式处理视频，需启动要从中流式处理视频的**流式处理终结点**。
>
> 仅当流式处理终结点处于运行状态时才进行计费。

请确保同时查看[动态打包](dynamic-packaging-overview.md)主题。 

## <a name="naming-convention"></a>命名约定

流式处理 URL 的主机名格式为：`{servicename}-{accountname}-{regionname}.streaming.media.azure.net`，其中 `servicename` = 流式处理终结点名称或实时事件名称。

使用默认的流式处理终结点时，将省略 `servicename`，因此 URL 为：`{accountname}-{regionname}.streaming.azure.net`。

### <a name="limitations"></a>限制

* 流式处理终结点名称的最大值为 24 个字符。
* 该名称应遵循此[正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)模式：`^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`。

## <a name="types"></a>类型

有两种类型的**流式处理终结点**：**标准**（预览版）和**高级**。 类型由用户为流式处理终结点分配的缩放单元（`scaleUnits`）数定义。

最大流单元限制通常为 10。 请 [在此处](https://azure.microsoft.com/support/create-ticket/) 与我们联系，为你的帐户增加限制。

下表描述了类型：

|类型|缩放单元|说明|
|--------|--------|--------|  
|**标准**|0|默认的流式处理终结点是“标准”类型，可以通过调整 `scaleUnits` 更改为“高级”类型。 |
|**高级**|>0|“高级”  流式处理终结点适用于高级工作负荷，可提供专用且可缩放的带宽容量。 可以通过调整 `scaleUnits`（流单元）转到“高级”  类型。 `scaleUnits` 提供专用的出口容量，可以按照 200 Mbps 的增量购买。 使用高级  类型时，每个启用的单元都为应用提供额外的带宽容量。 |

> [!NOTE]
> 对于希望将内容传递给大型 internet 受众的客户，我们建议你在流式处理终结点上启用 CDN。

有关 SLA 的信息，请参阅[定价和 SLA](https://azure.microsoft.com/pricing/details/media-services/)。

## <a name="comparing-streaming-types"></a>比较流式处理类型

功能|标准|高级
---|---|---
吞吐量 |使用 CDN 时，高达 600 Mbps，可以提供更高的有效吞吐量。|每个流单元 (SU) 200 Mbps。 使用 CDN 时，可以提供更高的有效吞吐量。
CDN|Azure CDN、第三方 CDN 或没有 CDN。|Azure CDN、第三方 CDN 或没有 CDN。
按比例计费| 每日|每日
动态加密|是|是
动态打包|是|是
缩放|自动扩展到目标吞吐量。|更多的 SU
IP 筛选/G20/自定义主机 <sup>1</sup>|是|是
渐进式下载|是|是
建议用途 |建议用于绝大多数流式处理方案。|专业用途。

<sup>1</sup> 仅当未在终结点上启用 CDN 时，才直接在流式处理终结点上使用。<br/>

## <a name="streaming-endpoint-properties"></a>流式处理终结点属性

本部分提供有关某些流式处理终结点属性的详细信息。 有关如何创建新流式处理终结点和所有属性描述的示例，请参阅[流式处理终结点](/rest/api/media/streamingendpoints/create)。

- `accessControl`：用于为此流式处理终结点配置以下安全设置：Akamai 签名标头身份验证密钥和允许连接到此终结点的 IP 地址。 仅当设置为 false 时，才能设置此属性 `cdnEnabled` 。

- `cdnEnabled`：指示是否启用此流式处理终结点的 Azure CDN 集成 (默认情况下) 禁用。 如果将 `cdnEnabled` 设置为 true，则会禁用以下配置：`customHostNames` 和 `accessControl`。

    并非所有数据中心都支持 Azure CDN 集成。 若要检查你的数据中心是否具有可用 Azure CDN 集成，请执行以下步骤：

  - 尝试将 `cdnEnabled` 设置为 true。
  - 检查 (Httpstatuscode.preconditionfailed) 的返回结果，该 `HTTP Error Code 412` 消息的 "流式处理终结点 CdnEnabled" 属性不能设置为 true，因为 CDN 功能在当前区域中不可用。

    如果出现此错误，则数据中心不支持此错误。 请尝试另一个数据中心。

- `cdnProfile`：当 `cdnEnabled` 设置为 true 时，还可以传递 `cdnProfile` 值。 `cdnProfile` 是将在其中创建 CDN 终结点的 CDN 配置文件的名称。 可以提供现有的 cdnProfile 或使用新的 cdnProfile。 如果值为 NULL 且 `cdnEnabled` 为 true，则使用默认值“AzureMediaStreamingPlatformCdnProfile”。 如果提供的 `cdnProfile` 已经存在，则在其下创建一个终结点。 如果该配置文件不存在，将自动创建一个新的配置文件。
- `cdnProvider`：启用 CDN 后，还可以传递 `cdnProvider` 值。 `cdnProvider` 控制将使用哪个提供程序。 目前，支持三个值：“StandardVerizon”、“PremiumVerizon”和“StandardAkamai”。 如果未提供任何值且 `cdnEnabled` 为 true，则使用 "StandardVerizon" 作为默认值)  (。
- `crossSiteAccessPolicies`：用于为各种客户端指定跨站点访问策略。 有关详细信息，请参阅[跨域策略文件规范](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)和[提供跨域边界的服务](/previous-versions/azure/azure-services/gg185950(v=azure.100))。 这些设置仅适用于平滑流式处理。
- `customHostNames`：用于配置流式处理终结点以接受定向到自定义主机名的流量。 此属性对标准和高级流式处理终结点有效，可以在以下情况下设置 `cdnEnabled` ： false。

    域名的所有权必须由媒体服务确认。 媒体服务通过要求将包含媒体服务帐户 ID 的 `CName` 记录作为组件添加到正在使用的域来验证域名所有权。 例如，要将“sports.contoso.com”用作流式处理终结点的自定义主机名，则必须将 `<accountId>.contoso.com` 的记录配置为指向其中一个媒体服务验证主机名。 验证主机名由 verifydns.\<mediaservices-dns-zone> 组成。

    下面是要在不同 Azure 区域的验证记录中使用的预期 DNS 区域。
  
  - 北美、欧洲、新加坡、香港特别行政区、日本：

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - 中国：

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    例如，将 `CName` "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" 映射到 "verifydns.media.azure.net" 的记录证明了媒体服务 ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad 拥有 contoso.com 域的所有权，因此，可将 contoso.com 下的任何名称用作该帐户下的流式处理终结点的自定义主机名。 若要查找媒体服务 ID 值，请转至 [Azure 门户](https://portal.azure.com/)，然后选择你的媒体服务帐户。 “帐户 ID”显示在页面的右上方。 

    如果尝试在没有正确验证 `CName` 记录的情况下设置自定义主机名，则 DNS 响应将失败，然后缓存一段时间。 拥有适当的记录后，可能需要一段时间才能重新验证缓存的响应。 根据自定义域的 DNS 提供程序，重新验证记录需要几分钟到一个小时的时间。

    除了将 `<accountId>.<parent domain>` 映射到 `verifydns.<mediaservices-dns-zone>` 的 `CName`，还必须创建另一个 `CName`，以将自定义主机名（例如 `sports.contoso.com`）映射到媒体服务流式处理终结点的主机名（例如 `amstest-usea.streaming.media.azure.net`）。

    > [!NOTE]
    > 位于同一数据中心的流式处理终结点不能共享相同的自定义主机名。

    当前，对于自定义域，媒体服务不支持 TLS。

- `maxCacheAge` - 替代媒体片段和按需清单上的流式处理终结点设置的默认 max-age HTTP 缓存控制标头。 该值以秒为单位进行设置。
- `resourceState` -

    - Stopped：创建后的流式处理终结点的初始状态
    - Starting：正在转换为运行状态
    - Running：可将内容流式传输到客户端
    - Scaling：缩放单元正在增加或减少
    - Stopping：正在转换到停止状态
    - Deleting：正在删除

- `scaleUnits`：提供专用的出口容量，可以按照 200 Mbps 的增量购买。 如果需要转到高级  类型，请调整 `scaleUnits`。

## <a name="why-use-multiple-streaming-endpoints"></a>为何要使用多个流式处理终结点？

单个流式处理终结点可以同时流式传输直播视频和点播视频，大多数客户只使用一个流式处理终结点。 本部分提供了一些示例，说明了为什么可能需要使用多个流式处理终结点。

* 每个预留单位都允许 200 Mbps 的带宽。 如果需要的带宽超过 2,000 Mbps (2 Gbps)，则可使用第二个流式处理终结点和负载均衡来提供额外的带宽。

    但是，CDN 是实现流式处理内容扩大的最佳方法，但如果你要传递的内容太多，CDN 需要超过 2 Gbps，则可以将其他流式处理终结点添加 (源) 。 在这种情况下，需要将在两个流式处理终结点之间平衡的内容 Url 进行分发。 此方法比尝试将请求随机发送到每个源 (例如，通过流量管理器) 提供更好的缓存。 
    
    > [!TIP]
    > 通常情况下，如果 CDN 请求的数量超过 2 Gbps，则可能是配置错误 (例如，) 没有源屏蔽。
    
* 对不同 CDN 提供程序进行负载平衡。 例如，可以设置默认的流式处理终结点以使用 Verizon CDN，并创建另一个使用 Akamai 的流式处理终结点。 然后在二者之间添加一些负载平衡，以实现多 CDN 平衡。 

    但是，客户通常使用单个源跨多个 CDN 提供程序进行负载均衡。
* 流式传输混合内容：直播视频和点播视频。 

    直播内容和点播内容的访问模式有很大差别。 直播内容往往会同时收到对同一内容的大量需求。 视频点播内容（例如，长尾存档内容）对同一内容的使用率较低。 因此，缓存非常适合直播内容，但不适合长尾内容。

    假设你的客户主要观看直播内容，但只是偶尔观看点播内容，而且是从同一个流式处理终结点接受服务。 低使用率的点播内容会占用缓存空间，这些空间本来可以更好地节省给直播内容使用。 在这种情况下，建议你从一个流式处理终结点提供直播内容，从另一个流式处理终结点提供长尾内容。 这将提高直播活动内容的性能。
    
## <a name="scaling-streaming-with-cdn"></a>使用 CDN 缩放流式处理

请参阅下列文章：

- [CDN 概述](../../cdn/cdn-overview.md)
- [使用 CDN 缩放流式处理](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>提出问题并获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="see-also"></a>另请参阅

[动态打包](dynamic-packaging-overview.md)

## <a name="next-steps"></a>后续步骤

[管理流式处理终结点](manage-streaming-endpoints-howto.md)
