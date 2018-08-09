---
title: 使用 Azure 门户配置内容保护策略 | Microsoft Docs
description: 本文演示如何使用 Azure 门户配置内容保护策略。 本文还演示如何为资产启用动态加密。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: c46faf2298ebaac4f40fb1d18cbfca83076e0d4f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423527"
---
# <a name="configure-content-protection-policies-by-using-the-azure-portal"></a>使用 Azure 门户配置内容保护策略
 使用 Azure 媒体服务，可以在媒体从离开计算机到存储、处理和传送的整个过程中确保其安全。 可以通过媒体服务传送按高级加密标准 (AES)（使用 128 位加密密钥）动态加密的内容。 也可借助 PlayReady 和/或 Widevine 数字版权管理 (DRM) 以及 Apple FairPlay，将其与通用加密 (CENC) 配合使用。 

媒体服务提供用于向已授权客户端传送 DRM 许可证和 AES 明文密钥的服务。 可以使用 Azure 门户创建一个适用于所有类型的加密的密钥/许可证授权策略。

本文演示如何使用门户配置内容保护策略。 本文还演示如何为资产应用动态加密。

## <a name="start-to-configure-content-protection"></a>开始配置内容保护
若要通过门户使用媒体服务帐户来配置全局内容保护，请执行以下步骤：

1. 在[门户](https://portal.azure.com/)中选择媒体服务帐户。

1. 选择“设置” > “内容保护”。

    ![内容保护](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>密钥/许可证授权策略
媒体服务支持通过多种方式对发出密钥或许可证请求的用户进行身份验证。 必须配置内容密钥授权策略。 然后，客户端必须符合策略，系统才会向其传送密钥/许可证。 内容密钥授权策略可能有一种或多种授权限制：开放或令牌限制。

可以使用门户创建一个适用于所有类型的加密的密钥/许可证授权策略。

### <a name="open-authorization"></a>开放授权
“开放”限制意味着系统会将密钥传送到发出密钥请求的任何用户。 此限制可能适用于测试用途。 

### <a name="token-authorization"></a>令牌授权
令牌限制策略必须附带由安全令牌服务 (STS) 颁发的令牌。 媒体服务支持采用简单 Web 令牌 (SWT) 格式和 JSON Web 令牌 (JWT) 格式的令牌。 媒体服务不提供 STS。 可以创建自定义 STS 或使用 Azure 访问控制服务来颁发令牌。 必须将 STS 配置为创建令牌，该令牌使用指定密钥以及在令牌限制配置中指定的颁发声明进行签名。 如果令牌有效，而且令牌中的声明与为密钥（或许可证）配置的声明相匹配，则媒体服务密钥传送服务会将请求的密钥（或许可证）返回到客户端。

配置令牌限制策略时，必须指定主验证密钥、颁发者和受众参数。 主验证密钥包含为令牌签名时使用的密钥。 颁发者是颁发令牌的安全令牌服务。 受众（有时称为范围）描述该令牌的意图，或者令牌授权访问的资源。 媒体服务密钥交付服务会验证令牌中的这些值是否与模板中的值匹配。

![密钥/许可证授权策略](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-license-template"></a>PlayReady 许可证模板
PlayReady 许可证模板设置在 PlayReady 许可证上启用的功能。 有关 PlayReady 许可证模板的详细信息，请参阅[媒体服务 PlayReady 许可证模板概述](media-services-playready-license-template-overview.md)。

### <a name="nonpersistent"></a>非永久性
如果将许可证配置为非永久性许可证，则当播放器使用它时，它仅存储在内存中。  

![非永久性内容保护](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>永久
如果将许可证配置为永久性许可证，它会保存在客户端的永久性存储中。

![永久性内容保护](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-license-template"></a>Widevine 许可证模板
Widevine 许可证模板设置在 Widevine 许可证上启用的功能。

### <a name="basic"></a>基本
如果选择“基本”，将使用所有默认值创建模板。

### <a name="advanced"></a>高级
有关 Widevine 权限模板的详细信息，请参阅 [Widevine 许可证模板概述](media-services-widevine-license-template-overview.md)。

![高级内容保护](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay 配置
若要启用 FairPlay 加密，请选择“FairPlay 配置”。 然后选择“应用证书”并输入**应用程序机密密钥**。 有关 FairPlay 配置和要求的详细信息，请参阅[使用 Apple FairPlay 或 Microsoft PlayReady 保护 HLS 内容](media-services-protect-hls-with-FairPlay.md)。

![FairPlay 配置](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>将动态加密应用于资产
若要利用动态加密，请将源文件编码为一组自适应比特率 MP4 文件。

### <a name="select-an-asset-that-you-want-to-encrypt"></a>选择要加密的资产
若要查看所有资产，选择“设置” > “资产”。

![“资产”选项](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>使用 AES 或 DRM 加密
针对资产选择“加密”时，会看到两个选择：**AES** 或 **DRM**。 

#### <a name="aes"></a>AES
对所有流式处理协议启用 AES 明文密钥加密：平滑流式处理、HLS 和 MPEG DASH。

![加密配置](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
1. 选择“DRM”后，会看到不同的内容保护策略（必须通过此点进行配置）和一组流式处理协议：

    a. **将 PlayReady 和 Widevine 应用于 MPEG-DASH** 即可通过 PlayReady 和 Widevine DRM 动态加密 MPEG-DASH 流。

    b. **将 PlayReady 和 Widevine 应用于 MPEG-DASH + 将 FairPlay 应用于 HLS** 即可通过 PlayReady 和 Widevine DRM 动态加密 MPEG-DASH 流。 此选项还使用 FairPlay 加密 HLS 流。

    c. **仅将 PlayReady 应用于平滑流式处理、HLS 和 MPEG-DASH** 即可通过 PlayReady DRM 动态加密平滑流式处理、HLS 和 MPEG-DASH 流。

    d. **仅将 Widevine 应用于 MPEG-DASH** 即可通过 Widevine DRM 动态加密 MPEG-DASH。
    
    e. **仅将 FairPlay 应用于 HLS** 即可通过 FairPlay 动态加密 HLS 流。

1. 若要启用 FairPlay 加密，请在“内容保护全局设置”边栏选项卡上选择“FairPlay 配置”。 然后选择“应用证书”并输入**应用程序机密密钥**。

    ![加密类型](./media/media-services-portal-content-protection/media-services-content-protection009.png)

1. 进行加密选择后，选择“应用”。

>[!NOTE] 
>如果打算在 Safari 中播放 AES 加密的 HLS，请参阅博客文章：[Safari 中加密的 HLS](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)。

## <a name="next-steps"></a>后续步骤
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

