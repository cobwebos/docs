---
title: 使用 Azure Media Player 进行播放 - Azure | Microsoft Docs
description: 本主题提供了 Azure Media Player 的概述。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/03/2018
ms.author: juliako
ms.openlocfilehash: fd5bf9f7bca9c667191d0d3f83be6e29a96c8dbe
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926517"
---
# <a name="azure-media-player-overview"></a>Azure Media Player 概述

Azure Media Player 是一种 Web 视频播放器，用于在各种浏览器和设备中播放 Microsoft Azure 媒体服务中的媒体内容。 Azure 媒体播放器采用行业标准（如 HTML5、媒体源扩展 (MSE) 和加密媒体扩展插件 (EME)）来提供更丰富的自适应流式处理体验。 如果无法在设备或浏览器中提供这些标准，Azure 媒体播放器会采用 Flash 和 Silverlight 作为回退技术。 如果不考虑所使用的播放技术，开发人员将有一个统一的 JavaScript 接口来访问 API。 这使 Azure 媒体服务提供的内容无需其他措施便可在各种设备和浏览中轻松播放。

Microsoft Azure 媒体服务允许播放使用 HLS、DASH、平滑流格式提供的内容。 Azure 媒体播放器会考虑这些不同的格式并基于平台/浏览器功能自动播放最佳链接。 媒体服务还允许使用 PlayReady 加密或 AES 128 位信封加密对资产进行动态加密。 Azure 媒体播放器在合理配置时允许对 PlayReady 和 AES-128 位加密的内容进行解密。 

[开始免费试用](https://azure.microsoft.com/pricing/free-trial/)

## <a name="use-azure-media-player-demo-page"></a>使用 Azure Media Player 演示页

### <a name="start-using"></a>开始使用

可以使用 [Azure Media Player 演示页](https://aka.ms/amp)播放 Azure 媒体服务示例或你自己的流。  

若要播放新视频，请粘贴一个不同的 URL，然后按“更新”。

若要配置各种播放选项（例如技术、语言或加密），请按“高级选项”。

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>监视视频流的诊断

可以使用 [Azure Media Player 演示页](https://aka.ms/amp)监视视频流的诊断。 

![Azure Media Player 诊断](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>在 HTML 中设置 Azure Media Player

Azure Media Player 易于设置。 只需要片刻时间便可实现媒体服务帐户中的媒体内容的基本播放。 请参阅 [Azure Media Player 文档](https://aka.ms/ampdocs)来了解有关如何设置和配置 Azure Media Player 的详细信息。 

## <a name="next-steps"></a>后续步骤

- [Azure Media Player 文档](https://aka.ms/ampdocs)
- [Azure Media Player 示例](https://aka.ms/ampsamples)
