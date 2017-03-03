---
title: "概述并比较 Azure 点播媒体编码器 | Microsoft Docs"
description: "本主题简要介绍并比较了 Azure 点播媒体编码器。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: e126076717eac275914cb438ffe14667aad6f7c8
ms.openlocfilehash: 6aecea32361166d363a412b4e8f2b01d421da5cf
ms.lasthandoff: 01/13/2017


---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>概述并比较 Azure 点播媒体编码器
## <a name="encoding-overview"></a>编码概述
Azure 媒体服务提供了多个用于在云中对媒体进行编码的选项。

一开始使用媒体服务时，了解编解码器与文件格式之间的区别很重要。
编解码器是实现压缩/解压缩算法的软件，而文件格式是用于保存压缩视频的容器。

媒体服务所提供的动态打包，允许以媒体服务支持的流格式（MPEG DASH、HLS、平滑流式处理）传送自适应比特率 MP4 或平滑流式处理编码内容，而无须重新打包成这些流格式。

>[!NOTE]
>创建 AMS 帐户后，会将一个处于“已停止”状态的**默认**流式处理终结点添加到帐户。 若要开始流式传输内容并利用动态打包和动态加密，要从中流式传输内容的流式处理终结点必须处于“正在运行”状态。 若要使用[动态打包](media-services-dynamic-packaging-overview.md)，必须执行下列操作：
>
>此外，将源文件编码成一组自适应比特率 MP4 文件或自适应比特率平滑流式处理文件（本教程稍后将演示编码步骤）。

媒体服务支持将在本文中介绍的以下按需编码器：

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [媒体编码器高级工作流](media-services-encode-asset.md#media-encoder-premium-workflow)

本文简要概述了点播媒体编码器，并提供了指向介绍更多详细信息的文章的链接。 本主题还提供对编码器的比较。

请注意，默认情况下每个媒体服务帐户同时只能有一个活动的编码任务。 你可以保留编码单元，使用它们可以同时运行多个编码任务，你购买的每个编码保留单位对应一个任务。 有关信息，请参阅[缩放编码单位](media-services-scale-media-processing-overview.md)。

## <a name="media-encoder-standard"></a>媒体编码器标准版
### <a name="how-to-use"></a>如何使用
[如何使用 Media Encoder Standard 进行编码](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>格式
[格式和编解码器](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>预设
Media Encoder Standard 使用[此处](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)所述的编码器预设之一进行配置。

### <a name="input-and-output-metadata"></a>输入和输出元数据
[此处](media-services-input-metadata-schema.md)说明了编码器输入元数据。

[此处](media-services-output-metadata-schema.md)说明了编码器输出元数据。

### <a name="generate-thumbnails"></a>生成缩略图
有关信息，请参阅[如何使用 Media Encoder Standard 生成缩略图](media-services-advanced-encoding-with-mes.md#thumbnails)。

### <a name="trim-videos-clipping"></a>修剪视频（裁剪）
有关信息，请参阅[如何使用 Media Encoder Standard 剪裁视频](media-services-advanced-encoding-with-mes.md#trim_video)。

### <a name="create-overlays"></a>创建覆盖层
有关信息，请参阅[如何使用 Media Encoder Standard 创建覆盖层](media-services-advanced-encoding-with-mes.md#overlay)。

### <a name="see-also"></a>另请参阅
[媒体服务博客](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>媒体编码器高级工作流
### <a name="overview"></a>概述
[在 Azure 媒体服务中引入高级编码](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>如何使用
媒体编码器高级工作流使用复杂的工作流进行配置。 可以使用[工作流设计器](media-services-workflow-designer.md)工具创建和更新工作流文件。

[如何在 Azure 媒体服务中使用高级编码](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>已知问题
如果输入视频不包含隐藏式字幕，输出资产仍将包含一个空的 TTML 文件。


## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>相关文章
* [通过自定义 Media Encoder Standard 预设执行高级编码任务](media-services-custom-mes-presets-with-dotnet.md)
* [配额和限制](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/

