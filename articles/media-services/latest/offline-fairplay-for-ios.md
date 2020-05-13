---
title: 适用于 iOS 的脱机 FairPlay 流式处理与 Azure 媒体服务 v3
description: 本主题概括介绍并演示了如何使用 Azure 媒体服务以脱机模式通过 Apple FairPlay 动态加密 HTTP Live Streaming (HLS) 内容。
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), 脱机, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 0e65bf39db00f1277635d600da87346f19a881a6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197166"
---
# <a name="offline-fairplay-streaming-for-ios-with-media-services-v3"></a>适用于 iOS 的脱机 FairPlay 流式处理与媒体服务 v3

 Azure 媒体服务提供一套设计良好的[内容保护服务](https://azure.microsoft.com/services/media-services/content-protection/)，包括：

- Microsoft PlayReady
- Google Widevine
    
    Widevine 是 Google Inc. 提供的一项服务，并受 Google Inc. 服务条款和隐私策略的约束。
- Apple FairPlay
- AES-128 加密

数字版权管理 (DRM)/高级加密标准 (AES) 基于各种流式处理协议的请求，动态执行内容的 DRM/AES 加密。 媒体服务还提供 DRM 许可证/AES 解密密钥传送服务。

除通过各种流式处理协议对联机流式处理的内容提供保护外，通常还要求提供受保护内容的脱机模式功能。 以下情况需要脱机模式支持：

* 在 Internet 连接不可用（如旅行期间）时播放。
* 某些内容提供程序可能不允许在某个国家/地区的边界之外进行 DRM 许可证传送。 如果用户想在该国家/地区外旅行期间查看内容，需要脱机下载。
* 在某些国家/地区，Internet 可用性和/或宽带仍然受到限制。 为获得满意的观看体验，用户可能选择首先下载以便能够观看高分辨率的内容。 在此情况下，通常问题不在于网络可用性，而在于受限的网络宽带。 此时，Over-the-Top (OTT)/联机视频平台 (OVP) 提供商会请求脱机模式支持。

本文介绍 FairPlay Streaming (FPS) 脱机模式支持，适用于运行 iOS 10 或更高版本的设备。 此功能不支持其他 Apple 平台，例如 watchOS、tvOS 或 macOS 上的 Safari。

> [!NOTE]
> 下载内容时，脱机 DRM 仅针对发出单个许可证请求进行计费。 任何错误都不收费。

## <a name="prerequisites"></a>先决条件

在 iOS 10+ 设备上为 FairPlay 实现 脱机 DRM 之前：

* 查看 FairPlay 的联机内容保护： 

    - [Apple FairPlay 许可要求和配置](fairplay-license-overview.md)
    - [使用 DRM 动态加密和许可证传送服务](protect-with-drm.md)
    - 包含联机 FPS 流式处理配置的 .NET 示例：[ConfigureFairPlayPolicyOptions](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L505)
* 从 Apple Developer Network 获取 FPS SDK。 FPS SDK 包含两个组件：

    - FPS Server SDK，该组件包含密钥安全模块 (KSM)、客户端示例、规格和一组测试矢量。
    - FPS 开发包，该组件包含 D 函数、规格和 FPS 证书生成说明、客户特定的私钥以及应用程序密钥。 Apple 仅对许可的内容提供商发布 FPS 开发包。
* 克隆 https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git 。 

    需要修改[使用 .NET 通过 DRM 加密](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM)中的代码以添加 FairPlay 配置。  

## <a name="configure-content-protection-in-azure-media-services"></a>在 Azure 媒体服务中配置内容保护

在 [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) 方法中，执行以下操作：

取消评论配置 FairPlay 策略选项的代码：

```csharp
ContentKeyPolicyFairPlayConfiguration fairplayConfig = ConfigureFairPlayPolicyOptions();
```

此外，取消评论将 CBCS ContentKeyPolicyOption 添加到 ContentKeyPolicyOptions 列表中的代码

```csharp
options.Add(
    new ContentKeyPolicyOption()
    {
        Configuration = fairplayConfig,
        Restriction = restriction,
        Name = "ContentKeyPolicyOption_CBCS"
    });
```

## <a name="enable-offline-mode"></a>启用脱机模式

若要启用脱机模式，请在 [CreateStreamingLocatorAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L561) 中创建 StreamingLocator 时创建自定义 StreamingPolicy 并使用其名称。
 
```csharp
CommonEncryptionCbcs objStreamingPolicyInput= new CommonEncryptionCbcs()
{
    Drm = new CbcsDrmConfiguration()
    {
        FairPlay = new StreamingPolicyFairPlayConfiguration()
        {
            AllowPersistentLicense = true // This enables offline mode
        }
    },
    EnabledProtocols = new EnabledProtocols()
    {
        Hls = true,
        Dash = true // Even though DASH under CBCS is not supported for either CSF or CMAF, HLS-CMAF-CBCS uses DASH-CBCS fragments in its HLS playlist
    },

    ContentKeys = new StreamingPolicyContentKeys()
    {
        // Default key must be specified if keyToTrackMappings is present
        DefaultKey = new DefaultKey()
        {
            Label = "CBCS_DefaultKeyLabel"
        }
    }
}

```

现在，媒体服务帐户已配置为传送脱机 FairPlay 许可证。

## <a name="sample-ios-player"></a>示例 iOS 播放器

FPS 脱机模式支持仅适用于 iOS 10 及更高版本。 FPS Server SDK（3.0 或更高版本）包含 FPS 脱机模式文档和示例。 具体而言，FPS Server SDK（3.0 或更高版本）包含以下与脱机模式相关的两项：

* 文档：“使用 FairPlay Streaming 和 HTTP Live Streaming 的脱机播放”。 2016 年 9 月 14 日由 Apple 提供。 在 FPS Server SDK 版本 4.0 中，此文档已合并到主要 FPS 文档。
* 示例代码：\FairPlay Streaming Server SDK version 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\ 中 FPS 脱机模式的 HLSCatalog 示例（Apple 的 FPS Server SDK 的一部分）。 在 HLSCatalog 示例应用中，以下代码文件用于实现脱机模式功能：

    - AssetPersistenceManager.swift 代码文件：AssetPersistenceManager 是此示例中的主类，演示如何：

        - 管理下载 HLS 流，例如用于开始和取消下载的 API 以及用于删除用户设备中现有资产的 API。
        - 监视下载进度。
    - AssetListTableViewController.swift 和 AssetListTableViewCell.swift 代码文件：AssetListTableViewController 是此示例的主接口。 它提供示例可以用来播放、下载、删除或取消下载的资产列表。 

以下步骤说明如何设置正在运行的 iOS 播放器。 假设从 FPS Server SDK 版本 4.0.1 的 HLSCatalog 示例开始。需要对代码进行以下更改：

在 HLSCatalog\Shared\Managers\ContentKeyDelegate.swift 中，使用以下代码实现方法 `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)`。 使“drmUr”成为分配到 HLS URL 的变量。

```swift
    var ckcData: Data? = nil
    
    let semaphore = DispatchSemaphore(value: 0)
    let postString = "spc=\(spcData.base64EncodedString())&assetId=\(assetIDString)"
    
    if let postData = postString.data(using: .ascii, allowLossyConversion: true), let drmServerUrl = URL(string: self.drmUrl) {
        var request = URLRequest(url: drmServerUrl)
        request.httpMethod = "POST"
        request.setValue(String(postData.count), forHTTPHeaderField: "Content-Length")
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        request.httpBody = postData
        
        URLSession.shared.dataTask(with: request) { (data, _, error) in
            if let data = data, var responseString = String(data: data, encoding: .utf8) {
                responseString = responseString.replacingOccurrences(of: "<ckc>", with: "").replacingOccurrences(of: "</ckc>", with: "")
                ckcData = Data(base64Encoded: responseString)
            } else {
                print("Error encountered while fetching FairPlay license for URL: \(self.drmUrl), \(error?.localizedDescription ?? "Unknown error")")
            }
            
            semaphore.signal()
            }.resume()
    } else {
        fatalError("Invalid post data")
    }
    
    semaphore.wait()
    return ckcData
```

在 HLSCatalog\Shared\Managers\ContentKeyDelegate.swift 中，实现方法 `requestApplicationCertificate()`。 此实现取决于是将证书（仅公钥）嵌入设备还是将证书托管在 Web 上。 下面是使用测试示例中使用的托管应用程序证书的实现。 使 certUrl 成为一个包含应用程序证书 URL 的变量。

```swift
func requestApplicationCertificate() throws -> Data {

        var applicationCertificate: Data? = nil
        do {
            applicationCertificate = try Data(contentsOf: URL(string: certUrl)!)
        } catch {
            print("Error loading FairPlay application certificate: \(error)")
        }
        
        return applicationCertificate
    }
```

对于最终集成测试，“集成测试”部分将提供视频 URL 和应用程序证书 URL 两者。

在 HLSCatalog\Shared\Resources\Streams.plist 中添加测试视频 URL。 对于内容密钥 ID，只需使用带有 SKD 协议的 FairPlay 许可证获取 URL 作为唯一值。

![脱机 FairPlay iOS 应用流](media/offline-fairplay-for-ios/offline-fairplay-ios-app-streams.png)

如果已设置，可以使用自己的测试视频 URL、FairPlay 许可证获取 URL 和应用程序证书 URL。 或者，可以继续查看包含测试示例的下一部分。

## <a name="integrated-test"></a>集成测试

媒体服务中的三个测试示例包含以下三种方案：

* FPS 受到保护，包括视频、音频和备用音频曲目
* FPS 受到保护，包括视频、音频，但不包括备用音频曲目
* FPS 受到保护，仅包括视频，不包括音频

可在[此演示站点](https://aka.ms/poc#22)上找到这些示例，相应的应用程序证书托管在 Azure Web 应用中。
使用 FPS Server SDK 的版本 3 或版本 4 示例时，如果在脱机模式期间主播放列表包含备用的音频，则只播放音频。 因此，需要删除备用音频。 换言之，前面所列的第二和第三个示例在联机和脱机模式下都可正常运行。 所列的第一个示例在脱机模式期间只播放音频，联机流式处理可正常运行。

## <a name="faq"></a>常见问题

请参阅[常见问题解答提供故障排除帮助](frequently-asked-questions.md#why-does-only-audio-play-but-not-video-during-offline-mode)。

## <a name="next-steps"></a>后续步骤

了解如何[使用 AES-128 提供保护](protect-with-aes128.md)
