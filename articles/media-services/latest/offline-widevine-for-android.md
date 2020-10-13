---
title: 通过 Azure 媒体服务 v3 流式传输 Widevine Android
description: 本主题说明如何配置 Azure 媒体服务 v3 帐户，使其脱机流式传输 Widevine 受保护的内容。
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
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: b8c4bed81a73957cc80318064f2aa2a58b3cfe11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91597072"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>适用于 Android 的脱机 Widevine 流式处理媒体服务 v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

除了保护联机流式处理的内容之外，媒体内容订阅和租赁服务还提供可下载的内容，供用户在未连接到 Internet 时使用。 可以将内容下载到手机或平板电脑上，以便在飞行中与网络断开连接时，在飞行模式下播放。 可能需要下载内容的其他情况包括：

- 某些内容提供程序可能不允许在某个国家/地区的边界之外进行 DRM 许可证传送。 如果用户想在国外旅行期间查看内容，需要脱机下载。
- 在某些国家/地区，Internet 可用性和/或带宽受到限制。 为获得满意的观看体验，用户可以选择下载观看高分辨率的内容。

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

本文讨论如何在 Android 设备上实现受 Widevine 保护的 DASH 内容的脱机模式播放。 脱机 DRM 可用于为内容提供订阅、出租和购买模型，让使用用户服务的客户能够在与 Internet 断开连接时轻松获取内容。

对于生成 Android 播放器应用，我们概括了三个选项：

> [!div class="checklist"]
> * 使用 ExoPlayer SDK 的 Java API 生成播放器
> * 使用 ExoPlayer SDK 的 Xamarin 绑定生成播放器
> * 在 Chrome 移动浏览器 v62 或更高版本中，使用加密媒体扩展 (EME) 和媒体源扩展 (MSE) 生成播放器

本文还回答了与对受 Widevine 保护的内容进行脱机流式处理相关的一些常见问题。

> [!NOTE]
> 下载内容时，脱机 DRM 仅针对发出单个许可证请求进行计费。 任何错误都不收费。

## <a name="prerequisites"></a>先决条件 

在 Android 设备上为 Widevine 实现脱机 DRM 之前，首先应：

- 熟悉为使用 Widevine DRM 的联机内容保护引入的概念。 以下文档/示例对此进行了详细介绍：
    - [设计带访问控制的多 DRM 内容保护系统](design-multi-drm-system-with-access-control.md)
    - [使用 DRM 动态加密和许可证传送服务](protect-with-drm.md)
- 克隆 https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git 。

    需要修改[使用 .NET 加密 DRM ](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM)中的代码以添加 Widevine 配置。  
- 熟悉适用于 Android 的 Google ExoPlayer SDK，此开源视频播放器 SDK 能够支持脱机 Widevine DRM 播放。 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer 开发人员指南](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer 开发人员博客](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>在 Azure 媒体服务中配置内容保护

使用 [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) 方法时，有必要执行以下步骤：

1. 指定在许可证传送服务中如何授权内容密钥传送： 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. 配置 Widevine 许可证模板：  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. 创建 ContentKeyPolicyOptions：

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>启用脱机模式

若要为 Widevine 许可证启用“脱机”**** 模式，需要配置 [Widevine 许可证模板](widevine-license-template-overview.md)。 在“policy_overrides”对象中，将“can_persist”属性设置为“true”（默认值为 false），如 [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563) 中所示************。 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>配置 Android 播放器进行脱机播放

要开发适用于 Android 设备的本机播放器应用，最简单的方法是使用开源视频播放器 SDK [Google ExoPlayer SDK](https://github.com/google/ExoPlayer)。 ExoPlayer 支持 Android 的本机 MediaPlayer API 目前不支持的功能，包括 MPEG-短划线和 Microsoft 平滑流式处理传递协议。

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

对于某些较旧的 Android 设备，必须设置以下 policy_overrides 属性（在 [Widevine 许可证模板](widevine-license-template-overview.md) 中定义）的值：rental_duration_seconds、playback_duration_seconds 和license_duration_seconds****************。 此外，也可以将它们设置为零，表示无限/无限制的持续时间。  

值的设置须避免整数溢出 bug。 有关此问题的详细说明，请参阅 https://github.com/google/ExoPlayer/issues/3150 和 https://github.com/google/ExoPlayer/issues/3112。 <br/>如果未对值进行显式设置，则会向 PlaybackDurationRemaining 和 LicenseDurationRemaining 分配非常大的值（例如 9223372036854775807，即 64 位整数的最大正值）********。 结果，Widevine 许可证显示为过期，因此不会进行解密。 

Android 5.0 Lollipop 或更高版本中不会出现此问题，因为 Android 5.0 是首个旨在完全支持 ARMv8（[高级 RISC 计算机](https://en.wikipedia.org/wiki/ARM_architecture)）和 64 位平台的 Android 版本，而 Android 4.4 KitKat 最初设计用于支持 ARMv7 和 32 位平台，与其他较旧的 Android 版本相同。

## <a name="using-xamarin-to-build-an-android-playback-app"></a>使用 Xamarin 生成 Android 播放应用

可通过以下链接找到适用于 ExoPlayer 的 Xamarin 绑定：

- [适用于 Google ExoPlayer 库的 Xamarin 绑定库](https://github.com/martijn00/ExoPlayerXamarin)
- [适用于 ExoPlayer NuGet 的 Xamarin 绑定](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

另请参阅以下线程：[Xamarin 绑定](https://github.com/martijn00/ExoPlayerXamarin/pull/57)。 

## <a name="chrome-player-apps-for-android"></a>适用于 Android 的 Chrome 播放器应用

从 [适用于 Android 的 Chrome 版本 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates)开始，支持 EME 中的永久许可证。 Chrome for Android 中当前也支持 [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1)。 如果你的最终用户使用此版本（或更高版本）的 Chrome，则你可在 Chrome 中创建脱机播放应用程序。 

此外，Google 已生成渐进式 Web 应用 (PWA) 示例并已开放其源代码： 

- [源代码](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google 托管版本](https://biograf-155113.appspot.com/ttt/episode-2/)（仅适用于 Android 设备上的 Chrome v 62 及更高版本）

如果将 Android 手机上的移动 Chrome 浏览器升级到 v62 （或更高版本）并测试上面的托管示例应用，可发现联机流式处理和脱机播放均正常运行。

上面的开源 PWA 应用是在 Node.js 中编写的。 如果希望在 Ubuntu 服务器上托管自己的版本，请注意以下可能会阻止播放的常见问题：

1. CORS 问题：示例应用中的示例视频在 https://storage.googleapis.com/biograf-video-files/videos/ 中托管。 Google 已为其托管在 Google 云存储桶中的所有测试示例设置了 CORS。 它们会使用 CORS 标头，显式指定 CORS 条目：`https://biograf-155113.appspot.com`（google 托管其示例的域），从而阻止任何其他站点的访问。 如果尝试，将看到以下 HTTP 错误： `Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. 证书问题：从 Chrome v 58 开始，EME for Widevine 需要 HTTPS。 因此，需要使用 X509 证书通过 HTTPS 来托管示例应用。 常用的测试证书由于以下要求而无法使用：需要获取满足以下最低要求的证书：
    - Chrome 和 Firefox 要求证书中具备 SAN 使用者可选名称设置
    - 证书必须具备受信任的 CA，自签名开发证书无法使用
    - 该证书必须具备与 Web 服务器或网关的 DNS 名称匹配的 CN

## <a name="faqs"></a>常见问题解答

有关详细信息，请参阅 [Widevine faq](frequently-asked-questions.md#widevine-streaming-for-android)。

## <a name="additional-notes"></a>附加说明

Widevine 是 Google Inc. 提供的一项服务，并受 Google Inc. 服务条款和隐私策略的约束。

## <a name="summary"></a>总结

本文讨论了如何在 Android 设备上实现受 Widevine 保护的 DASH 内容的脱机模式播放。  本文还回答了与对受 Widevine 保护的内容进行脱机流式处理相关的一些常见问题。
