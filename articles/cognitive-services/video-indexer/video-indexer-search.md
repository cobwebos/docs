---
title: 使用 Azure 视频索引器在视频中查找确切的时刻 | Microsoft Docs
description: 本主题演示如何在视频中查找确切的时刻。
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/31/2018
ms.author: juliako
ms.openlocfilehash: 1cffa067d8028adab4dbcc82c529f77d980ce6be
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397537"
---
# <a name="find-exact-moments-within-videos"></a>在视频中查找确切的时刻

本主题介绍可以在视频中查找确切时刻的搜索选项。

1. 登录到[视频索引器](https://api-portal.videoindexer.ai/)帐户。
2. 在帐户的所有视频中搜索。

    在以下示例中，我们搜索了 Channel9 创建的包含 Scott Hanselman 的所有视频。

    ![搜索](./media/video-indexer-search/video-indexer-search01.png)
    
3. 搜索视频的汇总见解。

    然后即可单击视频中的“播放”，在视频中进行搜索。 再然后，可以选择“选择”选项卡，在视频中进行搜索。例如，我们搜索了使用“标识保护”文本的所有位置。 

    ![搜索](./media/video-indexer-search/video-indexer-search02.png)

    如果单击某个结果，播放器会将你带到视频中的相应时刻。 可以在应用程序中获得播放器/见解视图和同步。 有关详细信息，请参阅[将视频索引器小组件嵌入应用程序](video-indexer-embed-widgets.md)。 

    
4. 搜索视频的明细。

    若要根据所发现的视频创建你自己的明细，请按“编辑”按钮。 此页面会显示视频的所有明细。 可以在明细中搜索，只显示感兴趣的行。 有关详细信息，请参阅[查看和编辑视频索引器见解](video-indexer-view-edit.md)。

    在此示例中，我们搜索了“标识保护”文本。 我们还应用了其他筛选器，如以下屏幕所示。

    ![搜索](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>后续步骤 

找到要处理的视频以后，即可根据下述某个主题中的说明继续处理该视频： 

- [根据现有视频创建新的视频见解](video-indexer-create-new.md)
- [使用视频索引器 REST API 处理内容](video-indexer-use-apis.md)
- [将视觉小组件嵌入应用程序](video-indexer-embed-widgets.md)

## <a name="see-also"></a>另请参阅

[视频索引器概述](video-indexer-overview.md)
