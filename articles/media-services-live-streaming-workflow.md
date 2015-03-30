<properties 
	pageTitle="Media Services 实时流式处理工作流" 
	description="本主题介绍典型 Media Services 实时流式处理工作流的步骤。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>











# Media Services 实时流式处理工作流

## 概述

本主题介绍典型 Azure Media Services (AMS) 实时流式处理工作流的步骤。每个步骤都链接到相关的主题。对于可以使用不同技术实现的任务，将提供相应的按钮链接到所选的技术（例如，.NET 或 REST）。   

请注意，你可以将 Media Services 与现有工具和过程相集成。例如，在现场为内容编码，再将其上载到 Media Services 以转码为多种格式，然后通过 Azure CDN 或第三方 CDN 交付内容。 

下图显示了参与视频点播工作流的主要 Media Services 平台部分。
![Live workflow][live-overview]


有关详细信息，请参阅 [Media Services 概述](/zh-cn/documentation/articles/media-services-overview)。

## 创建 Media Services 帐户

使用 **Azure 管理门户**来[创建 Azure Media Services 帐户](/zh-cn/documentation/articles/media-services-create-account/)。

## 配置流式处理终结点

[工作进行中]

## 设置开发环境  

为开发环境选择 **.NET**或 **REST API**。

[WACOM.INCLUDE [media-services-selector-setup](../includes/media-services-selector-setup.md)]

## 以编程方式建立连接  

选择".NET"或"REST API"以编程方式连接到 Azure Media Services。

[WACOM.INCLUDE [media-services-selector-connect](../includes/media-services-selector-connect.md)]

## 使用实时转码器

有关详细信息，请参阅[将第三方实时编码器与 Azure Media Services 结合使用](https://msdn.microsoft.com/zh-cn/library/azure/dn783464.aspx)。

## 管理通道、节目和资产

有关详细信息，请参阅[实时流式处理](https://msdn.microsoft.com/zh-cn/library/azure/dn783466.aspx)。

## 配置内容保护和内容密钥授权策略 

使用 **.NET** 或 **REST API** 配置密钥授权策略。

[WACOM.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

## 配置资产传送策略

使用 **.NET** 或 **REST API** 配置资产传送策略。

[WACOM.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

## 发布资产

使用 **Azure 管理门户**或 **.NET** 发布资产（通过创建定位符）。

[WACOM.INCLUDE [media-services-selector-publish](../includes/media-services-selector-publish.md)]


## 缩放 Media Services 帐户

通过指定要为帐户设置的**流式处理保留单元**数，可以缩放 **Media Services**。 

有关缩放流式处理单元的信息，请参阅：[如何缩放流式处理单元](/zh-cn/documentation/articles/media-services-manage-origins#scale_streaming_endpoints)。



[live-overview]: ./media/media-services-overview/media-services-live-streaming-current.png
