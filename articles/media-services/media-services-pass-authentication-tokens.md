---
title: "将身份验证令牌传递到 Azure 媒体服务 | Microsoft Docs"
description: "了解如何从客户端将身份验证令牌发送到 Azure 媒体服务密钥传送服务"
services: media-services
keywords: "内容保护, DRM, 令牌身份验证"
documentationcenter: 
author: dbgeorge
manager: jasonsue
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: dwgeo
ms.openlocfilehash: 0e56726266898e5738dd797a8a019987d457170e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2017
---
# <a name="how-clients-pass-tokens-to-azure-media-services-key-delivery-service"></a>客户端如何将令牌传递到 Azure 媒体服务密钥传送服务
我们不断收到有关播放器如何将令牌传递到密钥传送服务的问题（这些问题将得到验证）以及播放器如何获取密钥的问题。 我们支持简单 Web 令牌 (SWT) 和 JSON Web 令牌 (JWT) 这两种令牌格式。 令牌身份验证可应用于任何密钥类型 – 不论正在系统中进行通用加密还是 AES 信封加密。

可使用播放器通过以下方式传递令牌，具体取决于设定为目标的播放器和平台：
- 通过 HTTP 授权标头。
> [!NOTE]
> 请注意，根据 OAuth 2.0 规格，应使用“Bearer”前缀。 有一个示例播放器，其令牌配置托管在 Azure 媒体播放器[演示页面](http://ampdemo.azureedge.net/)。 请选择 AES（JWT 令牌）或 AES（SWT 令牌）以设置视频源。 令牌通过授权标头传递。

- 通过添加带有“令牌=tokenvalue”的 URL 查询参数。  
> [!NOTE]
> 请注意，不应使用“Bearer”前缀。 由于令牌通过 URL 发送，因此需要保护令牌字符串。 下面是执行此操作的 C# 示例代码：

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

- 通过 CustomData 字段。
仅针对 PlayReady 许可证获取，通过 PlayReady 许可证获取质询的 CustomData 字段。 在此情况下，令牌必须位于以下所述的 xml 文档中。

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```
将身份验证令牌放入 <Token> 元素中。

- 通过备用的 HLS 播放列表。 如果需要在 iOS/Safari 上为 AES + HLS 播放配置令牌身份验证，无法直接发送令牌。 请参阅此[博客文章](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)，了解有关如何替换播放列表来启用此方案的信息。

## <a name="next-steps"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]