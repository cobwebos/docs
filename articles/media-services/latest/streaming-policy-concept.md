---
title: Azure 媒体服务中的流式处理策略 | Microsoft Docs
description: 本文介绍何为流式处理策略以及 Azure 媒体服务如何使用这些终结点。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: 10600d8f3ff4e08b8d90f28ec15d3cb0c56bcae0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230890"
---
# <a name="streaming-policies"></a>流式处理策略

在 Azure 媒体服务 v3 中，使用[流式处理策略](https://docs.microsoft.com/rest/api/media/streamingpolicies)可为[流式处理定位符](streaming-locators-concept.md)定义流式处理协议和加密选项。 可使用其中一个预定义的流式处理策略，也可创建自定义策略。 目前可用的预定义流式处理策略包括：“Predefined_DownloadOnly”、“Predefined_ClearStreamingOnly”、“Predefined_DownloadAndClearStreaming”、“Predefined_ClearKey”、“Predefined_MultiDrmCencStreaming”和“Predefined_MultiDrmStreaming”。

> [!IMPORTANT]
> * 属于日期时间类型的**流式处理策略**的属性始终采用 UTC 格式。
> * 应为媒体服务帐户设计一组有限的策略，并在需要相同的选项时重新将这些策略用于流式处理定位符。 

## <a name="examples"></a>示例

### <a name="not-encrypted"></a>未加密

如果想要明文（非加密）流式传输文件，请将预定义的明文流式处理策略设置为“Predefined_ClearStreamingOnly”（在 .NET 中，可以使用 PredefinedStreamingPolicy.ClearStreamingOnly）。

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

如果需要使用信封和 CENC 加密来加密内容，请将策略设置为“Predefined_MultiDrmCencStreaming”。 此策略指示需要在定位符中生成并设置两个内容密钥（信封和 CENC）。 因此，应用了信封、PlayReady 和 Widevine 加密（根据配置的 DRM 许可证将密钥传送到播放客户端）。

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

如果还想使用 CBCS (FairPlay) 加密数据流，请使用“Predefined_MultiDrmStreaming”。

## <a name="filtering-ordering-paging"></a>筛选、排序、分页

请参阅[媒体服务实体的筛选、排序、分页](entities-overview.md)。

## <a name="next-steps"></a>后续步骤

* [流式传输文件](stream-files-dotnet-quickstart.md)
* [使用 AES-128 动态加密和密钥传递服务](protect-with-aes128.md)
* [使用 DRM 动态加密和许可证传送服务](protect-with-drm.md)
