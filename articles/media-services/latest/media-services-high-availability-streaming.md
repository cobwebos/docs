---
title: Azure 媒体服务高可用性流式处理
description: 了解如何在区域数据中心中断或发生故障时故障转移到辅助媒体服务帐户。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 1492dd392eabc4331f8e3d4604fb245a89dedff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899225"
---
# <a name="media-services-high-availability-streaming"></a>媒体服务高可用性流

如果区域数据中心中断或基础组件或从属服务出现故障，Azure 媒体服务当前不会提供服务的即时故障转移。 本文指导如何构建视频点播跨区域流。

## <a name="prerequisites"></a>先决条件

回顾[如何构建跨区域编码系统](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>如何构建视频点播跨区域流 

* 视频点播跨区域流涉及复制[资产](assets-concept.md)、[内容密钥策略](content-key-policy-concept.md)（如果使用）、[流式处理策略](streaming-policy-concept.md)和[流式处理器](streaming-locators-concept.md)。 
* 您必须在这两个区域创建策略，并使它们保持最新状态。 
* 创建流式处理器时，需要使用相同的定位器 ID 值、ContentKey ID 值和 ContentKey 值。  
* 如果要对内容进行编码，建议对区域 A 中的内容进行编码并发布，然后将编码的内容复制到区域 B，并使用与区域 A 相同的值发布内容。
* 您可以在源和密钥传递服务的主机名上使用流量管理器（在媒体服务配置中，这看起来像自定义密钥服务器 URL）。

## <a name="next-steps"></a>后续步骤

退房：

* [教程：基于 URL 对远程文件进行编码并流式传输视频](stream-files-dotnet-quickstart.md)
* [代码示例](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
