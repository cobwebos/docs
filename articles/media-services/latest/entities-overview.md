---
title: Azure 媒体服务实体概述 - Azure | Microsoft Docs
description: 本文概述了 Azure 媒体服务实体。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/24/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3f3322245983508e374d081e5d7905f67344ad7a
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2019
ms.locfileid: "54912640"
---
# <a name="azure-media-services-entities-overview"></a>Azure 媒体服务实体概述

本文简要概述了 Azure 媒体服务实体，并指向一些文章，方便你获取有关如何在媒体服务工作流中使用每个实体的更多信息。 

| 主题 | 说明 |
|---|---|
| [帐户筛选器和资产筛选器](filters-dynamic-manifest-overview.md)|将内容传送到客户（实时传送视频流事件或点播视频）时，客户端所需的灵活性可能比默认资产的清单文件中描述的灵活性更高。 使用 Azure 媒体服务可定义[帐户筛选器](https://docs.microsoft.com/rest/api/media/accountfilters)和[资产筛选器](https://docs.microsoft.com/rest/api/media/assetfilters)。 然后，根据预定义的筛选器使用**动态清单**。 |
| [资产](assets-concept.md)|[资产](https://docs.microsoft.com/rest/api/media/assets)实体包含数字文件（包括视频、音频、图像、缩略图集合、文本轨道和隐藏式字幕文件）以及这些文件的相关元数据。 数字文件在上传到资产中后，即可用于媒体服务编码、流式处理和分析内容工作流。|
| [内容密钥策略](content-key-policy-concept.md)|可以使用媒体服务在媒体从离开计算机到存储、处理和传送的整个过程中确保其安全。 借助媒体服务，可以传送使用高级加密标准 (AES-128) 或三个主要数字版权管理 (DRM) 系统（Microsoft PlayReady、Google Widevine 和 Apple FairPlay）中任意一个动态加密的实时和请求内容。 媒体服务还提供了用于向已授权客户端传送 AES 密钥和 DRM（PlayReady、Widevine 和 FairPlay）许可证的服务。|
| [实时事件和实时输出](live-events-outputs-concept.md)|使用媒体服务可将实时事件传送到 Azure 云中的客户。 若要在媒体服务 v3 中配置实时传送视频流事件，需了解[实时事件](https://docs.microsoft.com/rest/api/media/liveevents)和[实时输出](https://docs.microsoft.com/rest/api/media/liveoutputs)。|
| [流式处理终结点](streaming-endpoint-concept.md)|[流式处理终结点](https://docs.microsoft.com/rest/api/media/streamingendpoints)实体表示一个流式处理服务，该服务可以直接将内容传送到客户端播放器应用程序，也可以将内容传送到内容分发网络 (CDN) 以供进一步传送。 流式处理终结点服务的出站流可以是实时流，也可以是媒体服务帐户中的视频点播资产。 用户创建媒体服务帐户时，将为用户创建一个处于“已停止”状态的默认流式处理终结点。 无法删除**默认**流式处理终结点。 可以在帐户下创建其他流式处理终结点。 若要开始流式处理视频，需启动要从中流式处理视频的流式处理终结点。 |
| [流式处理定位符](streaming-locators-concept.md)|若要为客户提供可用于播放编码的视频或音频文件的 URL，需要创建[流式处理定位符](https://docs.microsoft.com/rest/api/media/streaminglocators)并生成流式处理 URL。|
| [流式处理策略](streaming-policy-concept.md)| 使用[流式处理策略](https://docs.microsoft.com/rest/api/media/streamingpolicies)可为 StreamingLocator 定义流式处理协议和加密选项。 可以指定创建的自定义流式处理策略的名称，或使用媒体服务所提供的预定义流式处理策略之一。 <br/><br/>使用自定义的流式处理策略时，应为媒体服务帐户设计有限的一组此类策略，并在需要同样的加密选项和协议时重新将这些策略用于流式处理定位符。 不应为每个流式处理定位符创建新的流式处理策略。|
| [转换和作业](transforms-jobs-concept.md)|[转换](https://docs.microsoft.com/rest/api/media/transforms)可用来配置对视频进行编码或分析的常见任务。 每个**转换**描述了用于处理视频或音频文件的脚本或任务工作流。<br/><br/>[作业](https://docs.microsoft.com/rest/api/media/jobs)是针对 Azure 媒体服务的实际请求，目的是将**转换**应用到给定的输入视频或音频内容。 **作业**指定输入视频位置和输出位置等信息。 可以使用以下项指定输入视频的位置：HTTPS URL、SAS URL 或资产。|

## <a name="next-steps"></a>后续步骤

[流式传输文件](stream-files-dotnet-quickstart.md)
