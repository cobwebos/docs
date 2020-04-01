---
title: 流式处理终结点（源）
titleSuffix: Azure Media Services
description: 了解流式处理端点 （原点），这是一种动态打包和流式处理服务，可直接将内容传递到客户端播放器应用或内容交付网络 （CDN）。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: a8674714451f3cc686acb74d6c66ac6800b75936
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478082"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Azure 媒体服务中的流式处理终结点（来源）

在 Microsoft Azure 媒体服务中，[流式处理终结点](https://docs.microsoft.com/rest/api/media/streamingendpoints)表示动态（即时）打包和源服务，该服务可以使用一种常见的流媒体协议 （HLS 或 DASH） 将实时和按需内容直接传递到客户端播放器应用。 此外，**流式处理终结点**为行业领先的 DRM 提供动态（及时）加密。 

用户创建媒体服务帐户时，将为用户创建一个处于“已停止”状态的默认**** 流式处理终结点。 无法删除**默认**流式处理终结点。 可以在帐户下创建更多的流式处理终结点（请参阅[配额和限制](limits-quotas-constraints.md)）。

> [!NOTE]
> 要开始流式传输视频，您需要从中启动流**式处理终结点**，以便从中流式传输视频。
>
> 仅当流式处理终结点处于运行状态时才进行计费。

请确保还要查看[动态打包](dynamic-packaging-overview.md)主题。 

## <a name="naming-convention"></a>命名约定

流式处理 URL 的主机名格式为：`{servicename}-{accountname}-{regionname}.streaming.media.azure.net`，其中 `servicename` = 流式处理终结点名称或实时事件名称。

使用默认的流式处理终结点时，将省略 `servicename`，因此 URL 为：`{accountname}-{regionname}.streaming.azure.net`。

### <a name="limitations"></a>限制

* 流式处理终结点名称的最大值为 24 个字符。
* 该名称应遵循此[正则表达式](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)模式：`^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`。

## <a name="types"></a>类型

有两种**流式处理终结点**类型：**标准**（预览）和**高级**。 类型由用户为流式处理终结点分配的缩放单元（`scaleUnits`）数定义。

下表描述了类型：

|类型|缩放单元|说明|
|--------|--------|--------|  
|**标准**|0|默认的流式处理终结点是“标准”类型，可以通过调整 `scaleUnits` 更改为“高级”类型。****|
|**溢价**|>0|“高级”**** 流式处理终结点适用于高级工作负荷，可提供专用且可缩放的带宽容量。 可以通过调整 `scaleUnits`（流单元）转到“高级”**** 类型。 `scaleUnits` 提供专用的出口容量，可以按照 200 Mbps 的增量购买。 使用高级**** 类型时，每个启用的单元都为应用提供额外的带宽容量。 |

> [!NOTE]
> 对于希望向大型互联网受众提供内容的客户，我们建议您在流式处理终结点上启用 CDN。

有关 SLA 的信息，请参阅[定价和 SLA](https://azure.microsoft.com/pricing/details/media-services/)。

## <a name="comparing-streaming-types"></a>比较流式处理类型

Feature|Standard|Premium
---|---|---
吞吐量 |高达 600 Mbps，在使用 CDN 时可提供更高的有效吞吐量。|每个流单元 (SU) 200 Mbps。 在使用 CDN 时，可以提供更高的有效吞吐量。
CDN|Azure CDN、第三方 CDN 或无 CDN。|Azure CDN、第三方 CDN 或无 CDN。
按比例计费| 每日|每日
动态加密|是|是
动态打包|是|是
缩放|自动增加到目标吞吐量。|更多的 SU
IP 过滤/G20/自定义主机<sup>1</sup>|是|是
渐进式下载|是|是
建议的用法 |建议用于绝大多数的流式处理方案。|专业用法。

<sup>1</sup>仅在终结点上未启用 CDN 时直接在流式处理终结点上使用。<br/>

## <a name="streaming-endpoint-properties"></a>流式处理终结点属性

本部分提供有关某些流式处理终结点属性的详细信息。 有关如何创建新流式处理终结点和所有属性描述的示例，请参阅[流式处理终结点](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)。

- `accessControl`： 用于为此流式处理终结点配置以下安全设置：允许连接到此终结点的 Akamai 签名标头身份验证密钥和 IP 地址。 此属性只能在设置为 false 时`cdnEnabled`设置。

- `cdnEnabled`： 指示是否启用了此流式处理终结点的 Azure CDN 集成（默认情况下禁用）。 如果将 `cdnEnabled` 设置为 true，则会禁用以下配置：`customHostNames` 和 `accessControl`。

    并非所有数据中心都支持 Azure CDN 集成。 要检查数据中心是否具有可用的 Azure CDN 集成，请执行以下步骤：

  - 尝试将 `cdnEnabled` 设置为 true。
  - 检查返回的结果（`HTTP Error Code 412`预置失败）与消息"流终结点 CdnEnabled 属性不能设置为 true，因为 CDN 功能在当前区域不可用。

    如果收到此错误，数据中心不支持它。 尝试其他数据中心。

- `cdnProfile`：`cdnEnabled`当设置为 true 时，还可以传递`cdnProfile`值。 `cdnProfile` 是将在其中创建 CDN 终结点的 CDN 配置文件的名称。 可以提供现有的 cdnProfile 或使用新的 cdnProfile。 如果值为 NULL 且 `cdnEnabled` 为 true，则使用默认值“AzureMediaStreamingPlatformCdnProfile”。 如果提供的 `cdnProfile` 已经存在，则在其下创建一个终结点。 如果配置文件不存在，将自动创建新配置文件。
- `cdnProvider`：启用 CDN 后，还可以传递`cdnProvider`值。 `cdnProvider` 控制将使用哪个提供程序。 目前，支持三个值：“StandardVerizon”、“PremiumVerizon”和“StandardAkamai”。 如果未提供任何值且`cdnEnabled`为 true，则使用"标准Verizon"（这是默认值）。
- `crossSiteAccessPolicies`：用于为各种客户端指定跨站点访问策略。 有关详细信息，请参阅[跨域策略文件规范](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)和[提供跨域边界的服务](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx)。 这些设置仅适用于平滑流式处理。
- `customHostNames`： 用于配置流式处理终结点以接受定向到自定义主机名的流量。 此属性对标准和高级流式处理终结点有效，可在 ： false`cdnEnabled`时设置。

    域名的所有权必须由媒体服务确认。 媒体服务通过要求将包含媒体服务帐户 ID 的 `CName` 记录作为组件添加到正在使用的域来验证域名所有权。 例如，要将“sports.contoso.com”用作流式处理终结点的自定义主机名，则必须将 `<accountId>.contoso.com` 的记录配置为指向其中一个媒体服务验证主机名。 验证主机名由 verifydns.\<mediaservices-dns-zone> 组成。

    下面是要在不同 Azure 区域的验证记录中使用的预期 DNS 区域。
  
  - 北美、欧洲、新加坡、香港特别行政区、日本：

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - 中国：

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    例如，将`CName`"945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com"映射到"verifydns.media.azure.net"的记录证明，媒体服务 ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad 拥有contoso.com域的所有权，从而使contoso.com下的任何名称都可用作该帐户下的流端的自定义主机名。 若要查找媒体服务 ID 值，请转至 [Azure 门户](https://portal.azure.com/)，然后选择你的媒体服务帐户。 “帐户 ID”显示在页面的右上方。****

    如果尝试在没有正确验证 `CName` 记录的情况下设置自定义主机名，则 DNS 响应将失败，然后缓存一段时间。 拥有适当的记录后，可能需要一段时间才能重新验证缓存的响应。 根据自定义域的 DNS 提供程序，重新验证记录需要几分钟到一个小时的时间。

    除了将 `<accountId>.<parent domain>` 映射到 `verifydns.<mediaservices-dns-zone>` 的 `CName`，还必须创建另一个 `CName`，以将自定义主机名（例如 `sports.contoso.com`）映射到媒体服务流式处理终结点的主机名（例如 `amstest-usea.streaming.media.azure.net`）。

    > [!NOTE]
    > 位于同一数据中心的流式处理终结点不能共享相同的自定义主机名。

    目前，媒体服务不支持具有自定义域的 TLS。

- `maxCacheAge` - 替代媒体片段和按需清单上的流式处理终结点设置的默认 max-age HTTP 缓存控制标头。 该值以秒为单位进行设置。
- `resourceState` -

    - Stopped：创建后的流式处理终结点的初始状态
    - Starting：正在转换为运行状态
    - Running：可将内容流式传输到客户端
    - Scaling：缩放单元正在增加或减少
    - Stopping：正在转换到停止状态
    - Deleting：正在删除

- `scaleUnits`：为您提供专用出口容量，以 200 Mbps 的增量购买。 如果需要转到高级**** 类型，请调整 `scaleUnits`。

## <a name="why-use-multiple-streaming-endpoints"></a>为什么要使用多个流式处理终结点？

单个流式处理终结点可以同时流式传输实时和点播视频，大多数客户仅使用一个流式处理终结点。 本节给出了一些示例，说明为什么您可能需要使用多个流式处理终结点。

* 每个预留单元允许 200 Mbps 的带宽。 如果您需要超过 2，000 Mbps （2 Gbps） 的带宽，则可以使用第二个流式处理终结点和负载平衡来为您提供额外的带宽。

    但是，CDN 是实现流式处理内容横向扩展的最佳方式，但如果提供的内容太多，CDN 的拉取量超过 2 Gbps，则可以添加其他流式处理终结点（原点）。 在这种情况下，您需要分发跨两个流式处理终结点平衡的内容 URL。 此方法提供比尝试随机向每个源发送请求（例如，通过流量管理器）更好的缓存。 
    
    > [!TIP]
    > 通常，如果 CDN 的拉拉超过 2 Gbps，则某些内容可能配置错误（例如，没有源屏蔽）。
    
* 负载平衡不同的 CDN 提供程序。 例如，您可以将默认流式处理终结点设置为使用 Verizon CDN，并创建第二个流式处理终结点以使用 Akamai。 然后，在两者之间添加一些负载平衡，以实现多 CDN 平衡。 

    但是，客户通常使用单个源在多个 CDN 提供程序之间进行负载平衡。
* 流式传输混合内容：实时和视频点播。 

    实时和按需内容的访问模式非常不同。 实时内容往往同时获得对相同内容的大量需求。 视频点播内容（例如长尾存档内容）对同一内容的用法较低。 因此，缓存在实时内容上效果很好，但在长尾内容上效果不是很好。

    考虑一个方案，您的客户主要观看实时内容，但只是偶尔观看点播内容，并且从同一流终结点提供服务。 按需内容使用率低将占用缓存空间，以更好地为实时内容保存。 在这种情况下，我们建议从一个流式处理终结点提供实时内容，另一个流终结点提供长尾内容。 这将提高实时事件内容的性能。
    
## <a name="scaling-streaming-with-cdn"></a>使用 CDN 扩展流

请参阅以下文章：

- [CDN 概述](../../cdn/cdn-overview.md)
- [使用 CDN 扩展流](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>提出问题并获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="see-also"></a>请参阅

[动态打包](dynamic-packaging-overview.md)

## <a name="next-steps"></a>后续步骤

[管理流式处理终结点](manage-streaming-endpoints-howto.md)
