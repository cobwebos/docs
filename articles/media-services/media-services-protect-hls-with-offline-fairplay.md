---
title: "使用脱机 Apple FairPlay 保护 HLS 内容 - Azure | Microsoft Docs"
description: "本主题概括介绍并演示了如何使用 Azure 媒体服务以脱机模式通过 Apple FairPlay 动态加密 HTTP Live Streaming (HLS) 内容。"
services: media-services
keywords: "HLS, DRM, FairPlay Streaming (FPS), 脱机, iOS 10"
documentationcenter: 
author: willzhan
manager: steveng
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: b68ceac2056f0a9a7a9c4df7984789858c77a626
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="offline-fairplay-streaming"></a>脱机 FairPlay Streaming
Microsoft Azure 媒体服务提供一套设计良好的[内容保护服务](https://azure.microsoft.com/services/media-services/content-protection/)，包括：
- Microsoft PlayReady
- Google Widevine
- Apple FairPlay
- AES-128 加密

基于各种流式处理协议的请求，动态执行内容的 DRM/AES 加密。 Azure 媒体服务还提供 DRM 许可证/AES 解密密钥传送服务。

除通过各种流式处理协议对联机流式处理的内容提供保护外，通常还要求提供受保护内容的脱机模式功能。 以下情况需要脱机模式支持：
1. 在 Internet 连接不可用（如旅行期间）时播放；
2. 某些内容提供程序可能不允许在某个国家/地区边界之外进行 DRM 许可证传送。 如果用户想在国外旅行期间查看内容，需要脱机下载。
3. 在某些国家/地区，Internet 可用性和/或宽带仍然受到限制。 为获得满意的观看体验，用户可能选择首先下载以便能够观看高分辨率的内容。 在此情况下，通常问题不在于网络可用性，而在于受限的网络宽带。 此时，OTT/OVP 提供程序就会请求提供脱机模式支持。

本文介绍 FairPlay Streaming (FPS) 脱机模式支持，适用于运行 iOS 10 或更高版本的设备。 此功能不支持其他 Apple 平台，例如 watchOS、tvOS 或 macOS 上的 Safari。

## <a name="preliminary-steps"></a>预备步骤
在 iOS 10+ 设备上为 FairPlay 实现 脱机 DRM 之前，首先应：
1. 熟悉 FairPlay 的联机内容保护。 以下文章/示例对此进行了详细介绍：
- [Azure 媒体服务的 Apple FairPlay Streaming 正式发布](https://azure.microsoft.com/blog/apple-FairPlay-streaming-for-azure-media-services-generally-available/)
- [使用 Apple FairPlay 或 Microsoft PlayReady 保护 HLS 内容](https://docs.microsoft.com/azure/media-services/media-services-protect-hls-with-FairPlay)
- [联机 FPS 流式处理的示例](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-FairPlay/)
2. 从 Apple Developer Network 获取 FPS SDK。 FPS SDK 包含两个组件：
- FPS Server SDK，该组件包含 KSM（密钥安全模块）、客户端示例、规格和一组测试矢量；
- FPS 开发包，该组件包含 D 函数、规格和 FPS 证书生成说明、客户特定的私钥以及应用程序密钥 (ASK)。 Apple 仅对许可的内容提供商发布 FPS 开发包。

## <a name="configuration-in-azure-media-services"></a>Azure 媒体服务中的配置
对于通过 [Azure 媒体服务 .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices) 的 FPS 脱机模式配置，需要使用 Azure 媒体服务 .NET SDK v. 4.0.0.4 或更高版本提供必要的 API 来配置 FPS 脱机模式。
如以上假设所述，假设具有用于配置联机模式 FPS 内容保护的工作代码。 具有该代码之后，只需进行以下两个更改。

## <a name="code-change-in-fairplay-configuration"></a>FairPlay 配置中的代码更改
定义名为 objDRMSettings.EnableOfflineMode 的“启用脱机模式”布尔，使其在启用脱机 DRM 方案时，布尔为 true。 根据该指示器，对 FairPlay 配置做出以下更改：

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

## <a name="code-change-in-asset-delivery-policy-configuration"></a>资产传送策略配置中的代码更改
第二个更改是将第三个密钥添加到字典 Dictionary<AssetDeliveryPolicyConfigurationKey, string>。
需要添加的第三个 AssetDeliveryPolicyConfigurationKey 如下所示： 
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

完成此步骤后，FPS 资产传送策略中的 Dictionary<AssetDeliveryPolicyConfigurationKey, string> 将包含以下三项：
1. AssetDeliveryPolicyConfigurationKey.FairPlayBaseLicenseAcquisitionUrl 或 AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl，具体取决于一些因素，如使用的 FPS KSM/密钥以及是否希望跨多个资产重用相同的资产传送策略
2. AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs
3. AssetDeliveryPolicyConfigurationKey.AllowPersistentLicense

现在，媒体服务帐户已配置为传送脱机 FairPlay 许可证。

## <a name="sample-ios-player"></a>示例 iOS 播放器
首先应注意的是，FPS 脱机模式支持仅适用于 iOS 10 及更高版本。 因此，应获取包含 FPS 脱机模式文档和示例的 FPS Server SDK（v3.0 或更高版本）。 具体而言，FPS Server SDK（v3.0 或更高版本）包含以下与脱机模式相关的两项：
1. 文档：使用 FairPlay Streaming 和 HTTP Live Streaming 的脱机播放。 Apple，2016/9/14。 在 FPS Server SDK v 4.0 中，此文档已合并到主 FPS 流式处理文档。
2. 示例代码：\FairPlay Streaming Server SDK v3.1\Development\Client\HLSCatalog_With_FPS\HLSCatalog\ 中的 FPS 脱机模式的 HLSCatalog 示例。 在 HLSCatalog 示例应用中，以下代码文件部分用于实现脱机模式功能：
- AssetPersistenceManager.swift 代码文件：AssetPersistenceManager 是演示的此示例中的主类
    - 如何管理下载 HLS 流，例如用于开始和取消下载的 API 以及用于删除用户设备中现有资产的 API；
    - 如何监视下载进度。
- AssetListTableViewController.swift 和 AssetListTableViewCell.swift 代码文件：AssetListTableViewController 是此示例的主接口。 它提供示例可以播放、下载、删除或取消下载的资产列表。 

以下步骤详细介绍了正在运行的 iOS 播放器的设置。 假设从 FPS Server SDK v 4.0.1 的 HLSCatalog 示例开始。  需要对代码进行以下更改：

在 HLSCatalog\Shared\Managers\ContentKeyDelegate.swift 中，使用以下代码实现方法 `requestContentKeyFromKeySecurityModule(spcData: Data, assetID: String)`：使 drmUr 成为一个分配到 HLS 流式处理 URL 的变量。

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

在 HLSCatalog\Shared\Resources\Streams.plist 中，添加测试视频 URL，对于内容密钥 ID，可只需使用带有 SKD 协议的 FairPlay 许可证获取 URL 作为唯一值。

![脱机 FairPlay iOS 应用流](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-ios-app-streams.png)

对于测试视频 URL、FairPlay 许可证获取 URL 和应用程序证书 URL，如果已经设置，可以使用自己的 URL，如果未设置，可继续查看包含测试示例的下一部分。

## <a name="integrated-test"></a>集成测试
Azure 媒体服务中已设置三个测试示例，其中包含三种方案：
1.  FPS 受到保护，包括视频、音频和备用音频曲目；
2.  FPS 受到保护，包括视频、音频，但不包括备用音频曲目；
3.  FPS 受到保护，仅包括视频，不包括音频。

可在此[演示网站](http://aka.ms/poc#22)中找到这些示例，相应的应用程序证书托管在 Azure Web 应用中。
我们发现，如果在脱机模式期间，主播放列表包含备用的音频，则不管使用 FPS Server SDK 的 v3 还是 v4 示例，都只播放音频。 因此，需要删除备用音频。 换言之，在以上三个示例中，示例 (2) 和 (3) 在联机和脱机模式下都可用。 但是示例 (1) 在脱机模式期间将只播放音频，而在联机流式处理时运作正常。

## <a name="faq"></a>常见问题
一些常见问题的解答：
- **为什么在脱机模式期间只播放音频而不播放视频？** 此行为似乎是示例应用专门设计的。 存在备用音频曲目时（这适用于 HLS），在脱机模式期间，iOS 10 和 iOS 11 都默认播放备用音频曲目。为了补偿 FPS 脱机模式的此行为，需要从流删除备用音频曲目。 若要在 Azure 媒体服务上完成此操作，可以简单添加动态清单筛选器“audio-only=false”。 换言之，HLS URL 将以 .ism/manifest(format=m3u8-aapl,audio-only=false) 结尾。 
- **为什么添加 audio-only=false 之后，在脱机模式期间仍只播放音频而不播放视频？** 根据 CDN 缓存键设计，可能会缓存该内容。 需要清除缓存。
- **除 iOS 10 之外，iOS 11 是否也支持 FPS 脱机模式？** 是，iOS 10 和 iOS 11 都支持 FPS 脱机模式。
- **为什么在 FPS Server SDK 中，无法使用 FairPlay Streaming 和 HTTP Live Streaming 找到文档“脱机播放”？** 自 FPS Server SDK v 4 之后，此文档已合并到“FairPlay Streaming 编程指南”文档中。
- **FPS 脱机模式的以下 API 中最后一个参数代表什么？**
`Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(objX509Certificate2, pfxPassword, pfxPasswordId, askId, iv, RentalAndLeaseKeyType.PersistentUnlimited, 0x9999);`

此 API 的文档可在[此处](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mediaservices.client.FairPlay.FairPlayconfiguration.createserializedFairPlayoptionconfiguration?view=azure-dotnet)找到。 该参数代表脱机租赁的持续时间（以小时为单位）。
- **iOS 设备上的已下载/脱机文件结构是什么？** iOS 设备上的已下载文件结构如下所示（屏幕截图）。 `_keys` 文件夹存储已下载的 FPS 许可证，每个许可证服务主机一个存储文件。 `.movpkg` 文件夹存储音频和视频内容。 第一个文件夹（文件名以破折号加数字结尾）包含视频内容。 数值是“PeakBandwidth”视频呈现形式。 第二个文件夹（文件名以破折号加 0 结尾）包含音频内容。 第三个文件夹（文件名为“Data”）包含 FPS 内容的主播放列表。 Boot.xml 提供 `.movpkg` 文件夹内容的完整说明（请参阅以下示例 boot.xml 文件）。

![脱机 FairPlay iOS 示例应用文件结构](media/media-services-protect-hls-with-offline-FairPlay/media-services-offline-FairPlay-file-structure.png)

示例 boot.xml 文件：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
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

## <a name="summary"></a>摘要
在此文档中，我们提供了用于实现 FPS 脱机模式的详细步骤和信息，包括：
1. 借助 AMS .NET API 的 Azure 媒体服务内容保护配置。 这将在 AMS 中配置动态 FairPlay 加密和 FairPlay 许可证传送。
2. 基于 Apple FPS Server SDK 的示例的 iOS 播放器。 这将设置可以在联机流式处理模式或脱机模式中播放 FPS 内容的 iOS 播放器。
3. 用于测试脱机模式和联机流式处理的示例 FPS 视频。
4. 有关 FPS 脱机模式的常见问题解答。
