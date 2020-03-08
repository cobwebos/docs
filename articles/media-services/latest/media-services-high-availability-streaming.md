---
title: Azure 媒体服务高可用性流式处理
description: 了解在发生区域性的数据中心服务中断或故障时如何故障转移到辅助媒体服务帐户。
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
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899225"
---
# <a name="media-services-high-availability-streaming"></a>媒体服务高可用性流式处理

如果存在区域性数据中心服务中断或基础组件或从属服务故障，Azure 媒体服务当前不提供服务的即时故障转移。 本文提供了有关如何构建点播视频点播跨区域流式处理的指导。

## <a name="prerequisites"></a>必备条件

查看[如何构建跨区域编码系统](media-services-high-availability-encoding.md)

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>如何构建点播视频点播跨区域流式处理 

* 视频点播跨区域的流式处理涉及到复制[资产](assets-concept.md)、[内容密钥策略](content-key-policy-concept.md)（如果已使用）、[流式处理策略](streaming-policy-concept.md)和[流式处理](streaming-locators-concept.md)定位符。 
* 需要在这两个区域中创建策略，并使其保持最新。 
* 创建流式处理定位符时，需要使用相同的定位符 ID 值、ContentKey ID 值和 ContentKey 值。  
* 如果要对内容进行编码，建议对区域 A 中的内容进行编码并将其发布，然后将编码内容复制到区域 B 并使用与区域 A 相同的值发布该内容。
* 你可以使用流量管理器来存放源和密钥传送服务（在媒体服务配置中，这将类似于自定义密钥服务器 URL）。

## <a name="next-steps"></a>后续步骤

退房：

* [教程：根据 URL 编码远程文件并流式传输视频](stream-files-dotnet-quickstart.md)
* [代码示例](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
