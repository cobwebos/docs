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
ms.date: 05/22/2019
ms.author: juliako
ms.openlocfilehash: 9a14399117971807c1d18f8eb5fab7d6e6cef2d5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120356"
---
# <a name="streaming-locators"></a>流式处理定位符

若要使输出资产中的视频可供客户端进行播放，必须创建[流式处理定位符](https://docs.microsoft.com/rest/api/media/streaminglocators)，然后生成流式处理 URL。 对于 .NET 示例，请参阅[获取流式处理定位符](stream-files-tutorial-with-api.md#get-a-streaming-locator)。

创建**流定位符**的过程称为发布。 默认情况下，除非配置可选的开始和结束时间，否则调用 API 后，**流定位符**立即生效，并持续到被删除为止。 

创建时**流式处理定位符**，则必须指定**资产**名称和一个**流式处理策略**名称。 有关详细信息，请参阅下列主题：

* [资产](assets-concept.md)
* [流式处理策略](streaming-policy-concept.md)
* [内容密钥策略](content-key-policy-concept.md)

> [!IMPORTANT]
> * 属于日期/时间类型的流式处理定位符的属性始终采用 UTC 格式。
> * 应为媒体服务帐户设计一组有限的策略，并在需要相同的选项时重新将这些策略用于流式处理定位符。 有关详细信息，请参阅[配额和限制](limits-quotas-constraints.md)。

## <a name="associate-filters-with-streaming-locators"></a>将筛选器与流式处理定位符相关联

请参阅[筛选器： 流式处理定位符相关联](filters-concept.md#associate-filters-with-streaming-locator)。

## <a name="filter-order-page-streaming-locator-entities"></a>筛选器、 顺序、 页流式处理定位符实体

请参阅[媒体服务实体的筛选、排序、分页](entities-overview.md)。

## <a name="next-steps"></a>后续步骤

[教程：使用 .NET 上传、编码和流式传输视频](stream-files-tutorial-with-api.md)
