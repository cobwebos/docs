---
title: "  使用 Azure 门户发布内容 | Microsoft Docs"
description: "本教程逐步演示如何使用 Azure 门户发布内容。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 96a8342dc782a9270a6de5b67387c35ea45b860e


---
# <a name="publish-content-with-the-azure-portal"></a>使用 Azure 门户发布内容
> [!div class="op_single_selector"]
> * [门户](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>概述
> [!NOTE]
> 若要完成本教程，你需要一个 Azure 帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。 
> 
> 

若要为用户提供一个可用来流式传输内容或下载内容的 URL，你首先需要通过创建定位符来“发布”资产。 定位符提供对资产中所含文件的访问权限。 媒体服务支持两种类型的定位符： 

* 用于自适应流式处理（例如，对 MPEG DASH、HLS 或 Smooth Streaming 进行流式处理）的流式处理 (OnDemandOrigin) 定位符。 若要创建流式处理定位符，你的资产必须包含一个 .ism 文件。 
* 渐进式 (SAS) 定位符，用于通过渐进式下载来传输视频。

流 URL 采用以下格式，你可以用它来播放平滑流资产。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

若要生成 HLS 流 URL，请将 (format=m3u8-aapl) 附加到 URL。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

若要生成 MPEG DASH 流 URL，请将 (format=mpd-time-csf) 附加到 URL。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

SAS URL 采用以下格式。

    {blob container name}/{asset name}/{file name}/{SAS signature}

有关详细信息，请参阅[传送内容概述](media-services-deliver-content-overview.md)。

> [!NOTE]
> 如果你使用门户在 2015 年 3 月之前创建了定位符，则会创建过期日期在两年后的定位符。  
> 
> 

若要更新定位符的过期日期，请使用 [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) 或 [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API。 请注意，当你更新 SAS 定位符的过期日期时，URL 会发生变化。

### <a name="to-use-the-portal-to-publish-an-asset"></a>使用门户发布资产
若要使用门户发布资产，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 Azure 媒体服务帐户。
2. 选择“设置” > “资产”。
3. 选择你想要发布的资产。
4. 单击“上载”按钮 **发布** 。
5. 选择定位符类型。
6. 按“添加”。
   
    ![发布](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL 将添加到“已发布 URL”列表。

## <a name="play-content-from-the-portal"></a>从门户播放内容
Azure 门户提供可用于测试视频的内容播放器。

单击所需的视频，然后单击“播放”按钮。

![发布](./media/media-services-portal-vod-get-started/media-services-play.png)

请注意以下事项：

* 确保视频已发布。
* 此“Media Player”  从默认的流式处理终结点播放。 如果要从非默认流式处理终结点播放，请单击复制该 URL，然后使用其他播放器。 例如 [Azure 媒体服务播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。
* 用于进行流式处理的流式处理终结点必须处于运行状态。  
* 要从一个流式处理终结点进行流式处理，应添加至少一个流式处理单元。 有关详细信息，请参阅[此](media-services-portal-scale-streaming-endpoints.md)主题。   

## <a name="next-steps"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO3-->


