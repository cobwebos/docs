---
title: 在视频索引器中自定义品牌模型 - Azure
titlesuffix: Azure Media Services
description: 本文概述了什么是视频索引器中的品牌模型，以及如何自定义它。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 863dbd9a6044ee33ae39ac9693a7d4f74382b9c7
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799675"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>在视频索引器中自定义品牌模型

在对视频和音频内容进行索引和重新索引的过程中，可以使用视频索引器从语音和视觉文本中检测品牌。 品牌检测功能可以确定内容中是否提到必应品牌数据库建议的产品、服务和公司。 例如，如果在某个视频或音频内容中提到了 Microsoft，或者 Microsoft 出现在视频的视觉文本中，则视频索引器会将其作为内容中的品牌检测到。 将使用上下文将品牌与其他术语进行区分。

品牌检测在许多业务场景中非常有用，例如，内容存档和发现、上下文广告、社交媒体分析、零售竞争分析，等等。 通过视频索引器品牌检测，可以使用必应的品牌数据库以及自定义项通过为每个视频索引器帐户构建一个自定义品牌模型，为在语音和视觉文本中提到的品牌编制索引。 使用“自定义品牌模型”功能，可以选择是否让视频索引器检测必应品牌数据库中的品牌；可以排除某些品牌，不让其检测到（实质上是创建一个品牌的方块列表）；还可以添加一些品牌，这些品牌应该在你的模型中，但可能不在必应的品牌数据库中（实质上是创建一个品牌的允许列表）。 所创建的自定义品牌模型仅在创建该模型的帐户中可用。

## <a name="out-of-the-box-detection-example"></a>现成的检测示例

在 [Microsoft Build 2017 Day 2](https://www.videoindexer.ai/media/ed6ede78ad/) 演示文稿中，品牌“Microsoft Windows”出现了多次。 有时是在脚本中，有时是作为视觉文本，但从未以表示字面含义的形式出现。 视频索引器根据上下文以高精度检测某个术语是否确实为品牌，它涵盖了 9 万多个现成的品牌并且持续在更新。 在 02:25，视频索引器从语音中检测到品牌，然后在 02:40 再次从视觉文本中检测到品牌，该品牌是 Windows 徽标的一部分。

![品牌概述](./media/content-model-customization/brands-overview.png)

高级机器学习算法知道如何根据上下文消除歧义，在建筑上下文中谈论 windows（窗户）时，不会将单词“Windows”检测为品牌，对于 Box、Apple 和 Fox 等也是如此。 品牌检测可以针对我们支持的所有语言进行工作。 若要了解[完整的 Microsoft Build 2017 Day 2 主题视频和索引](https://www.videoindexer.ai/media/ed6ede78ad/)，请单击此处。

若要使用自己的品牌，请查看“后续步骤”。

## <a name="next-steps"></a>后续步骤

[使用 API 自定义品牌模型](customize-brands-model-with-api.md)

[使用网站自定义品牌模型](customize-brands-model-with-website.md)
