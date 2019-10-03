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
ms.openlocfilehash: 5897b7df2460257784c40eb974c473573ec4003d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299165"
---
# <a name="streaming-locators"></a>流式处理定位符

若要使输出资产中的视频可供客户端进行播放，必须创建[流式处理定位符](https://docs.microsoft.com/rest/api/media/streaminglocators)，然后生成流式处理 URL。 若要生成的 URL，需要将流式处理终结点主机名和流式处理定位符路径连接。 对于 .NET 示例，请参阅[获取流式处理定位符](stream-files-tutorial-with-api.md#get-a-streaming-locator)。

创建**流定位符**的过程称为发布。 默认情况下，除非配置可选的开始和结束时间，否则调用 API 后，**流定位符**立即生效，并持续到被删除为止。 

创建时**流式处理定位符**，则必须指定**资产**名称和一个**流式处理策略**名称。 有关详细信息，请参阅下列主题：

* [资产](assets-concept.md)
* [流式处理策略](streaming-policy-concept.md)
* [内容密钥策略](content-key-policy-concept.md)

此外可以指定在流式处理定位符，它仅允许用户上的开始和结束时间播放 （例如，5/1/2019 到 5/5/2019年) 之间的这些时间之间的内容。  

## <a name="considerations"></a>注意事项

* **流式处理定位符**不能更新。 
* 属于日期/时间类型的流式处理定位符  的属性始终采用 UTC 格式。
* 应为媒体服务帐户设计一组有限的策略，并在需要相同的选项时重新将这些策略用于流式处理定位符。 有关详细信息，请参阅[配额和限制](limits-quotas-constraints.md)。

## <a name="create-streaming-locators"></a>创建流式处理定位符  

### <a name="not-encrypted"></a>未加密

如果你想要在文件中的-清除 （非加密） 流式传输，预定义的清除流式处理策略设置: Predefined_ClearStreamingOnly 到 （在.NET 中，您可以使用 PredefinedStreamingPolicy.ClearStreamingOnly 枚举）。

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

如果需要加密你的内容使用 CENC 加密，你将策略设置为 Predefined_MultiDrmCencStreaming。 Widevine 加密将为 DASH 流，并 PlayReady 应用到平滑。 该密钥将传送到基于配置的 DRM 许可证播放客户端。

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

如果还想要加密 HLS 流使用 CBC (FairPlay)，使用 Predefined_MultiDrmStreaming。

## <a name="associate-filters-with-streaming-locators"></a>将筛选器与流式处理定位符相关联

请参阅[筛选器： 流式处理定位符相关联](filters-concept.md#associating-filters-with-streaming-locator)。

## <a name="filter-order-page-streaming-locator-entities"></a>筛选器、 顺序、 页流式处理定位符实体

请参阅[媒体服务实体的筛选、排序、分页](entities-overview.md)。

## <a name="list-streaming-locators-by-asset-name"></a>列出资产名称由流式处理定位符

若要获取基于关联的资产名称流式处理定位符，请使用以下操作：

|语言|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams asset list-streaming-locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/assets?view=azure-node-latest#liststreaminglocators-string--string--string--object-)|

## <a name="also-see"></a>另请参阅

* [资产](assets-concept.md)
* [流式处理策略](streaming-policy-concept.md)
* [内容密钥策略](content-key-policy-concept.md)

## <a name="next-steps"></a>后续步骤

[教程：使用 .NET 上传、编码和流式传输视频](stream-files-tutorial-with-api.md)
