---
title: 使用 Azure 媒体服务来保护内容 | Microsoft 文档
description: 本文概述了如何使用媒体服务来保护内容。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2018
ms.author: juliako
ms.openlocfilehash: 2f0996482c599a664d02e172dcb20cda4e039af5
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341658"
---
# <a name="content-protection-overview"></a>内容保护概述

可以使用 Azure 媒体服务在媒体从计算机离开到存储、处理和传送的整个过程中确保其安全。 借助媒体服务，可以传送使用高级加密标准 (AES-128) 或三个主要数字版权管理 (DRM) 系统（Microsoft PlayReady、Google Widevine 和 Apple FairPlay）中任意一个动态加密的实时和请求内容。 媒体服务还提供了用于向已授权客户端传送 AES 密钥和 DRM（PlayReady、Widevine 和 FairPlay）许可证的服务。 

下图阐释了媒体服务内容保护工作流： 

![保护内容](./media/content-protection/content-protection.png)

&#42; *动态加密支持 AES-128“明文密钥”、CBCS 和 CENC。有关详细信息，请参阅[此处](#streaming-protocols-and-encryption-types)的支持矩阵。*

本文介绍与了解使用媒体服务进行内容保护相关的概念和术语。 本文还提供指向讨论如何保护内容的文章的链接。 

## <a name="main-components-of-the-content-protection-system"></a>内容保护系统的主要组件

若要成功完成“内容保护”系统/应用程序设计中，需要全面了解工作范围。 以下列表概述了需要实施的三个部分。 

1. Azure 媒体服务代码
  
  * PlayReady、Widevine 和/或 FairPlay 的许可证模板。 使用这些模板可为使用的每个 DRM 配置权利和权限
  * 许可证交付授权，指定基于 JWT 中的声明执行的授权检查的逻辑
  * 应用的内容密钥、流式处理协议和相应的 DRM，定义 DRM 加密

  > [!NOTE]
  > 可以使用多个加密类型（AES-128、PlayReady、Widevine、FairPlay）来加密每个资产。 请参阅[流式处理协议和加密类型](#streaming-protocols-and-encryption-types)，以了解有效的组合方式。
  
  以下文章介绍了使用 AES 加密内容的步骤：[使用 AES 加密进行保护](protect-with-aes128.md)
 
2. 使用 AES 或 DRM 客户端的播放器。 基于播放器 SDK 的视频播放器应用（本机或基于浏览器）需要满足以下要求：
  * 播放器 SDK 支持所需的 DRM 客户端
  * 播放器 SDK 支持所需的流式处理协议：平滑流、DASH 和/或 HLS
  * 播放器 SDK 需要能够处理许可证获取请求中 JWT 令牌的传递
  
    可以使用 [Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/) 创建播放器。 通过 [Azure 媒体播放器的 ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) 指定要在不同的 DRM 平台上使用哪种 DRM 技术。

    若要测试 AES 或 CENC (Widevine + PlayReady) 加密内容，可以使用 [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html)。 请务必单击“高级选项”，选中“AES”，并提供令牌。

    若要测试 FairPlay 加密内容，请使用[此测试播放器](http://aka.ms/amtest)。 该播放器支持 Widevine、PlayReady 和 FairPlay DRM，以及 AES-128 明文密钥加密。 需要选择适当的浏览器来测试不同的 DRM：适用于 Widevine 的 Chrome/Opera/Firefox、适用于 PlayReady 的 MS Edge/IE11、适用于 FairPlay 的 Safari on maOS。

3. 安全令牌服务 (STS)，颁发 JSON Web 令牌 (JWT) 作为用于访问后端资源的访问令牌。 可以使用 AMS 许可证传送服务作为后端资源。 STS 必须定义以下信息：

  * 颁发者和受众（或范围）
  * 与内容保护中的业务要求相关的声明
  * 签名的对称或非对称验证
  * 密钥滚动更新支持（如有必要）

    可以使用[此 STS 工具](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt)测试 STS，此工具支持所有 3 种类型的验证密钥：对称、非对称，或者 AAD 与密钥滚动。 

> [!NOTE]
> 强烈建议在移到下一部分之前，将重点放在每个部分的完整测试上。 若要测试“内容保护”系统，请使用上述列表中指定的工具。  

## <a name="streaming-protocols-and-encryption-types"></a>流式处理协议和加密类型

可以通过 Azure 媒体服务传送使用 AES 明文密钥或 DRM 加密（利用 PlayReady、Widevine 或 FairPlay）动态加密的内容。 当前可以加密 HTTP Live Streaming (HLS)、MPEG DASH 和平滑流式处理格式。 每个协议支持以下加密方法：

|协议|容器格式|加密方案|
|---|---|---|---|
|MPEG-DASH|全部|AES|
||CSF(fmp4) |CENC (Widevine + PlayReady) |
||CMAF(fmp4)|CENC (Widevine + PlayReady)|
|HLS|全部|AES|
||MPG2-TS |CBCS (Fairplay) |
||MPG2-TS |CENC (PlayReady) |
||CMAF(fmp4) |CENC (PlayReady) |
|平滑流|fMP4|AES|
||fMP4 | CENC (PlayReady) |

## <a name="dynamic-encryption"></a>动态加密

在媒体服务 v3 中，内容密钥与 StreamingLocator 相关联（参阅[此示例](protect-with-aes128.md)）。 如果使用媒体服务密钥传送服务，应自动生成内容密钥。 如果使用自己的密钥传送服务，或者需要处理高可用性方案（需要在两个数据中心使用相同的内容密钥），则应自行生成内容密钥。

播放器请求流时，媒体服务将通过 AES 明文密钥或 DRM 加密使用指定的密钥来动态加密内容。 为了解密流，播放器将从媒体服务密钥传送服务或者指定的密钥传送服务请求密钥。 为了确定用户是否被授权获取密钥，服务将评估你为密钥指定的授权策略。

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 明文密钥与DRM

客户通常希望知道他们应该使用 AES 加密还是 DRM 系统。 这两种系统之间的主要区别是，使用 AES 加密时，内容密钥以未加密格式（“明文”）传输到客户端。 因此，可以通过网络跟踪在客户端上明文查看用于加密内容的密钥。 AES-128 明文密钥加密适合查看者是受信任方的用例（例如，员工查看公司内分发的加密公司视频）。

与 AES-128 明文密钥加密相比，PlayReady、Widevine 和 FairPlay 均提供更高等级的加密。 内容密钥以加密格式传输。 此外，解密是操作系统级别在安全的环境中处理的，在这样的环境中，恶意用户更难进行攻击。 在查看者可能不是受信任方且需要更高等级的安全性的用例中，建议使用 DRM。

## <a name="storage-side-encryption"></a>存储端加密

若要保护静态资产，应通过存储端加密对资产进行加密。 下表显示了存储端加密在媒体服务 v3 中的工作方式：

|加密选项|说明|媒体服务 v3|
|---|---|---|---|
|媒体服务存储加密| AES-256 加密，媒体服务管理的密钥|不支持<sup>(1)</sup>|
|[静态数据的存储服务加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|由 Azure 存储提供的服务器端加密，由 Azure 或客户管理的密钥|支持|
|[存储客户端加密](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|由 Azure 存储提供的客户端加密，由 Key Vault 中的客户管理的密钥|不支持|

<sup>1</sup> 在媒体服务 v3 中，仅当资产是使用媒体服务 v2 创建的时才支持存储加密（AES-256 加密）以实现向后兼容性。 这意味着 v3 会处理现有的存储加密资产，但不会允许创建新资产。

## <a name="licenses-and-keys-delivery-service"></a>许可证和密钥传送服务

媒体服务提供用于向已授权客户端传送 DRM（PlayReady、Widevine 和 FairPlay）许可证和 AES 密钥的密钥传送服务。 可以使用 REST API 或媒体服务客户端库来配置许可证和密钥的授权与身份验证策略。

## <a name="control-content-access"></a>控制内容访问

可以通过配置内容密钥策略来控制谁有权访问内容。 媒体服务支持通过多种方式对发出密钥请求的用户进行身份验证。 必须配置内容密钥策略。 客户端（播放器）必须符合该策略才能将密钥传送到客户端。 内容密钥策略可以采用**开放**或**令牌**限制。 

使用令牌限制的内容密钥策略时，内容密钥仅发送到在密钥/许可证请求中提供了有效 JSON Web 令牌 (JWT) 或简单 Web 令牌 (SWT) 的客户端。 此令牌必须是由安全令牌服务 (STS) 颁发的。 可以使用 Azure Active Directory 作为 STS，也可以部署自定义 STS。 必须将 STS 配置为创建令牌，该令牌使用指定密钥以及在令牌限制配置中指定的颁发声明进行签名。 如果令牌有效，而且令牌中的声明与为密钥/许可证配置的声明相匹配，则媒体服务密钥传送服务会将所请求的密钥/许可证返回到客户端。

配置令牌限制策略时，必须指定主验证密钥、颁发者和受众参数。 主验证密钥包含为令牌签名时使用的密钥。 颁发者是颁发令牌的安全令牌服务。 受众（有时称为范围）描述该令牌的意图，或者令牌授权访问的资源。 媒体服务密钥交付服务会验证令牌中的这些值是否与模板中的值匹配。

## <a name="streaming-urls"></a>流 URL

如果资产是使用多个 DRM 加密的，请在流式处理 URL 中使用加密标记：(format='m3u8-aapl', encryption='xxx')。

请注意以下事项：

* 如果仅向资产应用了一种加密，则无需在 URL 中指定加密类型。
* 加密类型不区分大小写。
* 可以指定以下加密类型：
  * **cenc**：对于 PlayReady 或 Widevine（通用加密）
  * **cbcs-aapl**：对于 FairPlay（AES CBC 加密）
  * **cbc**：对于 AES 信封加密

## <a name="next-steps"></a>后续步骤

[如何使用媒体服务 v3 中的 AES 加密进行保护](protect-with-aes128.md)

可以在 [DRM 参考设计和实施](../previous/media-services-cenc-with-multidrm-access-control.md)中找到更多信息


