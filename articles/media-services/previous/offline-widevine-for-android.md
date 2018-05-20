---
title: 配置帐户以对受 Widevine 保护的内容进行脱机流式处理 - Azure
description: 本主题演示如何配置 Azure 媒体服务帐户，以对受 Widevine 保护的内容进行脱机流式处理。
services: media-services
keywords: DASH, DRM, Widevine 脱机模式, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: 158b58c13aee4d6241900db4a5e2b3fe8a45cc3c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="offline-widevine-streaming-for-android"></a>适用于 Android 的脱机 Widevine 流式处理

除了保护联机流式处理的内容之外，媒体内容订阅和租赁服务还提供可下载的内容，供用户在未连接到 Internet 时使用。 可以将内容下载到手机或平板电脑上，以便在飞行中与网络断开连接时，在飞行模式下播放。 可能需要下载内容的其他情况包括：

- 某些内容提供程序可能不允许在某个国家/地区边界之外进行 DRM 许可证传送。 如果用户想在国外旅行期间查看内容，需要脱机下载。
- 在某些国家/地区，Internet 可用性和/或宽带受到限制。 为获得满意的观看体验，用户可以选择下载观看高分辨率的内容。

本文讨论如何在 Android 设备上实现受 Widevine 保护的 DASH 内容的脱机模式播放。 脱机 DRM 可用于为内容提供订阅、出租和购买模型，让使用用户服务的客户能够在与 Internet 断开连接时轻松获取内容。

对于生成 Android 播放器应用，我们概括了三个选项：

> [!div class="checklist"]
> * 使用 ExoPlayer SDK 的 Java API 生成播放器
> * 使用 ExoPlayer SDK 的 Xamarin 绑定生成播放器
> * 在 Chrome 移动浏览器 v62 或更高版本中，使用加密媒体扩展 (EME) 和媒体源扩展 (MSE) 生成播放器

本文还回答了与对受 Widevine 保护的内容进行脱机流式处理相关的一些常见问题。

## <a name="requirements"></a>要求 

在 Android 设备上为 Widevine 实现脱机 DRM 之前，首先应：

- 熟悉为使用 Widevine DRM 的联机内容保护引入的概念。 以下文档/示例对此进行了详细介绍：
    - [使用 Azure 媒体服务传送 DRM 许可证或 AES 密钥](media-services-deliver-keys-and-licenses.md)
    - [使用多重 DRM 的 CENC 和访问控制：Azure 与 Azure 媒体服务的参考设计和实现](media-services-cenc-with-multidrm-access-control.md)
    - [Using PlayReady and/or Widevine Dynamic Common Encryption with .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)（将 PlayReady 和/或 Widevine 动态通用加密与 .NET 结合使用）
    - [Use Azure Media Services to deliver PlayReady and/or Widevine licenses with .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)（借助 .NET 使用 Azure 媒体服务传送 PlayReady 和/或 Widevine 许可证）
- 熟悉适用于 Android 的 Google ExoPlayer SDK，此开源视频播放器 SDK 能够支持脱机 Widevine DRM 播放。 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer 开发人员指南](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer 开发人员博客](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Azure 媒体服务中的内容保护配置

在媒体服务中配置某个资产的 Widevine 保护时，需要创建 ContentKeyAuthorizationPolicyOption，它指定以下三项内容：

1. DRM 系统 (Widevine)
2. ContentKeyAuthorizationPolicyRestriction，指定在许可证传送服务中授权内容密钥传送的方式（开放授权或令牌授权）
3. DRM (Widevine) 许可证模板

若要为 Widevine 许可证启用“脱机”模式，需要配置 [Widevine 许可证模板](media-services-widevine-license-template-overview.md)。 在 policy_overrides 对象中，将 can_persist 属性设置为 true（默认值为 false）。 

下面的代码示例使用 .NET 来为 Widevine 许可证启用“脱机”模式。 此代码基于 [Using PlayReady and/or Widevine Dynamic Common Encryption with .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)（将 PlayReady 和/或 Widevine 动态通用加密与 .NET 结合使用）示例。 

```
private static string ConfigureWidevineLicenseTemplateOffline(Uri keyDeliveryUrl)
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            //can_renew = true,                             //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),   //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
            can_renew = false,
            //rental_duration_seconds = 1209600,
            //playback_duration_seconds = 1209600,
            //license_duration_seconds = 1209600
        }
        };

    string configuration = Newtonsoft.Json.JsonConvert.SerializeObject(template);
    return configuration;
}
```

## <a name="configuring-the-android-player-for-offline-playback"></a>配置 Android 播放器进行脱机播放

要开发适用于 Android 设备的本机播放器应用，最简单的方法是使用开源视频播放器 SDK [Google ExoPlayer SDK](https://github.com/google/ExoPlayer)。 ExoPlayer 支持 Android 的本机 MediaPlayer API 目前不支持的功能，包括 MPEG-DASH 和 Microsoft 平滑流式处理传递协议。

ExoPlayer 2.6 和更高版本包括许多支持脱机 Widevine DRM 播放的类。 具体而言，OfflineLicenseHelper 类提供实用工具函数，以便使用 DefaultDrmSessionManager 来下载、续订和发布脱机许可证。 SDK 文件夹“library/core/src/main/java/com/google/android/exoplayer2/offline/”中提供的类支持脱机视频内容下载。

下面列出的类有助于通过适用于 Android 的 ExoPlayer SDK 实现脱机模式：

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- library/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

在开发应用程序的过程中，开发人员应参考 [ExoPlayer 开发人员指南](https://google.github.io/ExoPlayer/guide.html)和相应的[开发人员博客](https://medium.com/google-exoplayer)。 目前，Google 尚未针对支持 Widevine 脱机的 ExoPlayer 应用发布完整记录的参考实现或示例代码，因此这些信息仅限于开发人员指南和博客。 

### <a name="working-with-older-android-devices"></a>使用较旧的 Android 设备

对于某些较旧的 Android 设备，必须设置以下 policy_overrides 属性（在 [Widevine 许可证模板](media-services-widevine-license-template-overview.md) 中定义）的值：rental_duration_seconds、playback_duration_seconds 和license_duration_seconds。 此外，也可以将它们设置为零，表示无限/无限制的持续时间。  

值的设置须避免整数溢出 bug。 有关此问题的详细说明，请参阅 https://github.com/google/ExoPlayer/issues/3150 和 https://github.com/google/ExoPlayer/issues/3112。 <br/>如果未对值进行显式设置，则会向 PlaybackDurationRemaining 和 LicenseDurationRemaining 分配非常大的值（例如 9223372036854775807，即 64 位整数的最大正值）。 结果，Widevine 许可证显示为过期，因此不会进行解密。 

Android 5.0 Lollipop 或更高版本中不会出现此问题，因为 Android 5.0 是首个旨在完全支持 ARMv8（[高级 RISC 计算机](https://en.wikipedia.org/wiki/ARM_architecture)）和 64 位平台的 Android 版本，而 Android 4.4 KitKat 最初设计用于支持 ARMv7 和 32 位平台，与其他较旧的 Android 版本相同。

## <a name="using-xamarin-to-build-an-android-playback-app"></a>使用 Xamarin 生成 Android 播放应用

可通过以下链接找到适用于 ExoPlayer 的 Xamarin 绑定：

- [适用于 Google ExoPlayer 库的 Xamarin 绑定库](https://github.com/martijn00/ExoPlayerXamarin)
- [适用于 ExoPlayer NuGet 的 Xamarin 绑定](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

另请参阅以下线程：[Xamarin 绑定](https://github.com/martijn00/ExoPlayerXamarin/pull/57)。 

## <a name="chrome-player-apps-for-android"></a>适用于 Android 的 Chrome 播放器应用

[Chrome for Android v.62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates) 及更高版本支持 EME 中的永久许可证。 Chrome for Android 中当前也支持 [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1)。 如果你的最终用户使用此版本（或更高版本）的 Chrome，则你可在 Chrome 中创建脱机播放应用程序。 

此外，Google 已生成渐进式 Web 应用 (PWA) 示例并已开放其源代码： 

- [源代码](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google 托管版本](https://biograf-155113.appspot.com/ttt/episode-2/)（仅适用于 Android 设备上的 Chrome v 62 及更高版本）

如果将 Android 手机上的移动 Chrome 浏览器升级到 v62 （或更高版本）并测试上面的托管示例应用，可发现联机流式处理和脱机播放均正常运行。

上面的开源 PWA 应用是在 Node.js 中编写的。 如果希望在 Ubuntu 服务器上托管自己的版本，请注意以下可能会阻止播放的常见问题：

1. CORS 问题：示例应用中的示例视频在 https://storage.googleapis.com/biograf-video-files/videos/ 中托管。 Google 已为其托管在 Google 云存储桶中的所有测试示例设置了 CORS。 它们会使用 CORS 标头，显式指定 CORS 条目：https://biograf-155113.appspot.com（google 托管其示例的域），从而阻止任何其他站点的访问。 如果尝试访问，将看到以下 HTTP 错误：未能加载 https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd:: 请求的资源中未出现 "Access-Control-Allow-Origin" 标头。 因此不允许源“https://13.85.80.81:8080”进行访问。 如果非跳转响应可满足需求，请将请求的模式设置为“no-cors”，以便在禁用 CORS 的情况下提取资源。
2. 证书问题：从 Chrome v 58 开始，EME for Widevine 需要 HTTPS。 因此，需要使用 X509 证书通过 HTTPS 来托管示例应用。 常用的测试证书由于以下要求而无法使用：需要获取满足以下最低要求的证书：
    - Chrome 和 Firefox 要求证书中具备 SAN 使用者可选名称设置
    - 证书必须具备受信任的 CA，自签名开发证书无法使用
    - 该证书必须具备与 Web 服务器或网关的 DNS 名称匹配的 CN

## <a name="frequently-asked-questions"></a>常见问题

### <a name="question"></a>问题

如何为某些客户端/用户传送永久许可证（允许脱机）并为其他人传送非永久许可证（禁用脱机）？ 是否必须复制内容并使用单独的内容密钥？

### <a name="answer"></a>Answer
不需要复制内容。 只需使用单个内容副本和单个 ContentKeyAuthorizationPolicy，但需使用两个单独的 ContentKeyAuthorizationPolicyOption：

1. IContentKeyAuthorizationPolicyOption 1：使用永久许可证和包含如 license_type = “Persistent” 等声明的 ContentKeyAuthorizationPolicyRestriction 1
2. IContentKeyAuthorizationPolicyOption 2：使用非永久许可证和包含如 license_type = “Nonpersistent” 等声明的 ContentKeyAuthorizationPolicyRestriction 2

这样，如果许可证请求来自客户端应用，则许可证请求中没有任何区别。 但是，对于不同的最终用户/设备，STS 应当具有业务逻辑，可发布包含不同声明（上述两个 license_type 之一）的不同 JWT 令牌。 JWT 令牌中的声明值由许可证服务用于决定发出哪种类型的许可证：永久或非永久。

这意味着安全令牌服务 (STS) 需要具有业务逻辑和客户端/设备信息，以便将相应的声明值添加到令牌中。

### <a name="question"></a>问题

对于 Widevine 安全级别，在 Google 的 [Widevine DRM Architecture Overview doc](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)（Widevine DRM 体系结构概述文档）中，定义了三种不同的安全级别。 但是，在 [Widevine 许可证模板上的 Azure 媒体服务文档](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)中，概述了五种不同的安全级别。 这两组不同安全级别之间的关系或映射是什么？

### <a name="answer"></a>Answer

在 Google 的 [Widevine DRM Architecture Overview](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)（Widevine DRM 体系结构概述）中，定义以下三种安全级别：

1.  安全级别 1：所有内容处理、加密和控制操作均在受信任的执行环境 (TEE) 中进行。 在某些实现模型中，可在不同的芯片中执行安全处理。
2.  安全级别 2：在 TEE 中执行加密（但不是视频处理）：已解密的缓冲区返回到应用程序域，并通过单独的视频硬件或软件进行处理。 但是，在级别 2，加密信息仍仅在 TEE 中处理。
3.  安全级别 3：设备上没有 TEE。 可采取相应措施来保护主机操作系统中的加密信息和已解密内容。 级别 3 实现还包括硬件加密引擎，但只能增强性能，而不能增强安全性。

同时，在 [Widevine 许可证模板上的 Azure 媒体服务文档](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)中，content_key_specs 的 security_level 属性可以具有以下五个不同的值（播放的客户端稳定性要求）：

1.  需要基于软件的白盒加密。
2.  需要软件加密和模糊处理解码器。
3.  密钥材料和加密操作必须在硬件支持的 TEE 中执行。
4.  内容加密和解码必须在硬件支持的 TEE 中执行。
5.  加密、解码与媒体（压缩和未压缩）的所有处理必须在硬件支持的 TEE 中处理。

这两种安全级别均由 Google Widevine 定义。 不同之处是其使用情况级别：体系结构级别或 API 级别。 Widevine API 中使用了这五种安全级别。 ontent_key_specs 对象包含 security_level，该对象被反序列化，并通过 Azure 媒体服务 Widevine 许可证服务传递给 Widevine 全球传送服务。 下表显示两组安全级别之间的映射。

| **Widevine 体系结构中定义的安全级别** |**Widevine API 中使用的安全级别**|
|---|---| 
| **安全级别 1**：所有内容处理、加密和控制操作均在受信任的执行环境 (TEE) 中进行。 在某些实现模型中，可在不同的芯片中执行安全处理。|**security_level=5**：加密、解码与媒体（压缩和未压缩）的所有处理必须在硬件支持的 TEE 中处理。<br/><br/>**security_level=4**：内容加密和解码必须在硬件支持的 TEE 中执行。|
**安全级别 2**：在 TEE 中执行加密（但不是视频处理）：已解密的缓冲区返回到应用程序域，并通过单独的视频硬件或软件进行处理。 但是，在级别 2，加密信息仍仅在 TEE 中处理。| **security_level=3**：密钥材料和加密操作必须在硬件支持的 TEE 中执行。 |
| **安全级别 3**：设备上没有 TEE。 可采取相应措施来保护主机操作系统中的加密信息和已解密内容。 级别 3 实现还包括硬件加密引擎，但只能增强性能，而不能增强安全性。 | **security_level=2**：需要软件加密和模糊处理解码器。<br/><br/>**security_level=1**：需要基于软件的白盒加密。|

### <a name="question"></a>问题

为什么下载内容需要很长时间？

### <a name="answer"></a>Answer

可通过两种方法提高下载速度：

1.  启用 CDN，使最终用户更容易命中内容下载的 CDN，而不是源/流式处理终结点。 如果用户命中流式处理终结点，会动态打包和加密每个 HLS 段或 DASH 片段。 即使每个段/片段的延迟时间都只有几毫秒，如果视频时间长达一个小时，累积延迟可能会很长，从而导致下载时间变长。
2.  让最终用户能够选择性地下载视频质量层和音轨，而不是所有内容。 对于脱机模式，无需下载所有的质量层。 可通过两种方式实现此目的：
    1.  客户端控制：播放器应用自动选择要下载的视频质量层和音轨，或由用户选择；
    2.  服务控制：可使用 Azure 媒体服务中的动态清单功能创建（全局）筛选器，将 HLS 播放列表或 DASH MPD 限制为单个视频质量层和所选音轨。 然后，向最终用户呈现的下载 URL 会包括此筛选器。

## <a name="summary"></a>摘要

本文讨论了如何在 Android 设备上实现受 Widevine 保护的 DASH 内容的脱机模式播放。  本文还回答了与对受 Widevine 保护的内容进行脱机流式处理相关的一些常见问题。
