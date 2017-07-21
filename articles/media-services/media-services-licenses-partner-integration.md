---
title: "使用合作伙伴将 Widevine 许可证传送到 Azure 媒体服务 | Microsoft Docs"
description: "本文介绍如何使用 Azure 媒体服务(AMS) 传送 AMS 使用 PlayReady 和 Widevine DRM 动态加密的流。 PlayReady 许可证来自媒体服务 PlayReady 许可证服务器，而 Widevine 许可证则由 castLabs 许可证服务器传送。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 5bcad5a4-c0bb-4871-9cce-808a913c53e6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: b4211cef3b4f3ffa2c0c97fd7650606f3eef7008
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016

---
# <a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>使用合作伙伴将 Widevine 许可证传送到 Azure 媒体服务
## <a name="overview"></a>概述
Microsoft Azure 媒体服务可让你传送使用 Widevine DRM 保护的 MPEG DASH，这些内容已根据通用加密 (CENC) 规范加密。

从媒体服务 .NET SDK 版本 3.5.2 开始，媒体服务允许你配置 Widevine 许可证模板并获取 Widevine 许可证。 还可以通过以下 AMS 合作伙伴来交付 Widevine 许可证：[Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/)、[EZDRM](http://ezdrm.com/)、[castLabs](http://castlabs.com/company/partners/azure/)。

## <a name="castlabs"></a>castLabs
可以使用 [castLabs](http://castlabs.com/company/partners/azure/) 传送 Widevine 许可证。 有关详细信息，请参阅[使用 castLabs 将 DRM 许可证传送到 Azure 媒体服务](media-services-castlabs-integration.md)

## <a name="axinom"></a>Axinom
可以使用 [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) 传送 Widevine 许可证。 有关详细信息，请参阅[使用 Axinom 将 DRM 许可证传送到 Azure 媒体服务](media-services-axinom-integration.md)

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>另请参阅
[使用 PlayReady 和/或 Widevine 动态通用加密](media-services-protect-with-drm.md)

[Mingfei 的博客](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)


