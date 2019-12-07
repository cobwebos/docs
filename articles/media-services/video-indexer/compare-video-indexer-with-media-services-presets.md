---
title: 视频索引器和 Azure 媒体服务 v3 预设的比较 | Microsoft Docs
description: 本文比较了视频索引器功能和 Azure 媒体服务 v3 预设。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 055e210efbb01268654e7823a0fc1320e9c27646
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892815"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>比较 Azure 媒体服务 v3 预设和视频索引器 

本文比较视频索引器 API 和媒体服务 v3 API 的功能。 

目前，[视频索引器 api](https://api-portal.videoindexer.ai/)提供的功能与[媒体服务 v3 api](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)提供的功能重叠。 下表提供当前用于了解二者异同的准则。 

## <a name="compare"></a>比较

|Feature|视频索引器 API |媒体服务 v3 API 中的<br/>视频分析器和音频分析器预设|
|---|---|---|
|数据见解|[增强版](video-indexer-output-json-v2.md) |[基础知识](../latest/intelligence-concept.md)|
|体验|查看支持的功能的完整列表： <br/> [概述](video-indexer-overview.md)|仅返回视频见解|
|计费|[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|合规|[Iso 27001](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001)、 [iso 27018](https://www.microsoft.com/trustcenter/Compliance/ISO-IEC-27018)、 [SOC 1、2、3](https://www.microsoft.com/TrustCenter/Compliance/SOC)、 [HIPAA](https://www.microsoft.com/trustcenter/compliance/hipaa)、 [FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/fedramp)、 [PCI](https://www.microsoft.com/trustcenter/compliance/pci)和[HITRUST](https://www.microsoft.com/TrustCenter/Compliance/hitrust)认证。 有关最新的更新，请访问[视频索引器的当前认证状态](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)。|媒体服务符合许多认证。 请查看 [Azure 合规性产品.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf)，并搜索“媒体服务”以查看是否符合相关证书的要求。|
|免费试用|美国东部|不可用|
|适用区域|美国东部2、美国中南部、美国西部2、北欧、西欧、东南亚、东亚和澳大利亚东部。  有关最新的更新，请访问[产品（按区域](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)）页。|请参阅 [Azure 状态](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。|

## <a name="next-steps"></a>后续步骤

[视频索引器概述](video-indexer-overview.md)

[媒体服务 v3 概述](../latest/media-services-overview.md)
