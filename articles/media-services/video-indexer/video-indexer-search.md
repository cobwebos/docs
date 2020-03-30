---
title: 使用视频索引器搜索视频中的确切时刻
titleSuffix: Azure Media Services
description: 了解如何使用视频索引器搜索视频中的确切时刻。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 1c3326456fefbbef9228cf26b5821f306cd21601
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127910"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>使用视频索引器搜索视频中的确切时刻

本主题显示搜索选项，使您能够在视频中搜索确切时刻。

1. 转到[视频索引器](https://www.videoindexer.ai/)网站并登录。
2. 在帐户的所有视频中搜索。

    在下面的示例中，我们搜索所有讨论安全性的视频，并在其中显示 Satya。

    ![在视频索引器中搜索视频](./media/video-indexer-search/video-indexer-search01.png)

3. 搜索视频的汇总见解。

    然后，您可以通过选择 **"播放**"视频来搜索视频。 然后，您可以通过选择 **"搜索"** 选项卡来搜索视频中的确切时刻。

    在下面的示例中，我们在所选视频中搜索"安全"。

    ![使用视频索引器在视频中搜索](./media/video-indexer-search/video-indexer-search02.png)

    如果选择其中一个结果，播放器将为您提供视频中的精确时刻。 您可以在应用中实现播放器/见解视图和同步。 有关详细信息，请参阅[将视频索引器小部件嵌入到你的应用中](video-indexer-embed-widgets.md)。

4. 搜索视频的明细。

    如果要根据找到的视频创建自己的剪辑，请选择 **"编辑**"按钮。 本页将视频及其见解作为筛选器显示。 有关详细信息，请参阅[查看和编辑视频索引器见解](video-indexer-view-edit.md)。

    您可以搜索视频中的确切时刻，以便仅显示您感兴趣的行。 使用侧洞察筛选要查看的部件。 完成后，可以预览剪辑并选择 **"发布"** 以创建库中显示的新剪辑。

    在下面的示例中，我们搜索了"混合现实"文本。 我们还应用了其他筛选器，如以下屏幕所示。

    ![在视频索引器中搜索确切时刻](./media/video-indexer-search/video-indexer-search03.png)

## <a name="next-steps"></a>后续步骤

在要处理的视频中找到确切时刻后，可以继续处理视频。 有关详情，请参阅以下主题：

- [使用视频的深入见解](use-editor-create-project.md)
- [使用视频索引器 REST API 处理内容](video-indexer-use-apis.md)
- [将视觉小组件嵌入应用程序](video-indexer-embed-widgets.md)

## <a name="see-also"></a>请参阅

[视频索引器概述](video-indexer-overview.md)
