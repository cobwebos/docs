---
title: Azure 媒体服务中的流式处理定位符 | Microsoft Docs
description: 本文介绍何为流式处理定位符以及 Azure 媒体服务如何使用这些定位符。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/26/2019
ms.author: juliako
ms.openlocfilehash: 6d13ca5b3657f1deac9e6b4218decf8fe57eb1d9
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113753"
---
# <a name="streaming-locators"></a>流式处理定位符

若要使输出资产中的视频可供客户端进行播放，必须创建[流式处理定位符](https://docs.microsoft.com/rest/api/media/streaminglocators)，然后生成流式处理 URL。 若要生成 URL，需要连接流式处理终结点主机名和流定位器路径。 对于 .NET 示例，请参阅[获取流式处理定位符](stream-files-tutorial-with-api.md#get-a-streaming-locator)。

创建**流定位符**的过程称为发布。 默认情况下，除非配置可选的开始和结束时间，否则调用 API 后，**流定位符**立即生效，并持续到被删除为止。 

创建**流定位器**时，必须指定**资产**名称和**流式处理策略**名称。 相关详细信息，请参阅以下主题：

* [资产](assets-concept.md)
* [流式处理策略](streaming-policy-concept.md)
* [内容密钥策略](content-key-policy-concept.md)

还可以在流定位器上指定开始和结束时间，这将仅允许用户在这两个时间之间播放内容（例如，在 2019 年 5 月 1 日至 2019 年 5 月 5 日之间）。  

## <a name="considerations"></a>注意事项

* **流定位器**不可更新。 
* 属于日期/时间类型的流式处理定位符的属性始终采用 UTC 格式。
* 应为媒体服务帐户设计一组有限的策略，并在需要相同的选项时重新将这些策略用于流式处理定位符。 有关详细信息，请参阅[配额和限制](limits-quotas-constraints.md)。

## <a name="create-streaming-locators"></a>创建流定位器  

### <a name="not-encrypted"></a>未加密

如果想要明文（非加密）流式传输文件，请将预定义的明文流式处理策略设置为“Predefined_ClearStreamingOnly”（在 .NET 中，可以使用 PredefinedStreamingPolicy.ClearStreamingOnly 枚举）。

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>加密 

如果需要使用 CENC 加密来加密内容，请将策略设置为“Predefined_MultiDrmCencStreaming”。 Widevine 加密将应用于短划线流，PlayReady 为平滑。 密钥会根据配置的 DRM 许可证传送到播放客户端。

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

如果还想使用 CBCS (FairPlay) 加密 HLS 流，请使用“Predefined_MultiDrmStreaming”。

## <a name="associate-filters-with-streaming-locators"></a>将筛选器与流定位器关联

请参阅[筛选器：与流定位器关联](filters-concept.md#associating-filters-with-streaming-locator)。

## <a name="filter-order-page-streaming-locator-entities"></a>对流定位器实体进行筛选、排序、分页

请参阅[媒体服务实体的筛选、排序、分页](entities-overview.md)。

## <a name="list-streaming-locators-by-asset-name"></a>按资产名称列出流定位器

若要基于关联的资产名称获取流定位器，请使用以下操作：

|语言|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams asset list-streaming-locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="also-see"></a>另请参阅

* [资产](assets-concept.md)
* [流式处理策略](streaming-policy-concept.md)
* [内容密钥策略](content-key-policy-concept.md)

## <a name="next-steps"></a>后续步骤

[教程：使用 .NET 上传、编码和流式传输视频](stream-files-tutorial-with-api.md)
