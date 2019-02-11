---
title: 视频索引器和 Azure 媒体服务 v3 预设的比较 | Microsoft Docs
description: 本主题比较视频索引器和 Azure 媒体服务 v3 预设。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: juliako
ms.openlocfilehash: 968614d183fe6857336ea92791ba4f1d279a5016
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198093"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>比较 Azure 媒体服务 v3 预设和视频索引器 

本文比较视频索引器 API 和媒体服务 v3 API 的功能。 

目前，[视频索引器 v2 API](https://api-portal.videoindexer.ai/) 和[媒体服务 v3 API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json) 提供的功能之间存在重叠。 下表提供当前用于了解二者异同的准则。 

## <a name="compare"></a>比较

|Feature|视频索引器 API |媒体服务 v3 API 中的<br/>视频分析器和音频分析器预设|
|---|---|---|
|数据见解|[增强版](video-indexer-output-json-v2.md) |[基础知识](../latest/intelligence-concept.md)|
|体验|查看支持的功能的完整列表： <br/> [概述](video-indexer-overview.md)|仅返回视频见解|
|计费|[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|合规性|[Azure 合规性](https://aka.ms/AzureCompliance)|媒体服务符合许多认证。 请查看 [Azure 合规性产品.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf)，并搜索“媒体服务”以查看是否符合相关证书的要求。|
|免费试用版|美国东部|不可用|
|可用性 |美国西部、东亚、北欧|请参阅 [Azure 状态](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。|

## <a name="next-steps"></a>后续步骤

[视频索引器概述](video-indexer-overview.md)

[媒体服务 v3 概述](../latest/media-services-overview.md)
