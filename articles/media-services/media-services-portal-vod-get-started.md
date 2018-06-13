---
title: 使用 Azure 门户传送点播视频入门 | Microsoft Docs
description: 本教程介绍了在 Azure 门户中使用 Azure 媒体服务应用程序实施基本的视频点播内容传送服务的步骤。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: fb981f3240799c924464c828b2c835ac5d9879ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "22713221"
---
# <a name="get-started-with-delivering-content-on-demand-by-using-the-azure-portal"></a>使用 Azure 门户按需传送内容入门
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

本教程介绍了在 Azure 门户中使用 Azure 媒体服务应用程序实施基本的视频点播内容传送服务的步骤。

## <a name="prerequisites"></a>先决条件
以下项目是完成本教程所需具备的条件：

* 一个 Azure 帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。 
* 一个媒体服务帐户。 若要创建媒体服务帐户，请参阅[如何创建媒体服务帐户](media-services-portal-create-account.md)。

本教程包括以下任务：

1. 启动流式处理终结点。
2. 上传视频文件。
3. 将源文件编码为一组自适应比特率 MP4 文件。
4. 发布资产并获取流式处理和渐进式下载 URL。  
5. 播放内容。

## <a name="start-the-streaming-endpoint"></a>启动流式处理终结点

使用 Azure 媒体服务时，最常见的场景之一是通过自适应比特率流式处理传送视频。 可以通过媒体服务进行动态打包。 使用动态打包时，可以按媒体服务所支持的实时流式处理格式传送自适应比特率 MP4 编码内容。 示例包括 Apple HTTP Live Streaming (HLS)、Microsoft 平滑流式处理，以及经 HTTP 的动态自适应流式处理（DASH，也称 MPEG-DASH）。 使用媒体服务自适应比特率流式处理，不需存储每个此类流式处理格式的预打包版本即可传送视频。

> [!NOTE]
> 创建媒体服务帐户后，会将一个处于“已停止”状态的默认流式处理终结点添加到帐户。 若要开始流式传输内容并利用动态打包和动态加密，要从中流式传输内容的流式处理终结点必须处于“正在运行”状态。 

若要启动流式处理终结点，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择“设置” > “流式处理终结点”。 
3. 选择默认的流式处理终结点。 此时会显示“默认流式处理终结点详细信息”窗口。
4. 选择“启动”图标。
5. 选择“保存”按钮。

## <a name="upload-files"></a>上传文件
若要使用媒体服务来流式传输视频，请先上传源视频、将其编码成多个比特率，然后发布结果。 第一步在本部分介绍。 

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 Azure 媒体服务帐户。
2. 选择“设置” > “资产”。 然后选择“上传”按钮。
   
    ![上传文件](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    此时会显示“上传视频资产”窗口。
   
   > [!NOTE]
   > 媒体服务不会限制上传视频的文件大小。
   > 
   > 
3. 在计算机上，转到要上传的视频。 选择视频，然后选择“确定”。  
   
    上传开始。 可以在文件名下看到进度。  

上传完成后，新资产列在“资产”窗格中。 

## <a name="encode-assets"></a>对资产进行编码
要利用动态打包，必须将源文件编码为一组多比特率 MP4 文件。 本部分演示编码步骤。

### <a name="encode-assets-in-the-portal"></a>在门户中对资产进行编码
若要在 Azure 门户中使用 Media Encoder Standard 对内容进行编码，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 Azure 媒体服务帐户。
2. 选择“设置” > “资产”。 选择要编码的资产。
3. 选择“编码”按钮。
4. 在“对资产进行编码”窗格中，选择“Media Encoder Standard”处理器和预设。 有关预设的信息，请参阅[自动生成比特率阶梯](media-services-autogen-bitrate-ladder-with-mes.md)和 [Media Encoder Standard 的任务预设](media-services-mes-presets-overview.md)。 请务必选择最适合输入视频的预设。 例如，如果知道输入视频的分辨率为 1920 &#215; 1080 像素，则可选择“H264 多比特率 1080p”预设。 如果视频的分辨率低 (640 &#215; 360)，则不应使用“H264 多比特率 1080p”预设。
   
   可以编辑输出资产名称和作业名称，以便管理资源。
   
   ![对资产进行编码](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. 选择“创建” 。

### <a name="monitor-encoding-job-progress"></a>监视编码作业进度
若要监视编码作业的进度，请先选择“设置”（位于页面顶部），然后选择“作业”。

![作业](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>发布内容
若要为用户提供一个可用来流式传输内容或下载内容的 URL，首先必须通过创建定位符来发布资产。 定位符提供对资产中文件的访问权限。 Azure 媒体服务支持两种类型的定位符： 

* **流式处理 (OnDemandOrigin) 定位符**。 流式处理定位符用于自适应流式处理。 自适应流式处理示例包括：HLS、平滑流式处理和 MPEG-DASH。 若要创建流式处理定位符，资产必须包括一个 .ism 文件。 
* **渐进式（共享访问签名）定位符**。 渐进式定位符用于通过渐进式下载来传输视频。

要生成 HLS 流 URL，请将 (format=m3u8-aapl) 附加到 URL：

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

要生成流式处理 URL 来播放平滑流资产，请使用以下 URL 格式：

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest

要生成 MPEG DASH 流式处理 URL，请将 (format=mpd-time-csf) 附加到 URL：

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

共享访问签名 URL 格式如下：

    {blob container name}/{asset name}/{file name}/{shared access signature}

> [!NOTE]
> 2015 年 3 月之前在 Azure 门户中创建的定位符的过期日期为两年后。  
> 
> 

可以使用 [REST API](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) 或 [.NET API](http://go.microsoft.com/fwlink/?LinkID=533259) 来更新定位符的过期日期。 

> [!NOTE]
> 更新共享访问签名定位符的过期日期时，URL 会发生变化。

### <a name="to-use-the-portal-to-publish-an-asset"></a>使用门户发布资产
1. 在 [Azure 门户](https://portal.azure.com/)中，选择 Azure 媒体服务帐户。
2. 选择“设置” > “资产”。 选择想要发布的资产。
3. 选择“发布”按钮。
4. 选择定位符类型。
5. 选择“添加”。
   
    ![发布视频](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL 将添加到“已发布 URL”列表。

## <a name="play-content-from-the-portal"></a>从门户播放内容
可以在 Azure 门户中的内容播放器上测试视频。

选择视频，然后选择“播放”按钮。

![在 Azure 门户中播放视频](./media/media-services-portal-vod-get-started/media-services-play.png)

请注意以下事项：

* 若要启动流式处理，请开始运行“默认”流式处理终结点。
* 确保视频已发布。
* Azure 门户媒体播放器从默认的流式处理终结点播放。 若要从非默认流式处理终结点播放，请选择并复制该 URL，然后将其粘贴到其他播放器中。 例如，可以在 [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) 上测试视频。

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>后续步骤
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]
