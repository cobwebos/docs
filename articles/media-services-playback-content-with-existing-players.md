<properties 
	pageTitle="播放内容" 
	description="本主题介绍如何播放内容。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>











# 播放内容

Azure Media Services 支持多种常用的流式处理格式，如平滑流、HTTP 实时流和 MPEG-Dash。本主题会将你指向可用来测试流的现有播放器。  

## 使用现有播放器播放内容

本主题介绍了可用来播放内容的现有播放器。

>[WACOM.NOTE]若要播放动态打包或动态加密的内容，请确保获取你计划从中传送内容的流式处理终结点的至少一个流式处理单元。有关缩放流式处理单元的信息，请参阅：[如何缩放流式处理单元](../media-services-manage-origins#scale_streaming_endpoints)。


### Azure 管理门户 Media Services 内容播放器

**Azure 管理门户**提供了可用于测试视频的内容播放器。

单击所需的视频内容（确保它[已发布](/zh-cn/documentation/articles/media-services-manage-content#publish)），然后单击门户底部的**播放**按钮。 
 
请注意以下事项：

- **MEDIA SERVICES 内容播放器**从默认的流式处理终结点播放。如果要从非默认流式处理终结点播放，请使用其他播放器。例如 [Azure Media Services 播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。
 

![AMSPlayer][AMSPlayer]

### Azure Media Services 播放器

使用 [Azure Media Services 播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html)播放你的内容。


## 开发视频播放器

有关如何开发自己的播放器的信息，请参阅[开发视频播放器](/zh-cn/documentation/articles/media-services-develop-video-players)
 
[AMSPlayer]: ./media/media-services-players/media-services-portal-player.png
