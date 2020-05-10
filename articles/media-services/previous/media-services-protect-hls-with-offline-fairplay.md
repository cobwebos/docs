---
title: 使用脱机 Apple FairPlay 保护 HLS 内容 - Azure | Microsoft Docs
description: 本主题概括介绍并演示了如何使用 Azure 媒体服务以脱机模式通过 Apple FairPlay 动态加密 HTTP Live Streaming (HLS) 内容。
services: media-services
keywords: HLS, DRM, FairPlay Streaming (FPS), 脱机, iOS 10
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2020
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: 618803e8e94f96a63e0c39c27b40a933acac7cb4
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995530"
---
# <a name="offline-fairplay-streaming-for-ios"></a>适用于 iOS 的脱机 FairPlay Streaming 

> [!div class="op_single_selector" title1="选择所使用的媒体服务版本："]
> * [第 3 版](../latest/offline-fairplay-for-ios.md)
> * [第 2 版](media-services-protect-hls-with-offline-fairplay.md)

> [!NOTE]
> 不会向媒体服务 v2 添加任何新特性或新功能。 <br/>查看最新版本的[媒体服务 v3](https://docs.microsoft.com/azure/media-services/latest/)。 另请参阅[从 v2 到 v3 的迁移指南](../latest/migrate-from-v2-to-v3.md)

Azure 媒体服务提供一套设计良好的[内容保护服务](https://azure.microsoft.com/services/media-services/content-protection/)，包括：

- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- AES-128 加密

数字版权管理 (DRM)/高级加密标准 (AES) 基于各种流式处理协议的请求，动态执行内容的 DRM/AES 加密。 媒体服务还提供 DRM 许可证/AES 解密密钥传送服务。

除通过各种流式处理协议对联机流式处理的内容提供保护外，通常还要求提供受保护内容的脱机模式功能。 以下情况需要脱机模式支持：

* 在 Internet 连接不可用（如旅行期间）时播放。
* 某些内容提供程序可能不允许在某个国家/地区的边界之外进行 DRM 许可证传送。 如果用户想在该国家/地区外旅行期间查看内容，需要脱机下载。
* 在某些国家/地区，Internet 可用性和/或宽带仍然受到限制。 为获得满意的观看体验，用户可能选择首先下载以便能够观看高分辨率的内容。 在此情况下，通常问题不在于网络可用性，而在于受限的网络宽带。 此时，Over-the-Top (OTT)/联机视频平台 (OVP) 提供商会请求脱机模式支持。

本文介绍 FairPlay Streaming (FPS) 脱机模式支持，适用于运行 iOS 10 或更高版本的设备。 此功能不支持其他 Apple 平台，例如 watchOS、tvOS 或 macOS 上的 Safari。

## <a name="preliminary-steps"></a>预备步骤
在 iOS 10+ 设备上为 FairPlay 实现 脱机 DRM 之前：

* 熟悉 FairPlay 的联机内容保护。 有关详细信息，请参阅以下文章和示例：

    - [Azure 媒体服务的 Apple FairPlay Streaming 正式发布](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
    - [使用 Apple FairPlay 或 Microsoft PlayReady 保护 HLS 内容](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
    - [联机 FPS 流式处理的示例](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)

* 从 Apple Developer Network 获取 FPS SDK。 FPS SDK 包含两个组件：

    - FPS Server SDK，该组件包含密钥安全模块 (KSM)、客户端示例、规格和一组测试矢量。
    - FPS 开发包，该组件包含 D 函数、规格和 FPS 证书生成说明、客户特定的私钥以及应用程序密钥。 Apple 仅对许可的内容提供商发布 FPS 开发包。

## <a name="configuration-in-media-services"></a>媒体服务中的配置
对于通过[媒体服务 .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices) 的 FPS 脱机模式配置，需要使用媒体服务 .NET SDK 4.0.0.4 或更高版本提供必要的 API 来配置 FPS 脱机模式。
此外，还需要提供有效的代码来配置联机模式 FPS 内容保护。 获取用于配置联机模式 FPS 内容保护的代码后，只需进行以下两项更改。

## <a name="code-change-in-the-fairplay-configuration"></a>FairPlay 配置中的代码更改
第一项更改是定义名为 objDRMSettings.EnableOfflineMode 的“启用脱机模式”布尔，使其在启用脱机 DRM 方案时，布尔值为 true。 根据该指示器，对 FairPlay 配置做出以下更改：

```csharp
if (objDRMSettings.EnableOfflineMode)
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv, 
            RentalAndLeaseKeyType.PersistentUnlimited,
            0x9999);
    }
    else
    {
        FairPlayConfiguration = Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
            objX509Certificate2,
            pfxPassword,
            pfxPasswordId,
            askId,
            iv);
    }
```

## <a name="code-change-in-the-asset-delivery-policy-configuration"></a>资产传送策略配置中的代码更改
第二项更改是将第三个密钥添加到 Dictionary<AssetDeliveryPolicyConfigurationKey, string>。
按如下所示添加 AssetDeliveryPolicyConfigurationKey：
 
```csharp
// FPS offline mode
    if (drmSettings.EnableOfflineMode)
    {
        objDictionary_AssetDeliveryPolicyConfigurationKey.Add(AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense, "true");
        Console.WriteLine("FPS OFFLINE MODE: AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense added into asset delivery policy configuration.");
    }

    // for IAssetDelivery for FPS
    IAssetDeliveryPolicy objIAssetDeliveryPolicy = objCloudMediaContext.AssetDeliveryPolicies.Create(
            drmSettings.AssetDeliveryPolicyName,
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            objDictionary_AssetDeliveryPolicyConfigurationKey);
```

完成此步骤后，FPS 资产传送策略中的 <Dictionary_AssetDeliveryPolicyConfigurationKey> 字符串将包含以下三项：

* AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl 或 AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl，具体取决于一些因素，如使用的 FPS KSM/密钥以及是否希望跨多个资产重用相同的资产传送策略
* AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
* AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

现在，媒体服务帐户已配置为传送脱机 FairPlay 许可证。

## <a name="sample-ios-player"></a>示例 iOS 播放器
FPS 脱机模式支持仅适用于 iOS 10 及更高版本。 FPS Server SDK（3.0 或更高版本）包含 FPS 脱机模式文档和示例。 具体而言，FPS Server SDK（3.0 或更高版本）包含以下与脱机模式相关的两项：

* 文档：“使用 FairPlay Streaming 和 HTTP Live Streaming 的脱机播放”。 2016 年 9 月 14 日由 Apple 提供。 在 FPS Server SDK 版本 4.0 中，此文档已合并到主要 FPS 文档。
* 示例代码：\FairPlay Streaming Server SDK version 3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\ 中的 FPS 脱机模式的 HLSCatalog 示例。 在 HLSCatalog 示例应用中，以下代码文件用于实现脱机模式功能：

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

![脱机 FairPlay iOS 应用流](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

如果已设置，可以使用自己的测试视频 URL、FairPlay 许可证获取 URL 和应用程序证书 URL。 或者，可以继续查看包含测试示例的下一部分。

## <a name="integrated-test"></a>集成测试
媒体服务中的三个测试示例包含以下三种方案：

* FPS 受到保护，包括视频、音频和备用音频曲目
* FPS 受到保护，包括视频、音频，但不包括备用音频曲目
* FPS 受到保护，仅包括视频，不包括音频

可在[此演示站点](https://aka.ms/poc#22)上找到这些示例，相应的应用程序证书托管在 Azure Web 应用中。
使用 FPS Server SDK 的版本 3 或版本 4 示例时，如果在脱机模式期间主播放列表包含备用的音频，则只播放音频。 因此，需要删除备用音频。 换言之，前面所列的第二和第三个示例在联机和脱机模式下都可正常运行。 所列的第一个示例在脱机模式期间只播放音频，联机流式处理可正常运行。

## <a name="faq"></a>常见问题
以下常见问题解答提供故障排除帮助：

- **为什么在脱机模式期间只播放音频而不播放视频？** 此行为似乎是示例应用专门设计的。 存在备用音频曲目时（这适用于 HLS），在脱机模式期间，iOS 10 和 iOS 11 都默认播放备用音频曲目。为了补偿 FPS 脱机模式的此行为，需要从流删除备用音频曲目。 若要在媒体服务中完成此操作，请添加动态清单筛选器“audio-only=false”。 换言之，HLS URL 将以 .ism/manifest(format=m3u8-aapl,audio-only=false) 结尾。 
- **为什么添加 audio-only=false 之后，在脱机模式期间仍只播放音频而不播放视频？** 根据内容分发网络 (CDN) 缓存键的设计，可能会缓存该内容。 请清除缓存。
- **除 iOS 10 之外，iOS 11 是否也支持 FPS 脱机模式？** 是的。 iOS 10 和 iOS 11 支持 FPS 脱机模式。
- **为什么在 FPS Server SDK 中，无法使用 FairPlay Streaming 和 HTTP Live Streaming 找到文档“脱机播放”？** 从 FPS Server SDK 版本 4 开始，此文档已合并到“FairPlay Streaming 编程指南”。
- **以下用于 FPS 脱机模式的 API 中的最后一个参数是什么？**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

    有关此 API 的文档，请参阅 [FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration 方法](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet)。 参数表示脱机租赁的持续时间，以秒为单位。
- **iOS 设备上的已下载/脱机文件结构是什么？** iOS 设备上的已下载文件结构如下屏幕截图所示。 `_keys` 文件夹存储已下载的 FPS 许可证，每个许可证服务主机一个存储文件。 `.movpkg` 文件夹存储音频和视频内容。 第一个文件夹（文件名以破折号加数字结尾）包含视频内容。 数值是“PeakBandwidth”视频呈现形式。 第二个文件夹（文件名以破折号加 0 结尾）包含音频内容。 第三个文件夹（文件名为“Data”）包含 FPS 内容的主播放列表。 最后，boot.xml 提供 `.movpkg` 文件夹内容的完整说明。 

![脱机 FairPlay iOS 示例应用文件结构](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

示例 boot.xml 文件：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

## <a name="additional-notes"></a>附加说明

* Widevine 是 Google Inc. 提供的一项服务，并受 Google Inc. 服务条款和隐私策略的约束。

## <a name="summary"></a>总结
本文档包含以下步骤，并提供了可用于实现 FPS 脱机模式的信息：

* 通过媒体服务 .NET API 的媒体服务内容保护配置在媒体服务中配置动态 FairPlay 加密和 FairPlay 许可证传送。
* 基于 FPS Server SDK 中的示例的 iOS 播放器设置可以在联机流式处理模式或脱机模式中播放 FPS 内容的 iOS 播放器。
* 用于测试脱机模式和联机流式处理的示例 FPS 视频。
* 有关 FPS 脱机模式的常见问题解答。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]