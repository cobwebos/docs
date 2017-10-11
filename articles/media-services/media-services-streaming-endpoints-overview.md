---
title: "Azure 媒体服务流式处理终结点概述 | Microsoft 文档"
description: "本主题提供 Azure 媒体服务流式处理终结点的概述。"
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: cfowler
editor: 
ms.assetid: 097ab5e5-24e1-4e8e-b112-be74172c2701
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: e454778c558b9c17c47ad9eb651737aa0b5e2605
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="streaming-endpoints-overview"></a>流式处理终结点概述 

##<a name="overview"></a>概述

在 Microsoft Azure 媒体服务 (AMS) 中，**流式处理终结点**表示一个流服务，该服务可以直接将内容传递给客户端播放器应用程序，也可以传递给内容支付网络 (CDN) 以进一步分发。 媒体服务还提供无缝 Azure CDN 集成。 StreamingEndpoint 服务的出站流可以是媒体服务帐户中的实时流、点播视频或渐进式下载的资产。 每个 Azure 媒体服务帐户均包括一个默认的流式处理终结点。 可以在帐户下创建其他流式处理终结点。 有两个版本的流式处理终结点：1.0 和 2.0。 从 2017 年 1 月 10 日开始，任何新创建的 AMS 帐户将包括版本 2.0 的**默认**流式处理终结点。 可添加到此帐户的其他流式处理终结点也是版本 2.0。 此更改不会影响现有帐户；现有流式处理终结点将是版本 1.0，可以升级到版本 2.0。 此更改将导致行为、计费和功能更改（有关详细信息，请参阅下面所述的**流式处理类型和版本**部分）。

此外，从 2.15 版本（已于 2017 年 1 月发布）开始，Azure 媒体服务对于流式处理终结点实体添加了以下属性：**CdnProvider**、**CdnProfile**、**FreeTrialEndTime**、**StreamingEndpointVersion**。 有关这些属性的详细概述，请参阅[此文](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)。 

用户创建 Azure 媒体服务帐户时，将为用户创建一个处于“已停止”状态的默认标准流式处理终结点。 无法删除默认流式处理终结点。 根据目标区域中的 Azure CDN 可用性，默认情况下新创建的默认流式处理终结点还提供“StandardVerizon”CDN 提供程序集成。 

>[!NOTE]
>可以在启动流式处理终结点之前禁用 Azure CDN 集成。

本主题概述了流式处理终结点提供的主要功能。

## <a name="streaming-types-and-versions"></a>流式处理类型和版本

### <a name="standardpremium-types-version-20"></a>标准/高级类型（版本 2.0）

从 2017 年 1 月发布的媒体服务开始，可使用两种流式处理类型：**标准**和**高级**。 这些类型属于流式处理终结点版本“2.0”。

类型|说明
---|---
**标准**|这是适用于大多数方案的默认选项。<br/>使用此选项，可以获得固定/有限的 SLA，在启动流式处理终结点后的前 15 天是免费的。<br/>如果创建多个流式处理终结点，只有第一个流式处理终结点在前 15 天是免费的，其他流式处理终结点在启动后立即就会计费。 <br/>请注意，免费试用仅适用于新创建的媒体服务帐户和默认的流式处理终结点。 现有的流式处理终结点和另外创建的流式处理终结点不包括免费试用期，即使将它们升级到版本 2.0 或创建为版本 2.0，也是如此。
**高级**|此选项适用于需要更高级缩放或控制的专业方案。<br/>选择此选项则会使用基于已购买高级流单元 (SU) 容量的可变 SLA，专用流式处理终结点存在于隔离环境中，并不争用资源。

有关更多详细信息，请参阅以下部分**比较流式处理类型**。

### <a name="classic-type-version-10"></a>经典类型（版本 1.0）

对于在 2017 年 1 月 10 日发行版之前创建 AMS 帐户的用户，可以使用**经典**类型的流式处理终结点。 此类型属于流式处理终结点版本“1.0”。

如果**“1.0”版**的流式处理终结点的高级流单元 (SU) 数 > = 1，则它将是高级流式处理终结点，并且无需任何其他配置步骤就会提供所有 AMS 功能（就像**标准/高级**类型一样）。

>[!NOTE]
>**经典**流式处理终结点（版本“1.0”且 0 个 SU）提供有限的功能，并且不包括 SLA。 建议迁移到“标准”类型，以便改进体验，并使用动态打包或加密等功能，以及“标准”类型附带的其他功能。 若要迁移到**标准**类型，请转到 [Azure 门户](https://portal.azure.com/)，选择“选择加入标准类型”。 有关迁移的详细信息，请参阅[迁移](#migration-between-types)部分。
>
>请注意，此操作无法回退，并且会对定价有影响。
>
 
## <a name="comparing-streaming-types"></a>比较流式处理类型

### <a name="versions"></a>版本

|类型|StreamingEndpointVersion|ScaleUnits|CDN|计费|SLA| 
|--------------|----------|-----------------|-----------------|-----------------|-----------------|    
|经典|1.0|0|不可用|免费|不可用|
|标准流式处理终结点|2.0|0|是|付费|是|
|高级流单元|1.0|>0|是|付费|是|
|高级流单元|2.0|>0|是|付费|是|

### <a name="features"></a>功能

功能|标准|高级
---|---|---
前 15 天免费| 是 |否
吞吐量 |未使用 Azure CDN 时，最多可达 600 Mbps。 使用 CDN 进行缩放。|每个流单元 (SU) 200 Mbps。 使用 CDN 进行缩放。
SLA | 99.9|99.9（每个 SU 200 Mbps）。
CDN|Azure CDN、第三方 CDN 或没有 CDN。|Azure CDN、第三方 CDN 或没有 CDN。
按比例计费| 每日|每日
动态加密|是|是
动态打包|是|是
缩放|自动增加到目标吞吐量。|额外流单元
IP 筛选/G20/自定义主机|是|是
渐进式下载|是|是
建议的用法 |建议用于绝大多数的流式处理方案。|专业用法。<br/>如果认为需求可能会超出标准。 如果预计并发受众大小大于 50,000 个查看器，请与我们联系（microsoft.com 上的 amsstreaming）。


## <a name="migration-between-types"></a>类型之间的迁移

从 | 如果 | 操作
---|---|---
经典|标准|需要选择加入
经典|高级| 缩放（额外流单元）
标准/高级|经典|不可用（如果流式处理终结点版本为 1.0， 允许通过将 scaleunits 设置为“0”更改为经典）
标准（带/不带 CDN）|具有相同配置的高级类型|在**已启动**状态下允许。 （通过 Azure 门户）
高级（带/不带 CDN）|具有相同配置的标准类型|在**已启动**状态下允许（通过 Azure 门户）
标准（带/不带 CDN）|具有不同配置的高级类型|在**已停止**状态下允许（通过 Azure 门户）。 在“正在运行”状态下不允许。
高级（带/不带 CDN）|具有不同配置的标准类型|在**已停止**状态下允许（通过 Azure 门户）。 在“正在运行”状态下不允许。
版本 1.0 且 SU >= 1，使用 CDN|不带 CDN 的标准/高级类型|在**已停止**状态下允许。 在**已启动**状态下不允许。
版本 1.0 且 SU >= 1，使用 CDN|标准（带/不带 CDN）|在**已停止**状态下允许。 在**已启动**状态下不允许。 将删除 1.0 版 CDN，然后创建新的 CDN 并将其启动。
版本 1.0 且 SU >= 1，使用 CDN|高级（带/不带 CDN）|在**已停止**状态下允许。 在**已启动**状态下不允许。 将删除经典 CDN，然后创建新的 CDN 并将其启动。

## <a name="next-steps"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

