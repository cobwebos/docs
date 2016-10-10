<properties
	pageTitle=" 使用 Azure 门户发布内容 | Azure"
	description="本教程逐步演示如何使用 Azure 门户发布内容。"
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/29/2016"
	wacn.date=""
	ms.author="juliako"/>

# 使用 Azure 门户发布内容

> [AZURE.SELECTOR]
- [门户](/documentation/articles/media-services-portal-publish/)
- [.NET](/documentation/articles/media-services-deliver-streaming-content/)
- [REST](/documentation/articles/media-services-rest-deliver-streaming-content/)

## 概述

> [AZURE.NOTE] 若要完成本教程，你需要一个 Azure 帐户。有关详细信息，请参阅 [Azure 试用](/pricing/1rmb-trial/)。

若要为用户提供一个可用来流式传输内容或下载内容的 URL，你首先需要通过创建定位符来“发布”资产。定位符提供对资产中所含文件的访问权限。媒体服务支持两种类型的定位符：

- 用于自适应流式处理（例如，对 MPEG DASH、HLS 或 Smooth Streaming 进行流式处理）的流式处理 (OnDemandOrigin) 定位符。若要创建流式处理定位符，你的资产必须包含一个 .ism 文件。
- 渐进式 (SAS) 定位符，用于通过渐进式下载来传输视频。


流 URL 采用以下格式，你可以用它来播放平滑流资产。

	{streaming endpoint name-media services account name}.streaming.mediaservices.chinacloudapi.cn/{locator ID}/{filename}.ism/Manifest

若要生成 HLS 流 URL，请将 (format=m3u8-aapl) 附加到 URL。

	{streaming endpoint name-media services account name}.streaming.mediaservices.chinacloudapi.cn/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

若要生成 MPEG DASH 流 URL，请将 (format=mpd-time-csf) 追加到 URL。

	{streaming endpoint name-media services account name}.streaming.mediaservices.chinacloudapi.cn/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

SAS URL 采用以下格式。

	{blob container name}/{asset name}/{file name}/{SAS signature}

有关详细信息，请参阅 [Delivering content overview](/documentation/articles/media-services-deliver-content-overview/)（传送内容概述）。

>[AZURE.NOTE] 如果你使用门户在 2015 年 3 月之前创建了定位符，则会创建过期日期在两年后的定位符。

若要更新定位符的过期日期，请使用 [REST](http://msdn.microsoft.com/zh-cn/library/azure/hh974308.aspx#update_a_locator) 或 [.NET](https://msdn.microsoft.com/zh-cn/library/azure/microsoft.windowsazure.mediaservices.client.ilocator.update(v=azure.10).aspx) API。请注意，当你更新 SAS 定位符的过期日期时，URL 会发生变化。

### 使用门户发布资产

若要使用门户发布资产，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.cn/)登录。
1. 选择“设置”>“资产”。
1. 选择你想要发布的资产。
1. 单击“发布”按钮。
1. 选择定位符类型。
2. 按“添加”。

	![发布](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL 将添加到“已发布 URL”列表。

## 从门户播放内容

Azure 门户提供了可用于测试视频的内容播放器。

单击所需的视频，然后单击“播放”按钮。

![发布](./media/media-services-portal-vod-get-started/media-services-play.png)

请注意以下事项：

- 确保视频已发布。
- 此 **Media player** 从默认的流式处理终结点播放。如果要从非默认流式处理终结点播放，请单击复制该 URL，然后使用其他播放器。例如 [Azure 媒体服务播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。
- 用于进行流式处理的流式处理终结点必须处于运行状态。
- 要从一个流式处理终结点进行流式处理，应添加至少一个流式处理单元。有关详细信息，请参阅[此](/documentation/articles/media-services-portal-scale-streaming-endpoints/)主题。

<!---HONumber=Mooncake_0926_2016-->