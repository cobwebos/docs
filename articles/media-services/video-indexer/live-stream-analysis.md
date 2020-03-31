---
title: 使用视频索引器进行实时流分析
titleSuffix: Azure Media Services
description: 本文演示如何使用视频索引器执行实时流分析。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/13/2019
ms.author: juliako
ms.openlocfilehash: 89d0254fc758834c437f347e6ecb7bcafc1fe467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185999"
---
# <a name="live-stream-analysis-with-video-indexer"></a>使用视频索引器进行实时流分析

Azure 媒体服务视频索引器是一种 Azure 服务，旨在从视频和音频文件脱机获取深入见解。 这是用于分析已提前创建的给定媒体文件。 但是，对于某些用例，尽快从实时源获取媒体见解以及时解锁操作和其他用例非常重要。 例如，内容制作者可以使用实时流上的此类丰富元数据来自动执行电视制作。

本文中描述的解决方案允许客户在实时源上以近乎实时的分辨率使用视频索引器。 使用此解决方案，索引的延迟可能低至 4 分钟，具体取决于要编制索引的数据块、输入分辨率、内容类型以及用于此过程的计算电源。

![实时流上的视频索引器元数据](./media/live-stream-analysis/live-stream-analysis01.png)

*图 1 = 在实时流上显示视频索引器元数据的示例播放器*

手头的[流分析解决方案](https://aka.ms/livestreamanalysis)使用 Azure 函数和两个逻辑应用使用视频索引器在 Azure 媒体服务的实时通道中处理实时程序，并使用 Azure 媒体播放器显示近乎实时结果流的结果。

在高级别上，它由两个主要步骤组成。 第一步每 60 秒运行一次，并采用播放的最后 60 秒的子剪辑，从中创建资产并通过视频索引器进行索引。 然后，在索引完成后调用第二步。 捕获的见解将处理，发送到 Azure Cosmos DB，并删除已索引的子剪辑。

示例播放器使用专用 Azure 函数播放实时流并从 Azure Cosmos DB 获取见解。 它显示元数据和缩略图与实时视频同步。

![两个逻辑应用程序在云中每分钟处理实时流](./media/live-stream-analysis/live-stream-analysis02.png)

*图 2 = 在云中每分钟处理实时流的两个逻辑应用。*

## <a name="step-by-step-guide"></a>分步指南 

完整的代码和部署结果的分步指南可以在[GitHub 项目中找到，用于使用视频索引器进行实时媒体分析](https://aka.ms/livestreamanalysis)。 

## <a name="next-steps"></a>后续步骤

[视频索引器概述](video-indexer-overview.md)
