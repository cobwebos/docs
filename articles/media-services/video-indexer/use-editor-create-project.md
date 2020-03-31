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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839167"
---
# <a name="use-the-video-indexer-editor-to-create-projects"></a>使用视频索引器编辑器创建项目

视频索引器网站，使您能够使用视频的深入见解：找到正确的媒体内容，找到您感兴趣的部件，并使用结果创建一个全新的项目。 创建后，可以从视频索引器呈现和下载项目，并在您自己的编辑应用程序或下游工作流中使用。

您可能会发现此功能有用的一些方案包括： 

* 为预告片创建影片亮点。
* 在新闻播报中使用视频的旧剪辑。
* 为社交媒体创建较短的内容。

本文演示如何从头开始创建项目，以及如何从帐户中的视频创建项目。

## <a name="create-new-project-and-manage-videos"></a>创建新项目并管理视频

1. 浏览到[视频索引器](https://www.videoindexer.ai/)网站并登录。
1. 选择"**项目**"选项卡。如果您以前创建过项目，您将在此处看到所有其他项目。
1. 单击 **"创建新项目**"。  

    ![新建项目](./media/video-indexer-view-edit/new-project.png)
1. 单击铅笔图标，为项目指定名称。 将"无标题项目"的文本替换为项目名称，然后单击检查。

    ![新建项目](./media/video-indexer-view-edit/new-project3.png)
    
### <a name="add-videos-to-the-project"></a>向项目添加视频

> [!NOTE]
> 目前，项目可能仅包含以相同语言索引的视频。 选择一种语言的视频后，您无法在帐户中添加不同语言的视频。

1. 通过选择 **"添加视频**"，添加您要在此项目中使用的视频。

    您将看到帐户中的所有视频以及一个搜索框，上面写着"搜索文本、关键字或视觉内容"。 搜索在脚本和 OCR 中具有指定人员、标签、品牌、关键字或出现的视频。
    
    例如，在下图中，我们正在寻找提及"GitHub"的视频。
    
    ![GitHub](./media/video-indexer-view-edit/github.png)

    您可以通过选择 **"筛选结果**"来进一步筛选结果。 您可以筛选以显示包含特定人员的视频，或指定您只想查看以特定语言或具有特定所有者的视频结果。 <br/> 您还可以指定查询的范围。 例如，如果要在 OCR 中搜索"GitHub"，请选择 **"可视文本**"。

    ![“筛选器”](./media/video-indexer-view-edit/visual-text.png)

    您可以将多个筛选器分层到查询中。 **+**/使用**-** 按钮添加/删除筛选器。 使用**清除筛选器**删除所有筛选器。
1. 要添加视频，请选择它们，然后选择"**添加**"。
1. 现在，您将看到您选择的所有视频。 这些是您将从中选择项目剪辑的视频。

    您可以通过拖放或选择列表菜单按钮并选择 **"向下移动**"或 **"向上移动**"来重新排列视频的顺序。 从列表菜单中，您还可以从此项目中删除视频。 

    ![重新 排列](./media/video-indexer-view-edit/rearrange.png)
    
    您可以选择"添加视频"，随时向此项目**添加更多视频**。 您还可以向项目添加同一视频的多个匹配项。 如果要显示一个视频的剪辑，然后显示另一个视频的剪辑，然后显示第一个视频的另一个剪辑，则可能需要执行此操作。 

### <a name="select-clips-to-use-in-your-project"></a>选择要在项目中使用的剪辑

如果单击每个视频右侧的向下箭头，您将根据时间戳（视频剪辑）打开视频中的见解。 

1. 选择 **"查看见解"** 可自定义要查看的见解以及不想查看的见解。 

    ![查看见解](./media/video-indexer-view-edit/insights.png)
1. 要为特定剪辑创建查询，请使用显示"以脚本、可视文本、人和标签搜索"的搜索框。
1. 通过选择 **"筛选器"选项**，添加筛选器以进一步指定要查找的场景的详细信息。

    ![筛选器选项](./media/video-indexer-view-edit/filter-options.png)

    例如，您可能希望查看在多诺万·布朗在屏幕上时提及 GitHub 的剪辑。 为此，您需要添加一个"包含"筛选器，该筛选器以"人员"为见解类型。 然后，您需要在筛选器的搜索框中键入"多诺万布朗"。
    
    ![包括](./media/video-indexer-view-edit/include.png)
    
    如果您希望在多诺万·布朗_不_在屏幕上时提及 GitHub 的剪辑，只需使用下拉列表将"包含"筛选器更改为"排除"筛选器即可。 

1. 通过选择要添加的片段向项目添加剪辑。 您可以通过再次单击分段来取消选择此剪辑。
    
    通过单击视频旁边的列表菜单选项并**选择"选择所有段**"来添加视频的所有段。 

    ![全部添加](./media/video-indexer-view-edit/add-all.png)

    您可以通过选择"清除"选择来清除所有选择。

> [!TIP]
> 在选择和订购剪辑时，您可以在页面右侧的播放器中预览视频。 

![预览](./media/video-indexer-view-edit/preview.png)

请记住，当您通过选择 **"保存项目"** 进行更改时保存项目。 

### <a name="render-and-download-the-project"></a>渲染和下载项目

> [!NOTE]
> 对于视频索引器付费帐户，渲染项目具有编码成本。 视频索引器试用帐户限制为 5 小时的渲染时间。

1. 完成后，请确保已保存项目。 您现在可以呈现此项目。 选择**渲染和下载**。 

    ![保存](./media/video-indexer-view-edit/save.png)

    将有一个弹出窗口，告诉您视频索引器将呈现一个文件，然后下载链接将发送到您的电子邮件。 选择“继续”。 
    
    您还将看到一条通知，指出项目正在页面顶部呈现。 呈现完成后，您将看到一个新的通知，表明项目已成功呈现。 单击通知以下载项目。 它将以 mp4 格式下载项目。

    ![渲染完成](./media/video-indexer-view-edit/rendering-done.png)

1. 可以从"**项目"** 选项卡访问保存的项目。 

    如果选择此项目，您将看到此项目的所有见解和时间表。 如果选择 **"视频编辑器**"，则可以继续编辑此项目。 编辑包括添加或删除视频和剪辑或重命名项目。

    ![视频编辑器](./media/video-indexer-view-edit/video-editor.png)
     
## <a name="create-a-project-from-your-video"></a>从视频创建项目

您可以直接从帐户中的视频创建新项目。 

1. 转到视频索引器网站的 **"库**"选项卡。
1. 打开要用于创建项目的视频。 在见解和时间线页上，选择 **"视频编辑器"** 按钮。

    这将带您到用于创建新项目的同一页面。 与新项目不同，您会看到以前开始编辑的视频的时间戳见解段。

## <a name="see-also"></a>请参阅

[视频索引器概述](video-indexer-overview.md)

