---
title: Azure 媒体服务 v3 发行说明 | Microsoft Docs
description: 为了让大家随时了解最新的开发成果，本文提供了 Azure 媒体服务 v3 的最新更新。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/25/2018
ms.author: juliako
ms.openlocfilehash: ed2550c1df4645933fb968c54ee536995c810136
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219312"
---
# <a name="azure-media-services-v3-preview-release-notes"></a>Azure 媒体服务 v3（预览）发行说明 

为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

* 最新版本
* 已知问题
* Bug 修复
* 已弃用的功能
* 更改计划

## <a name="may-07-2018"></a>2018 年 5 月 7 日

### <a name="net-sdk"></a>.NET SDK

.Net SDK 具有以下功能：

1. 转换和作业，用于对媒体内容来进行编码或分析。 有关示例，请参阅[流式传输文件](stream-files-tutorial-with-api.md)和[分析](analyze-videos-tutorial-with-api.md)。
2. StreamingLocators，用于发布内容并将其流式传输到最终用户设备
3. StreamingPolicies 和 ContentKeyPolicies，用于在传送内容时配置密钥传递和内容保护 (DRM)。
4. LiveEvents 和 LiveOutputs，用于配置实时传送视频流内容的引入和归档。
5. 资产，用于在 Azure 存储中存储和发布媒体内容。 
6. StreamingEndpoints，用于配置和缩放实时和点播媒体内容的动态打包、加密和流式处理。

### <a name="known-issues"></a>已知问题

* 提交作业时，可以指定使用 HTTPS URL、SAS URL 或位于 Azure Blob 存储中的文件路径引入源视频。 目前，AMS v3 不支持基于 HTTPS URL 的块传输编码。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [概述](media-services-overview.md)
