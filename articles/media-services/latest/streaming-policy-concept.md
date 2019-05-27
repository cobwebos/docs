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
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 510899e44e4ea4a90e21473ee6af546744c2be2a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120197"
---
# <a name="streaming-policies"></a>流式处理策略

在 Azure 媒体服务 v3 中，使用[流式处理策略](https://docs.microsoft.com/rest/api/media/streamingpolicies)可为[流式处理定位符](streaming-locators-concept.md)定义流式处理协议和加密选项。 媒体服务 v3 提供一些预定义流式处理策略，以便可以将其直接用于试用版或生产环境。 

当前可用的预定义流式处理策略：<br/>“Predefined_DownloadOnly”、“Predefined_ClearStreamingOnly”、“Predefined_DownloadAndClearStreaming”、“Predefined_ClearKey”、“Predefined_MultiDrmCencStreaming”和“Predefined_MultiDrmStreaming”。

如果你有特殊需求 （例如，如果你想要指定不同的协议，需要使用自定义密钥传送服务，或需要使用清除音频轨迹），可以创建自定义流式处理策略。 

 
> [!IMPORTANT]
> * 属于日期时间类型的**流式处理策略**的属性始终采用 UTC 格式。
> * 应为媒体服务帐户设计一组有限的策略，并在需要相同的选项时重新将这些策略用于流式处理定位符。 有关详细信息，请参阅[配额和限制](limits-quotas-constraints.md)。

## <a name="decision-tree"></a>决策树

以下决策树将帮助您选择你的方案的预定义的流式处理策略。

单击图像可查看其完整大小。  <br/>
<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/small.png"></a> 

## <a name="examples"></a>示例

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

### <a name="encrypted"></a>已加密 

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
