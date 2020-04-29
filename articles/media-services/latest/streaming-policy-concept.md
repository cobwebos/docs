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
ms.date: 05/28/2019
ms.author: juliako
ms.openlocfilehash: 9c80056fd62173ff1e5a6ed3979adba71b7706cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80582758"
---
# <a name="streaming-policies"></a>流式处理策略

在 Azure 媒体服务 v3 中，使用[流式处理策略](https://docs.microsoft.com/rest/api/media/streamingpolicies)可为[流式处理定位符](streaming-locators-concept.md)定义流式处理协议和加密选项。 媒体服务 v3 提供了一些预定义的流式处理策略，以便可以将它们直接用于试用版或生产版。 

目前可用的预定义流式处理策略：<br/>
* 'Predefined_DownloadOnly'
* 'Predefined_ClearStreamingOnly'
* 'Predefined_DownloadAndClearStreaming'
* 'Predefined_ClearKey'
* 'Predefined_MultiDrmCencStreaming' 
* 'Predefined_MultiDrmStreaming'

以下“决策树”可帮助你为方案选择预定义的流式处理策略。

> [!IMPORTANT]
> * 属于日期时间类型的**流式处理策略**的属性始终采用 UTC 格式。
> * 应为媒体服务帐户设计一组有限的策略，并在需要相同的选项时重新将这些策略用于流式处理定位符。 有关详细信息，请参阅[配额和限制](limits-quotas-constraints.md)。

## <a name="decision-tree"></a>决策树

单击图像可查看其完整大小。  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

如果要加密内容，则需要创建[内容密钥策略](content-key-policy-concept.md)，明文流式处理或下载不需要**内容密钥策略**。 

如果有特殊要求（例如，如果想要指定不同的协议，需要使用自定义密钥传送服务，或者需要使用明文音频轨道），则可以[创建](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)自定义流式处理策略。 

## <a name="get-a-streaming-policy-definition"></a>获取流式处理策略定义  

若要查看流式处理策略的定义，请使用 [Get](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) 并指定策略名称。 例如：

### <a name="rest"></a>REST

请求：

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

响应：

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>筛选、排序、分页

请参阅[媒体服务实体的筛选、排序、分页](entities-overview.md)。

## <a name="next-steps"></a>后续步骤

* [流式传输文件](stream-files-dotnet-quickstart.md)
* [使用 AES-128 动态加密和密钥传递服务](protect-with-aes128.md)
* [使用 DRM 动态加密和许可证传送服务](protect-with-drm.md)
