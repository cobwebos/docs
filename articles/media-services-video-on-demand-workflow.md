<properties 
	pageTitle="Media Services 视频点播工作流" 
	description="本主题介绍典型 Media Services 视频点播工作流的步骤。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

# Media Services 视频点播工作流

本主题介绍典型 Azure Media Services (AMS) 视频点播工作流的步骤。每个步骤都链接到相关的主题。对于可以使用不同技术实现的任务，将提供相应的按钮链接到所选的技术（例如，.NET 或 REST）。   

请注意，你可以将 Media Services 与现有工具和过程相集成。例如，在现场为内容编码，再将其上载到 Media Services 以转码为多种格式，然后通过 Azure CDN 或第三方 CDN 交付内容。 

下图显示了参与视频点播工作流的主要 Media Services 平台部分。
![VoD workflow][vod-overview]

## 创建 Media Services 帐户

使用 **Azure 管理门户**来[创建 Azure Media Services 帐户](/zh-cn/documentation/articles/media-services-create-account/)。

## 设置开发环境  

为开发环境选择".NET"或"REST API"。

[WACOM.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

## 以编程方式建立连接  

选择".NET"或"REST API"以编程方式连接到 Azure Media Services。  

[WACOM.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

## 上载媒体 

使用 **Azure 管理门户**、".NET"或"REST API"上载文件。

[WACOM.INCLUDE [media-services-selector-upload-files](../includes/media-services-selector-upload-files.md)]


## 索引

[WACOM.INCLUDE [media-services-selector-index-content](../includes/media-services-selector-index-content.md)]


## 编码 

有关详细信息，请参阅[编码和打包](https://msdn.microsoft.com/zh-cn/library/azure/dn621224.aspx)。

## 配置内容保护 

有关详细信息，请参阅[保护媒体](https://msdn.microsoft.com/zh-cn/library/azure/dn282272.aspx)。

## 配置资产传送策略

参阅以下示例：

[为使用 AES 加密的资产配置传送策略](https://msdn.microsoft.com/zh-cn/library/azure/dn783457.aspx#configure_delivery_policy)

[为使用 PlayReady 保护的资产配置传送策略](https://msdn.microsoft.com/zh-cn/library/azure/dn783467.aspx#configure_delivery_policy)




[创建 Azure Media Services 帐户]: /zh-cn/documentation/articles/media-services-create-account

[vod-overview]: ./media/media-services-overview/media-services-video-on-demand.png
