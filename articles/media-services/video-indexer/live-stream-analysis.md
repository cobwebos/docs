---
title: 使用视频索引器进行实时流分析
titleSuffix: Azure Media Services
description: 本文介绍如何使用视频索引器执行实时流分析。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 4766d48a748fedd82532f2ac49223ab0460b9ef7
ms.sourcegitcommit: fa41a56357de487b10e54711fbbdbd4a373fa67c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2019
ms.locfileid: "74153552"
---
# <a name="live-stream-analysis-with-video-indexer"></a>视频索引器的实时流分析

Azure 媒体服务视频索引器是一种 Azure 服务，旨在从视频和音频文件脱机提取深入了解。 这是为了分析预先创建的给定媒体文件。 但是，对于某些用例，很重要的一点是，尽可能快地从实时源中获取媒体见解，以解锁按时间使用的操作和其他用例。 例如，内容生成者可以使用实时流上的此类元数据来自动进行电视生产。

本文中所述的解决方案允许客户在实时源的近乎实时的解决方案中使用视频索引器。 根据要编制索引的数据块、输入分辨率、内容类型和用于此过程的计算，索引的延迟时间最短为四分钟。

![实时流上的视频索引器元数据](./media/live-stream-analysis/live-stream-analysis01.png)

*图 1-在实时流上显示视频索引器元数据的示例播放机*

目前的[流分析解决方案](https://github.com/Azure-Samples/media-services-dotnet-functions-integration/blob/master/media-functions-for-logic-app/LiveStreamAnalysis.md)使用 Azure Functions 和两个逻辑应用从 Azure 媒体服务中的实时通道使用视频索引器处理实时程序，并显示包含近乎实时生成的流的 Azure Media Player 的结果。

在高级别中，它由两个主要步骤组成。 第一步每60秒运行一次，并在最近60秒播放子剪辑，通过视频索引器创建资产并对其进行索引。 然后，在索引完成后调用第二个步骤。 捕获的 insights 将处理并发送到 Azure Cosmos DB，并删除子剪辑索引。

示例播放机播放实时流，并使用专用的 Azure 函数从 Azure Cosmos DB 获取见解。 它显示与直播视频同步的元数据和缩略图。

![两个逻辑应用在云中每分钟处理实时流](./media/live-stream-analysis/live-stream-analysis02.png)

*图 2-两个逻辑应用在云中每分钟处理实时流。*

## <a name="step-by-step-guide"></a>分步指南 

[有关使用视频索引器进行实时媒体分析的 GitHub 项目](https://aka.ms/livestreamanalysis)中提供了完整的代码和部署结果的循序渐进指南。 

## <a name="next-steps"></a>后续步骤

[视频索引器概述](video-indexer-overview.md)
