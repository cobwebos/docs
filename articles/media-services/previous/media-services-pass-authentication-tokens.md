---
title: 将身份验证令牌传递到 Azure 媒体服务 | Microsoft Docs
description: 了解如何将身份验证令牌从客户端发送到 Azure 媒体服务密钥传送服务
services: media-services
keywords: 内容保护, DRM, 令牌身份验证
documentationcenter: ''
author: dbgeorge
manager: jasonsue
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: dwgeo
ms.openlocfilehash: b6aca2928465b73e35ac15f01bb776b1f69add0b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>了解客户端如何将令牌传递到 Azure 媒体服务密钥传送服务
客户经常会问，播放器如何将令牌传递到 Azure 媒体服务密钥传送服务来进行验证，以便播放器可以获取密钥。 媒体服务支持简单 Web 令牌 (SWT) 和 JSON Web 令牌 (JWT) 格式。 令牌身份验证可应用于任何类型的密钥，不论系统中使用的是通用加密还是高级加密标准 (AES) 信封加密。

 可使用播放器通过以下方式传递令牌，具体取决于设定为目标的播放器和平台：

- 通过 HTTP 授权标头。
    > [!NOTE]
    > 根据 OAuth 2.0 规格，应使用“Bearer”前缀。 Azure 媒体播放器[演示页面](http://ampdemo.azureedge.net/)上提供了一个具有令牌配置的示例播放器。 若要设置视频源，请选择“AES（JWT 令牌）”或“AES（SWT 令牌）”。 令牌是通过授权标头传递的。

- 通过使用“token=tokenvalue”添加一个 URL 查询参数。  
    > [!NOTE]
    > 不应使用“Bearer”前缀。 由于令牌是通过 URL 发送的，因此需要保护令牌字符串。 下面的 C# 示例代码显示了如何执行此操作：

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- 通过 CustomData 字段。
此选项仅用于 PlayReady 许可证获取，通过 PlayReady 许可证获取质询的 CustomData 字段。 在此情况下，令牌必须位于如下所述的 xml 文档中：

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    将身份验证令牌放入 Token 元素中。

- 通过替换的 HTTP Live Streaming (HLS) 播放列表。 如果需要在 iOS/Safari 上为 AES + HLS 播放配置令牌身份验证，无法直接发送令牌。 有关如何替换播放列表来启用此方案的详细信息，请参阅此[博客文章](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]