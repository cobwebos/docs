---
title: Azure 媒体服务流式处理终结点概述 | Microsoft 文档
description: 本文概述了 Azure 媒体服务流式处理终结点。
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: a541e1b068ec3667120bbb31e65ca7bc35febadb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89265909"
---
# <a name="streaming-endpoints-overview"></a>流式处理终结点概述  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> 不会向媒体服务 v2 添加任何新特性或新功能。 <br/>查看最新版本：[媒体服务 v3](../latest/index.yml)。 另请参阅[从 v2 到 v3 的迁移指南](../latest/migrate-from-v2-to-v3.md)

在 Microsoft Azure 媒体服务 (AMS)中，“流式处理终结点”**** 表示一个流服务，该服务可以直接将内容分发给客户端播放器应用程序，也可以传递给内容分发网络 (CDN) 以进一步分发。 媒体服务还提供无缝 Azure CDN 集成。 StreamingEndpoint 服务的出站流可以是实时流、视频点播，也可以是媒体服务帐户中进行的渐进式资产下载。 每个 Azure 媒体服务帐户包括一个默认的 StreamingEndpoint。 可以在该帐户下创建其他 StreamingEndpoint。 StreamingEndpoint 有两个版本：1.0 和 2.0。 从 2017 年 1 月 10 日开始，任何新创建的 AMS 帐户都会包括 2.0 版的 **默认** StreamingEndpoint。 添加到该帐户的其他流式处理终结点也会是 2.0 版。 此更改不会影响现有帐户；现有的 StreamingEndpoint 会是 1.0 版，但可以升级到 2.0 版。 此更改将导致行为、计费和功能更改（有关详细信息，请参阅下面所述的**流式处理类型和版本**部分）。

Azure 媒体服务已将以下属性添加到流式处理终结点实体： **CdnProvider**、 **CdnProfile**、 **StreamingEndpointVersion**。 有关这些属性的详细概述，请参阅 [此文](/rest/api/media/operations/streamingendpoint)。 

用户创建 Azure 媒体服务帐户时，将为用户创建一个处于“已停止”  状态的默认标准流式处理终结点。 无法删除默认流式处理终结点。 根据目标区域中的 Azure CDN 可用性，默认情况下新创建的默认流式处理终结点还提供“StandardVerizon”CDN 提供程序集成。 
                
> [!NOTE]
> 可以在启动流式处理终结点之前禁用 Azure CDN 集成。 无论你是否启用 CDN，`hostname` 和流式处理 URL 都保持不变。

本主题概述流式处理终结点提供的主要功能。

## <a name="naming-conventions"></a>命名约定

对于默认终结点：`{AccountName}.streaming.mediaservices.windows.net`

对于任何其他终结点：`{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>流式处理类型和版本

### <a name="standardpremium-types-version-20"></a>标准/高级类型（版本 2.0）

从 2017 年 1 月发布的媒体服务开始，可使用两种流式处理类型：**标准**（预览版）和**高级**。 这些类型属于流式处理终结点版本“2.0”。


|类型|说明|
|--------|--------|  
|**标准**|默认的流式处理终结点是“标准”  类型，可以通过调整流单元更改为“高级”类型。|
|**高级** |此选项适用于需要更高级缩放或控制的专业方案。 可以通过调整流单元转到“高级”  类型。<br/>专用流式处理终结点存在于隔离环境中，不会争用资源。|

对于希望将内容传递给大型 internet 受众的客户，我们建议你在流式处理终结点上启用 CDN。

有关更多详细信息，请参阅以下部分 [比较流式处理类型](#comparing-streaming-types) 。

### <a name="classic-type-version-10"></a>经典类型（版本 1.0）

对于在 2017 年 1 月 10 日发行版之前创建 AMS 帐户的用户，可以使用 **经典** 类型的流式处理终结点。 此类型属于流式处理终结点版本“1.0”。

如果 **“1.0”版**的流式处理终结点的高级流单元 (SU) 数 > = 1，则它将是高级流式处理终结点，并且无需任何其他配置步骤就会提供所有 AMS 功能（就像**标准/高级**类型一样）。

>[!NOTE]
>**经典**流式处理终结点（版本“1.0”且 0 个 SU）提供有限的功能，并且不包括 SLA。 建议迁移到“标准”  类型，以便改进体验，并使用动态打包或加密等功能，以及“标准”  类型附带的其他功能。 若要迁移到**标准**类型，请转到 [Azure 门户](https://portal.azure.com/)，选择“选择加入标准类型”  。 有关迁移的详细信息，请参阅[迁移](#migration-between-types)部分。
>
>请注意，此操作无法回退，并且会对定价造成影响。
>
 
## <a name="comparing-streaming-types"></a>比较流式处理类型

### <a name="versions"></a>版本

|类型|StreamingEndpointVersion|ScaleUnits|CDN|计费|
|--------------|----------|-----------------|-----------------|-----------------|
|经典|1.0|0|NA|免费|
|标准流式处理终结点（预览版）|2.0|0|是|付费|
|高级流式处理单元|1.0|>0|是|付费|
|高级流式处理单元|2.0|>0|是|付费|

### <a name="features"></a>功能

功能|标准|高级
---|---|---
吞吐量 |使用 CDN 时，高达 600 Mbps，可以提供更高的有效吞吐量。|每个流单元 (SU) 200 Mbps。 使用 CDN 时，可以提供更高的有效吞吐量。
CDN|Azure CDN、第三方 CDN 或没有 CDN。|Azure CDN、第三方 CDN 或没有 CDN。
按比例计费| 每日|每日
动态加密|是|是
动态打包|是|是
缩放|自动扩展到目标吞吐量。|额外流单元。
IP 筛选/G20/自定义主机 <sup>1</sup>|是|是
渐进式下载|是|是
建议用途 |建议用于绝大多数流式处理方案。|专业用途。 

<sup>1</sup> 仅当未在终结点上启用 CDN 时，才直接在流式处理终结点上使用。<br/>

有关 SLA 的信息，请参阅[定价和 SLA](https://azure.microsoft.com/pricing/details/media-services/)。

## <a name="migration-between-types"></a>类型之间的迁移

从 | 如果 | 操作
---|---|---
经典|标准|需要选择加入
经典|高级| 规模（额外流单元）
标准/高级|经典|不可用（如果流式处理终结点版本为 1.0， 允许通过将 scaleunits 设置为“0”更改为经典）
标准（带/不带 CDN）|使用相同配置的高级类型|在 **已启动** 状态下允许。 （通过 Azure 门户）
高级（带/不带 CDN）|使用相同配置的标准类型|在**已启动**状态下允许（通过 Azure 门户）
标准（带/不带 CDN）|使用不同配置的高级类型|在**已停止**状态下允许（通过 Azure 门户）。 在“正在运行”状态下不允许。
高级（带/不带 CDN）|使用不同配置的标准类型|在**已停止**状态下允许（通过 Azure 门户）。 在“正在运行”状态下不允许。
版本 1.0 且 SU >= 1，使用 CDN|不带 CDN 的标准/高级类型|在**已停止**状态下允许。 在**已启动**状态下不允许。
版本 1.0 且 SU >= 1，使用 CDN|标准（带/不带 CDN）|在**已停止**状态下允许。 在**已启动**状态下不允许。 将删除 1.0 版 CDN，然后创建新的 CDN 并将其启动。
版本 1.0 且 SU >= 1，使用 CDN|高级（带/不带 CDN）|在**已停止**状态下允许。 在**已启动**状态下不允许。 将删除经典 CDN，然后创建新的 CDN 并将其启动。

## <a name="next-steps"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
