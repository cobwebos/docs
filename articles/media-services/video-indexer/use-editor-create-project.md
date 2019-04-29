---
title: 使用视频索引器编辑器创建项目
titlesuffix: Azure Media Services
description: 本主题演示如何使用视频索引器编辑器创建的项目。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.openlocfilehash: a9d6396cab560a201b98497e787af4b6c7c2dabb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560750"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>使用视频索引器编辑器创建项目

视频索引器网站，可以使用视频的深入见解： 查找正确的媒体内容中，找到您感兴趣，并使用结果创建全新的项目的部件。 创建后，可以呈现和视频索引器从下载该项目并在自己的编辑应用程序或下游工作流中使用。

下您可能会发现此功能非常有用的一些方案是： 

* 创建电影重点强调了尾部。
* 新闻转换中使用旧的视频剪辑。
* 创建用于社交媒体较短的内容。

本文介绍如何从头开始创建项目以及如何从您的帐户中的视频中创建一个项目。

## <a name="create-new-project-and-manage-videos"></a>创建新的项目和管理视频

1. 浏览到[视频索引器](https://www.videoindexer.ai/)网站并登录。
1. 选择**项目**选项卡。如果你已创建项目之前，您将看到所有其他项目下面。
1. 单击**创建新项目**。  

    ![新建项目](./media/video-indexer-view-edit/new-project.png)
1. 通过单击铅笔图标为你的项目指定名称。 显示"无标题的项目"的文本替换为你的项目名称并单击复选标记。

    ![新建项目](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>将视频添加到项目

> [!NOTE]
> 目前，项目可能只包含在相同的语言中编制索引的视频。 后一种语言中选择一个视频，您不能采用不同的是在帐户中添加视频。

1. 添加你想要使用此项目中通过选择的视频**将视频添加**。

    在你的帐户和一个搜索框，显示"搜索文本、 关键字或视觉内容"中，你将看到所有视频。 若要搜索具有指定的人员、 标签、 品牌、 关键字或在脚本和 OCR 的匹配项的视频。
    
    例如，在下图中，我们正在寻找提及"GitHub"的视频。
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    通过选择可以进一步筛选结果**筛选结果**。 您可以筛选显示中都有了某个人的视频或指定您只想要查看的视频结果中某一种语言或具有特定的所有者。 <br/> 此外可以指定查询的作用域。 例如，如果你想要在 OCR 中搜索"GitHub"，选择**视觉文本**。

    ![筛选器](./media/video-indexer-view-edit/visual-text.png)

    您可以在查询层多个筛选器。 使用**+** / **-** 按钮来添加/删除筛选器。 使用**清除筛选器**中删除所有筛选器。
1. 若要添加视频，请选择它们，然后选择**添加**。
1. 现在，您将看到所有所选的视频。 这些是要从中选择为你的项目的剪辑的视频。

    通过拖放方式或通过选择列表菜单按钮，选择，您可以重新排列的视频的顺序**向下移动**或**向上移动**。 从列表菜单中，您还将能够在此项目中删除该视频。 

    ![重新排列](./media/video-indexer-view-edit/rearrange.png)
    
    可以选择多个将视频添加到此项目在任何时候通过选择**将视频添加**。 此外可以向项目添加相同的视频的多个匹配项。 您可能想要执行此操作，如果你想要显示一个视频，然后从另一个剪辑中的剪辑和另一次从第一个视频剪辑。 

### <a name="select-clips-to-use-in-your-project"></a>选择要在你的项目中使用的剪辑

如果单击每个视频右侧的向下箭头，则会打开基于时间戳 （视频剪辑的） 视频中的见解。 

1. 选择**查看见解**自定义哪些 insights 要查看的内容并不想要查看其。 

    ![查看见解](./media/video-indexer-view-edit/insights.png)
1. 若要创建特定的剪辑的查询，请使用搜索框中显示"在脚本、 视觉文本、 人员和标签中搜索"。
1. 添加筛选器来进一步指定详细信息通过选择正在寻找哪些场景**筛选器选项**。

    ![筛选器选项](./media/video-indexer-view-edit/filter-options.png)

    例如，你可能想要查看的剪辑 Donovan Brown 在屏幕上时其中提到 GitHub。 为此，您需要添加具有"People"的"include"筛选器作为见解的类型。 然后需要在"Donovan Brown"筛选器在搜索框中键入。
    
    ![包括](./media/video-indexer-view-edit/include.png)
    
    如果你想 Donovan Brown 时其中提到 GitHub 剪辑_不_在屏幕上，您只需将更改"include"筛选器到使用下拉列表中的"exclude"筛选器。 

1. 通过选择你想要添加的段将剪辑添加到你的项目。 可以通过再次单击段，请取消选中此代码片段。
    
    通过单击旁边的视频和选择的列表菜单选项添加视频的所有段**选择的所有段**。 

    ![全部添加](./media/video-indexer-view-edit/add-all.png)

    可以通过选择清除选定内容清除所有所选内容。

> [!TIP]
> 选择和排序剪辑时，您可以预览页面右侧播放机中的视频。 

![预览](./media/video-indexer-view-edit/preview.png)

通过选择进行更改时保存项目，请务必**保存项目**。 

### <a name="render-and-download-the-project"></a>呈现和下载项目

> [!NOTE]
> 对于付费帐户视频索引器，呈现你的项目具有编码成本。 视频索引器试用版帐户仅限于呈现的 5 个小时。

1. 完成后，请确保已保存你的项目。 您现在可以呈现此项目。 选择**呈现并下载**。 

    ![保存](./media/video-indexer-view-edit/save.png)

    将有一个弹出窗口，指示视频索引器将呈现一个文件，然后下载链接将发送到你的电子邮件。 选择继续执行。 
    
    您将看到项目正在呈现页面顶部的通知。 完成操作后所呈现，您将看到已成功呈现项目的新通知。 单击通知以下载项目。 它将下载的 mp4 格式中的项目。

    ![呈现完成](./media/video-indexer-view-edit/rendering-done.png)

1. 您可以访问已保存的项目从**项目**选项卡。 

    如果选择此项目时，会看到所有的见解和此项目的时间线。 如果选择**视频编辑器**，可以继续对此项目进行编辑。 编辑包括添加或删除视频和剪辑或重命名项目。

    ![视频编辑器](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>从视频中创建项目

在帐户中，可以直接从视频中创建一个新的项目。 

1. 转到**库**视频索引器网站的选项卡。
1. 打开你想要用于创建你的项目的视频。 在 insights 和时间线页中，选择**视频编辑器**按钮。

    将转到用于创建新的项目的同一页面。 与不同的是新项目中，您将看到你启动以前编辑的视频的时间戳的 insights 段。

## <a name="see-also"></a>另请参阅

[视频索引器概述](video-indexer-overview.md)

