---
title: 从现有视频创建视频见解
titlesuffix: Azure Media Services
description: 本主题介绍如何基于现有的视频文件创建和发布视频见解。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: 7ba3fab514729b7b5645254fa9d34dd42b6718d5
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291732"
---
# <a name="tutorial-create-highlights-from-existing-videos"></a>教程：基于现有视频创建突出显示内容

本主题介绍如何根据一些其他的视频创建和发布视频见解。

1. 浏览到[视频索引器](https://www.videoindexer.ai/)网站并登录。
2. 找到需从其创建视频见解的视频。
3. 按“播放”。

    页面会显示视频的汇总见解。 

    ![洞察力](./media/video-indexer-create-new/video-indexer-summarized-insights.png)

3. 按“编辑”按钮。

    此页面会显示视频的所有明细。 明细会分成块。 有了块，就可以更轻松地浏览数据。 例如，可以在发言人换人或中断时间长时将块细分。 可以创建你自己的播放列表，其中只包含所需的行。 若要只显示源视频的特定部分，可以按主题/关键字、情绪、人物、发言人进行筛选。 可以选择只查看视频的脚本或 OCR。    

    ![洞察力](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)

4. 创建播放列表。

    若要在播放列表中添加或删除行，请按 **+**/**-**。

5. 预览播放列表。

    创建完播放列表后，请按“预览”。
6. 发布播放列表。

    预览完播放列表以后，即可将其发布。

    播放列表在发布后会添加到视频见解的列表中。


## <a name="next-steps"></a>后续步骤 

创建新的播放列表以后，即可根据下述某个主题中的说明继续处理它： 

- [使用视频索引器 REST API 处理内容](video-indexer-use-apis.md)
- [将视觉小组件嵌入应用程序](video-indexer-embed-widgets.md)

## <a name="see-also"></a>另请参阅

[视频索引器概述](video-indexer-overview.md) 
