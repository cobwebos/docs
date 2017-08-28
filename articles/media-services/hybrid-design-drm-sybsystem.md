---
title: "使用 Azure 媒体服务进行 DRM 子系统的混合设计 | Microsoft Docs"
description: "本主题介绍如何使用 Azure 媒体服务进行 DRM 子系统的混合设计。"
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: willzhan;juliako
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: dbaf2f99bb1d3ad719e2f680f53babfec707afb7
ms.contentlocale: zh-cn
ms.lasthandoff: 07/18/2017

---
# <a name="hybrid-design-of-drm-subsystems"></a>DRM 子系统的混合设计

本主题介绍如何使用 Azure 媒体服务进行 DRM 子系统的混合设计。

## <a name="overview"></a>概述

Azure 媒体服务针对以下三个 DRM 系统提供支持：

* PlayReady
* Widevine（模块化）
* FairPlay

DRM 支持包括 DRM 加密（动态加密）和许可证传送，其中的 Azure Media Player 支持使用所有 3 个 DRM 作为浏览器播放器 SDK。

有关 DRM/CENC 子系统设计和实现的详细处理方法，请参阅标题为[使用多重 DRM 的 CENC 和访问控制](media-services-cenc-with-multidrm-access-control.md)的文档。

尽管我们针对三个 DRM 系统提供完整支持，但客户有时除了使用 Azure 媒体服务以外，还需要使用其自己的基础结构/子系统的各个部件来构建混合 DRM 子系统。

下面是客户提出的一些常见问题：

* “可以使用我自己的 DRM 许可证服务器？” （对于这种情况，客户已经投资购买了具有嵌入式业务逻辑的 DRM 许可证服务器场）。
* “是否可以做到在 Azure 媒体服务中只使用你们的 DRM 许可证传送功能，而无需在 AMS 中托管内容？”

## <a name="modularity-of-the-ams-drm-platform"></a>AMS DRM 平台的模块性

作为综合性云视频平台的一部分，Azure 媒体服务 DRM 在设计上考虑到了灵活性和模块性。 可以针对下表中所述的任意不同组合使用 Azure 媒体服务（后面提供了表中使用的简写的说明）。 

|**内容托管和来源**|**内容加密**|**DRM 许可证传送**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|第三方|
|AMS|第三方|AMS|
|AMS|第三方|第三方|
|第三方|第三方|AMS|

### <a name="content-hosting--origin"></a>内容托管和来源

* AMS：视频资产托管在 AMS 中，通过 AMS 流式处理终结点（但不一定是动态打包）进行流式处理。
* 第三方：视频通过 AMS 外部的第三方流式处理平台托管和传送。

### <a name="content-encryption"></a>内容加密

* AMS：内容加密由 AMS 动态加密功能动态/按需执行。
* 第三方：使用预处理工作流在 AMS 外部执行内容加密。

### <a name="drm-license-delivery"></a>DRM 许可证传送

* AMS：DRM 许可证由 AMS 许可证传送服务传送。
* 第三方：DRM 许可证由 AMS 外部的第三方 DRM 许可证服务器传送。

## <a name="configure-based-on-your-hybrid-scenario"></a>根据混合方案进行配置

### <a name="content-key"></a>内容密钥

通过配置内容密钥，可以控制 AMS 动态加密和 AMS 许可证传送服务的以下属性：

* 用于 DRM 动态加密的内容密钥。
* 许可证传送服务要传送的 DRM 许可证内容：版权、内容密钥和限制。
* **内容密钥授权策略限制**的类型：开放、IP 或令牌限制。
* 如果使用**令牌**类型的**内容密钥授权策略限制**，则在颁发许可证之前，必须符合**内容密钥授权策略限制**。

### <a name="asset-delivery-policy"></a>资产传送策略

通过配置资产传送策略，可以控制 AMS 流式处理终结点的 AMS 动态打包程序和动态加密使用的以下属性：

* 流式处理协议和 DRM 加密的组合，例如 CENC 下的 DASH（PlayReady 和 Widevine）、PlayReady 下的平滑流式处理、Widevine 或 PlayReady 下的 HLS。
* 每个相关 DRM 的默认/嵌入式许可证传送 URL。
* DASH MPD 或 HLS 播放列表中的许可证获取 URL (LA_URL) 是否分别包含 Widevine 和 FairPlay 的密钥 ID (KID) 查询字符串。

## <a name="scenarios-and-samples"></a>方案和示例

以下五个混合方案根据前一部分中的说明，使用相应的**内容密钥**-**资产传送策略**配置组合（表格后面提供了最后一列中所述的示例）：

|**内容托管和来源**|**DRM 加密**|**DRM 许可证传送**|**配置内容密钥**|**配置资产传送策略**|**示例**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|是|是|示例 1|
|AMS|AMS|第三方|是|是|示例 2|
|AMS|第三方|AMS|是|否|示例 3|
|AMS|第三方|外部|否|否|示例 4|
|第三方|第三方|AMS|是|否|    

在示例中，PlayReady 保护适用于 DASH 和平滑流式处理。 以下视频 URL 是平滑流式处理 URL。 若要获取相应的 DASH URL，只需追加“(format=mpd-time-csf)”。 可以使用 [azure media test player](http://aka.ms/amtest) 在浏览器中进行测试。 这样就可以配置要在哪种技术下使用哪个流式处理协议。 Windows 10 上的 IE11 和 MS Edge 支持通过 EME 使用 PlayReady。 有关详细信息，请参阅[有关测试工具的详细信息](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/)。

### <a name="sample-1"></a>示例 1

* 源（基本）URL：https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady LA_URL（DASH 和平滑流）：https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine LA_URL (DASH)：https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* FairPlay LA_URL (HLS)：https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>示例 2

* 源（基本） URL：http://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL（DASH 和平滑流）：http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>示例 3

* 源 URL：https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL（DASH 和平滑流）：https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>示例 4

* 源 URL：https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL（DASH 和平滑流）：https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="summary"></a>摘要

总而言之，Azure 媒体服务 DRM 组件非常灵活，只需根据本主题中所述适当配置内容密钥和资产传送策略，即可在混合方案中使用这些组件。

## <a name="next-steps"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


