---
title: 使用 Azure 视频索引器根据现有视频创建视频见解 | Microsoft Docs
description: 本主题介绍如何根据一些其他的视频创建和发布视频见解。
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: 161a47f72a0f8038a515c09f25ec2a8e8a520547
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390834"
---
# <a name="create-highlights-from-existing-videos"></a>根据现有视频创建突出显示内容

本主题介绍如何根据一些其他的视频创建和发布视频见解。

1. 登录到[视频索引器](https://api-portal.videoindexer.ai/)帐户。
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
