---
title: "通过 Azure 门户使用 Media Encoder Standard 对资产进行编码 | Microsoft Docs"
description: "本教程逐步演示如何通过 Azure 门户使用 Media Encoder Standard 对资产进行编码。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: efe7db8a36273b4755dd057139bb1c673af868d3
ms.contentlocale: zh-cn
ms.lasthandoff: 08/08/2017

---
# <a name="encode-an-asset-using-media-encoder-standard-with-the-azure-portal"></a>通过 Azure 门户使用 Media Encoder Standard 对资产进行编码
> [!NOTE]
> 要完成本教程，需要一个 Azure 帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。 
> 
> 

使用 Azure 媒体服务时最常见的方案之一是将自适应比特率流传送至客户端。 媒体服务支持以下自适应比特率流式处理技术：HTTP Live Streaming (HLS)、平滑流式处理和 MPEG DASH。 要对视频进行准备，以便进行自适应比特率流式处理，需要将源视频编码成多比特率文件。 应使用 **Media Encoder Standard** 编码器来对视频编码。  

此外，媒体服务提供的动态打包可让用户以下述流格式传送多比特率 MP4，无需重新打包成这些流格式：MPEG DASH、HLS 和平滑流式处理。 通过动态打包，只需要存储及支付一种存储格式的文件，媒体服务会根据客户端的要求创建并提供适当的响应。

要利用动态打包，需将源文件编码成一组多比特率 MP4 文件（本部分稍后将演示编码步骤）。

若要缩放媒体处理，请参阅[此](media-services-portal-scale-media-processing.md)主题。

## <a name="encode-with-the-azure-portal"></a>使用 Azure 门户编码
本部分介绍使用 Media Encoder Standard 为内容编码时可以执行的步骤。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 Azure 媒体服务帐户。
2. 在“设置”窗口中，选择“资产”。  
3. 在“资产”窗口中，选择想要编码的资产。
4. 按“编码”按钮。
5. 在“对资产进行编码”窗口中，选择“Media Encoder Standard”处理器和预设。 有关预设的信息，请参阅[自动生成比特率阶梯](media-services-autogen-bitrate-ladder-with-mes.md)和 [MES 的任务预设](media-services-mes-presets-overview.md)。 如果计划控制所使用的编码预设，请谨记：必须选择最适合输入视频的预设。 例如，如果知道输入视频的分辨率为 1920x1080 像素，则可使用“H264 多比特率 1080p”预设。 如果视频的分辨率低 (640x360)，则不应使用“H264 多比特率 1080p”预设。
   
   为了方便管理，系统允许编辑输出资产的名称，以及作业的名称。
   
   ![对资产进行编码](./media/media-services-portal-vod-get-started/media-services-encode1.png)
6. 按“创建”。

## <a name="next-step"></a>后续步骤
可通过 Azure 门户监视编码作业的进度，如[此](media-services-portal-check-job-progress.md)文章中所述。  

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


