---
title: "使用 Azure 门户配置内容保护策略 | Microsoft Docs"
description: "本文演示如何使用 Azure 门户配置内容保护策略。 本文还演示如何为资产启用动态加密。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: f6d6b7b1051a22bbc865b237905f8df84e832231
ms.openlocfilehash: 4256201f2fd505ed86734e900496eb7364c9a575


---
# <a name="configuring-content-protection-policies-using-the-azure-portal"></a>使用 Azure 门户配置内容保护策略
> [!NOTE]
> 若要完成本教程，你需要一个 Azure 帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。
> 
> 

## <a name="overview"></a>概述
通过 Microsoft Azure 媒体服务 (AMS)，可以在媒体从离开计算机到存储、处理和传送的整个过程中确保其安全。 借助媒体服务，可以传送使用高级加密标准（AES，使用 128 位加密密钥）、通用加密（CENC，使用 PlayReady 和/或 Widevine DRM）以及 Apple FairPlay 传送动态加密的内容。 

AMS 提供用于向已授权客户端传送 DRM 许可证和 AES 明文密钥的服务。 通过 Azure 门户，可创建适用于所有类型加密的一个**密钥/许可证授权策略**。

本文演示如何使用 Azure 门户配置内容保护策略。 本文还演示如何为资产应用动态加密。


> [!NOTE]
> 如果使用 Azure 经典门户创建保护策略，[Azure 门户](https://portal.azure.com/)中可能不会显示策略。 但是，所有旧的策略仍然存在。 可以使用 Azure 媒体服务 .NET SDK 或 [Azure 媒体服务资源管理器](https://github.com/Azure/Azure-Media-Services-Explorer/releases)工具进行检查（若要查看这些策略，右键单击此资产 ->“显示信息 (F4)”->单击“内容密钥”选项卡 ->单击此密钥）。 
> 
> 若要使用新策略加密资产，可通过 Azure 门户对其进行配置，单击“保存”并重新应用动态加密。 
> 
> 

## <a name="start-configuring-content-protection"></a>开始配置内容保护
若要使用门户开始配置内容保护，全局到 AMS 帐户，执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 Azure 媒体服务帐户。
2. 选择“设置” > “内容保护”。

![保护内容](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>密钥/许可证授权策略
AMS 支持通过多种方式对发出密钥或许可证请求的用户进行身份验证。 必须配置内容密钥授权策略，并且客户端必须遵守该策略，才能将密钥/许可证传送到客户端。 内容密钥授权策略可能受到一种或多种授权限制：**开放**或**令牌**限制。

通过 Azure 门户，可创建适用于所有类型加密的一个**密钥/许可证授权策略**。

### <a name="open"></a>打开
开放限制意味着系统会将密钥传送到发出密钥请求的任何用户。 此限制可能适用于测试用途。 

### <a name="token"></a>令牌
令牌限制策略必须附带由安全令牌服务 (STS) 颁发的令牌。 媒体服务支持采用简单 Web 令牌 (SWT) 格式和 JSON Web 令牌 (JWT) 格式的令牌。 媒体服务不提供安全令牌服务。 你可以创建自定义 STS 或利用 Microsoft Azure ACS 来颁发令牌。 必须将 STS 配置为创建令牌，该令牌使用指定密钥以及你在令牌限制配置中指定的颁发声明进行签名。 如果令牌有效，而且令牌中的声明与为密钥（或许可证）配置的声明相匹配，则媒体服务密钥传送服务会将请求的密钥（或许可证）返回到客户端。

在配置令牌限制策略时，必须指定主验证密钥、颁发者和受众参数。 主验证密钥包含用来为令牌签名的密钥，颁发者是颁发令牌的安全令牌服务。 受众（有时称为范围）描述该令牌的意图，或者令牌授权访问的资源。 媒体服务密钥交付服务将验证令牌中的这些值是否与模板中的值匹配。

![保护内容](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-rights-template"></a>PlayReady 权限模板
有关 PlayReady 权限模板的详细信息，请参阅[媒体服务 PlayReady 许可证模板概述](media-services-playready-license-template-overview.md)。

### <a name="non-persistent"></a>非永久
如果将许可证配置为非永久，则在播放器使用许可证时，仅将其保持在内存中。  

![保护内容](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>永久
如果将许可证配置为永久，则其保存在客户端的永久性存储中。

![保护内容](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-rights-template"></a>Widevine 权限模板
有关 Widevine 权限模板的详细信息，请参阅 [Widevine 许可证模板概述](media-services-widevine-license-template-overview.md)。

### <a name="basic"></a>基本
如果选择“基本”，将使用所有默认值创建模板。

### <a name="advanced"></a>高级
有关 Widevine 配置的高级选项的详细说明，请参阅[此](media-services-widevine-license-template-overview.md)主题。

![保护内容](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay 配置
若要启用 FairPlay 加密，需要通过 FairPlay 配置选项提供应用证书和应用程序密钥 (ASK)。 有关 FairPlay 配置和要求的详细信息，请参阅[此](media-services-protect-hls-with-fairplay.md)文章。

![保护内容](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>将动态加密应用于资产
若要利用动态加密，需要将源文件编码为一组自适应比特率 MP4 文件。

### <a name="select-an-asset-that-you-want-to-encrypt"></a>选择要加密的资产
若要查看所有资产，选择“设置” > “资产”。

![保护内容](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>使用 AES 或 DRM 加密
在资产上按“加密”后，有两种选择：**AES** 或 **DRM**。 

#### <a name="aes"></a>AES
将对所有流式处理协议启用 AES 明文密钥加密：平滑流式处理、HLS 和 MPEG DASH。

![保护内容](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
如果选择“DRM”选项卡，可选择不同的内容保护策略（此时必须已配置）以及一组流式处理协议。

* **将 PlayReady 和 Widevine 应用于 MPEG-DASH** - 将使用 PlayReady 和 Widevine DRM 动态加密 MPEG-DASH 流。
* **将 PlayReady 和 Widevine 应用于 MPEG-DASH + 将 FairPlay 应用于 HLS** - 将使用 PlayReady 和 Widevine DRM 动态加密 MPEG-DASH 流。 还将使用 FairPlay 加密 HLS 流。
* **仅 PlayReady 应用于平滑流式处理、HLS 和 MPEG-DASH** - 将使用 PlayReady DRM 动态加密平滑流式处理、HLS、MPEG-DASH 流。
* **仅 Widevine 应用于 MPEG-DASH** - 将使用 Widevine DRM 动态加密 MPEG-DASH。
* **仅 FairPlay 应用于 HLS** - 将使用 FairPlay 动态加密 HLS 流。

若要启用 FairPlay 加密，则需要通过“内容保护”设置边栏选项卡的“FairPlay 配置”选项提供应用证书和应用程序密钥 (ASK)。

![保护内容](./media/media-services-portal-content-protection/media-services-content-protection009.png)

选择加密后，按“应用”。

## <a name="next-steps"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Jan17_HO2-->


