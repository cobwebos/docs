---
title: 使用视频索引器编辑器创建项目
titleSuffix: Azure Media Services
description: 本主题演示如何使用视频索引器编辑器创建项目。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: 9f16ab34dc9b37806f9c58b22a3f02afe839632e
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839167"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>使用视频索引器编辑器创建项目

视频索引器网站使你可以使用视频深入见解来：查找正确的媒体内容，找到你感兴趣的部分，并使用结果创建全新的项目。 创建后，可以从视频索引器呈现和下载项目，并在自己的编辑应用程序或下游工作流中使用。

在某些情况下，你可能会发现此功能非常有用： 

* 为拖车创建电影亮点。
* 在新闻强制转换中使用旧的视频剪辑。
* 为社交媒体创建较短的内容。

本文介绍如何从头开始创建项目，以及如何从帐户中的视频创建项目。

## <a name="create-new-project-and-manage-videos"></a>创建新项目并管理视频

1. 浏览到[视频索引器](https://www.videoindexer.ai/)网站并登录。
1. 选择 "**项目**" 选项卡。如果以前创建了项目，则会在此处看到所有其他项目。
1. 单击 "**创建新项目**"。  

    ![新建项目](./media/video-indexer-view-edit/new-project.png)
1. 单击铅笔图标，为项目指定名称。 将显示 "无标题项目" 的文本替换为你的项目名称，然后单击 "检查"。

    ![新建项目](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>向项目添加视频

> [!NOTE]
> 当前，项目只能包含用同一语言编制索引的视频。 选择一种语言的视频后，将无法在帐户中添加其他语言的视频。

1. 通过选择 "**添加视频**"，添加要在此项目中使用的视频。

    你将看到你的帐户中的所有视频，以及显示 "搜索文本、关键字或视觉内容" 的搜索框。 在脚本和 OCR 中搜索具有指定人员、标签、品牌、关键字或匹配项的视频。
    
    例如，在下图中，我们正在寻找提到 "GitHub" 的视频。
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    可以通过选择 "**筛选结果**" 进一步筛选结果。 您可以通过筛选来显示具有特定人员的视频，或者指定您只想查看以特定语言显示的或具有特定所有者的视频结果。 <br/> 还可以指定查询的作用域。 例如，如果要在 OCR 中搜索 "GitHub"，请选择 "**可视文本**"。

    ![筛选器](./media/video-indexer-view-edit/visual-text.png)

    你可以将多个筛选器分层为你的查询。 使用 " **+** / **-** " 按钮添加/删除筛选器。 使用 "**清除筛选器**" 可以删除所有筛选器。
1. 若要添加视频，请选择它们，然后选择 "**添加**"。
1. 现在，你将看到所选的所有视频。 这些是你要从中选择项目剪辑的视频。

    可以通过拖放或选择 "列表" 菜单按钮，然后选择 "**向下移动**" 或 "**上移**" 来重新排列视频的顺序。 从 "列表" 菜单中，你还可以从该项目中删除视频。 

    ![布置](./media/video-indexer-view-edit/rearrange.png)
    
    可以通过选择 "**添加视频**" 随时将更多视频添加到此项目。 还可以向项目中添加同一视频的多个匹配项。 如果要从一个视频中显示剪辑，然后在另一个视频中显示剪辑，然后在第一个视频中显示另一个剪辑，则可能需要执行此操作。 

### <a name="select-clips-to-use-in-your-project"></a>选择要在项目中使用的剪辑

如果单击每个视频右侧的向下箭头，将根据时间戳（视频剪辑）打开视频中的见解。 

1. 选择 "**查看见解**" 可自定义要查看和不想查看的见解。 

    ![查看见解](./media/video-indexer-view-edit/insights.png)
1. 若要为特定剪辑创建查询，请使用搜索框 "搜索脚本、视觉对象、人物和标签"。
1. 通过选择 "**筛选器选项**"，添加筛选器以进一步指定你要查找的场景的详细信息。

    ![筛选器选项](./media/video-indexer-view-edit/filter-options.png)

    例如，你可能想要查看在屏幕上 Donovan 棕色时提到 GitHub 的剪辑。 为此，需要添加 "包含" 筛选器，该筛选器将 "人员" 作为见解类型。 然后，需要在筛选器的搜索框中键入 "Donovan 棕色"。
    
    ![包括](./media/video-indexer-view-edit/include.png)
    
    如果需要在 Donovan_棕色不在屏幕上时_提到 GitHub 的剪辑，只需使用下拉列表将 "包含" 筛选器更改为 "排除" 筛选器。 

1. 通过选择要添加的段，将剪辑添加到项目。 您可以通过再次单击该段来取消选择此剪切。
    
    通过单击视频旁边的列表菜单选项并选择 "**选择所有段**" 来添加视频的所有段。 

    ![全部添加](./media/video-indexer-view-edit/add-all.png)

    可以通过选择 "清除选择" 清除所有选择。

> [!TIP]
> 选择剪辑并对其进行排序时，可以在页面右侧预览播放机中的视频。 

![预览](./media/video-indexer-view-edit/preview.png)

请记住在进行更改时保存项目，方法是选择 "**保存项目**"。 

### <a name="render-and-download-the-project"></a>呈现和下载项目

> [!NOTE]
> 对于视频索引器付费帐户，呈现项目具有编码成本。 视频索引器试用帐户的呈现时间限制为5小时。

1. 完成后，请确保已保存项目。 现在可以呈现此项目。 选择 "**呈现" 和 "下载**"。 

    ![保存](./media/video-indexer-view-edit/save.png)

    将出现一个弹出窗口，告诉你视频索引器将呈现一个文件，然后下载链接将发送到你的电子邮件。 选择 "继续"。 
    
    还会看到一条通知，指出项目是在页面顶部呈现的。 完成呈现后，您将看到一条新通知，指出项目已成功呈现。 单击通知以下载项目。 它将按 "格式" 格式下载项目。

    ![呈现完成](./media/video-indexer-view-edit/rendering-done.png)

1. 你可以从 "**项目**" 选项卡访问保存的项目。 

    如果选择此项目，将看到此项目的所有见解和时间线。 如果选择 "**视频编辑器**"，则可以继续编辑此项目。 编辑包括添加或删除视频和剪辑，或重命名项目。

    ![视频编辑器](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>从视频创建项目

你可以直接从你的帐户中的视频创建一个新项目。 

1. 请参阅视频索引器网站的 "**库**" 选项卡。
1. 打开要用于创建项目的视频。 在 "见解和时间线" 页上，选择 "**视频编辑器**" 按钮。

    这会转到用于创建新项目的同一页面。 与新项目不同，你会看到视频的带时间戳的见解段，你先前已开始编辑。

## <a name="see-also"></a>另请参阅

[视频索引器概述](video-indexer-overview.md)

