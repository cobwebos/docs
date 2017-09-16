---
title: "在 Azure 门户中使用 Media Encoder Standard 对资产进行编码 | Microsoft Docs"
description: "本教程逐步演示如何在 Azure 门户中使用 Media Encoder Standard 对资产进行编码。"
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
ms.sourcegitcommit: 3eb68cba15e89c455d7d33be1ec0bf596df5f3b7
ms.openlocfilehash: ae5f4fd391cbf62b41d1a65f1d8107cefe3a5df3
ms.contentlocale: zh-cn
ms.lasthandoff: 09/01/2017

---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>在 Azure 门户中使用 Media Encoder Standard 对资产进行编码

> [!NOTE]
> 要完成本教程，需要一个 Azure 帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。 
> 
> 

使用 Azure 媒体服务时最常见的情况之一是将自适应比特率流传送至客户端。 媒体服务支持以下自适应比特率流式处理技术：Apple HTTP Live Streaming (HLS)、Microsoft 平滑流式处理，以及经 HTTP 的动态自适应流式处理（DASH，也称 MPEG-DASH）。 要准备视频以便进行自适应比特率流式处理，首先要将源视频编码成多比特率文件。 可使用 Azure Media Encoder Standard 对视频进行编码。  

可以通过媒体服务进行动态打包。 通过动态打包，可以在 HLS、平滑流式处理和 MPEG-DASH 中提供多比特率 MP4，而无需重新打包这些流格式。 借助动态打包功能，可存储和播放使用单一存储格式的文件。 媒体服务会根据客户端的请求生成并提供适当的响应。

要利用动态打包，必须将源文件编码为一组多比特率 MP4 文件。 本文稍后将演示编码步骤。

要了解如何调整媒体处理的规模，请参阅[使用 Azure 门户调整媒体处理的规模](media-services-portal-scale-media-processing.md)。

## <a name="encode-in-the-azure-portal"></a>在 Azure 门户中进行编码

要使用 Media Encoder Standard 对内容进行编码，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 Azure 媒体服务帐户。
2. 选择“设置” > “资产”。 选择要编码的资产。
3. 选择“编码”按钮。
4. 在“对资产进行编码”窗格中，选择“Media Encoder Standard”处理器和预设。 有关预设的信息，请参阅[自动生成比特率阶梯](media-services-autogen-bitrate-ladder-with-mes.md)和 [Media Encoder Standard 的任务预设](media-services-mes-presets-overview.md)。 请务必选择最适合输入视频的预设。 例如，如果知道输入视频的分辨率为 1920 &#215; 1080 像素，则可选择“H264 多比特率 1080p”预设。 如果视频的分辨率低 (640 &#215; 360)，则不应使用“H264 多比特率 1080p”预设。
   
   可以编辑输出资产名称和作业名称，以便管理资源。
   
   ![对资产进行编码](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. 选择“创建” 。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>后续步骤
* 在 Azure 门户中[监视编码作业的进度](media-services-portal-check-job-progress.md)。  


