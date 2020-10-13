---
title: 利用视频索引器网站自定义语言模型
titleSuffix: Azure Media Services
description: 了解如何使用视频索引器网站自定义语言模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/10/2020
ms.author: kumud
ms.openlocfilehash: f9eedba6c8aee5c8aab73e2ed598a4511b4c5982
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020450"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>使用视频索引器网站自定义语言模型

视频索引器可以让你通过上传自适应文本（即其词汇需要引擎来适应的领域中的文本）创建自定义语言模型来自定义语音识别。 训练模型后，可以识别自适应文本中显示的新单词。

有关自定义语言模型的详细概述和最佳实践，请参阅 [使用视频索引器自定义语言模型](customize-language-model-overview.md)。

可以使用视频索引器网站在帐户中创建和编辑自定义语言模型，如本主题所述。 也可使用 API，如[使用 API 自定义语言模型](customize-language-model-with-api.md)中所述。

## <a name="create-a-language-model"></a>创建语言模型

1. 请参阅 [视频索引器](https://www.videoindexer.ai/) 网站并登录。
2. 若要在帐户中自定义模型，请在页面的右上角选择 " **内容模型自定义** " 按钮。

   ![自定义视频索引器中的内容模型](./media/content-model-customization/content-model-customization.png)

3. 选择“语言”选项卡。****

    此时会看到受支持的语言的列表。

    ![视频索引器中的语言模型列表](./media/customize-language-model/customize-language-model.png)

4. 在所需的语言下，选择 " **添加模型**"。
5. 键入语言模型的名称，然后按 Enter。

    此步骤将创建模型，并提供将文本文件上传到模型的选项。

6. 若要添加文本文件，请选择 " **添加文件**"。 文件资源管理器将打开。

7. 导航到文本文件并将其选中。 可以将多个文本文件添加到语言模型。

    您还可以通过选择语言模型右侧的 **...** 按钮添加文本文件，然后选择 " **添加文件**"。

8. 完成上传完文本文件后，选择绿色 **训练** 选项。

    ![在视频索引器中训练语言模型](./media/customize-language-model/train-model.png)

训练过程可能需要数分钟。 训练完以后，模型旁边会出现“已训练”字样。**** 可以预览、下载和删除模型中的此文件。

![视频索引器中训练的语言模型](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>在新视频上使用语言模型

若要在新视频上使用你的语言模型，请执行以下操作之一：

* 选择页面顶部的 " **上传** " 按钮。

    ![上传按钮视频索引器](./media/customize-language-model/upload.png)

* 将音频或视频文件放入圆圈，或浏览文件。

    ![上传媒体文件视频索引器](./media/customize-language-model/upload2.png)

你可以选择 **视频源语言**。 选择下拉列表，并从列表中选择创建的语言模型。 它会显示语言模型的语言，以及在括号中提供的名称。

选择页面底部的 " **上传** " 选项，新视频将使用您的语言模型进行索引。

### <a name="using-a-language-model-to-reindex"></a>使用语言模型重新编制索引

若要使用语言模型为集合中的视频重新编制索引，请转到****[视频索引器](https://www.videoindexer.ai/)主页上的“帐户视频”，将鼠标悬停在要重新编制索引的视频的名称上方。

你将看到用于编辑视频、删除视频和为视频重建索引的选项。 选择该选项以重新索引视频。

![用视频索引器重新索引](./media/customize-language-model/reindex1.png)

你可以选择 **视频源语言** 来为视频重建索引。 选择下拉列表，并从列表中选择创建的语言模型。 它会显示语言模型的语言，以及在括号中提供的名称。

![选择视频源语言-使用视频索引器重新索引视频](./media/customize-language-model/reindex.png)

选择 " **重新索引** " 按钮，将使用您的语言模型重新编制索引视频。

## <a name="edit-a-language-model"></a>编辑语言模型

编辑语言模型时，可以更改其名称、向其添加文件，以及删除其中的文件。

如果你在语言模型中添加或删除文件，则必须通过选择绿色 **定型** 选项来再次训练该模型。

### <a name="rename-the-language-model"></a>重命名语言模型

您可以通过选择语言模型右侧的 " (**...** ") 按钮，然后选择 " **重命名**"，来更改语言模型的名称。

键入新名称，然后按 Enter。

### <a name="add-files"></a>添加文件

若要添加文本文件，请选择 " **添加文件**"。 文件资源管理器将打开。

导航到文本文件并将其选中。 可以将多个文本文件添加到语言模型。

还可以通过选择语言模型右侧的 " (**...** ") 按钮添加文本文件，然后选择 " **添加文件**"。

### <a name="delete-files"></a>删除文件

若要从语言模型删除文件，请选择文本文件右侧的 "省略号 (**...** ") 按钮，然后选择 " **删除**"。 系统会弹出一个新窗口，告诉您无法撤消删除操作。 选择 "新建" 窗口中的 " **删除** " 选项。

此操作会从语言模型中彻底删除该文件。

## <a name="delete-a-language-model"></a>删除语言模型

若要从你的帐户中删除语言模型，请选择语言模型右侧 (**...** ") 按钮，然后选择" **删除**"。

系统会弹出一个新窗口，告诉您无法撤消删除操作。 选择 "新建" 窗口中的 " **删除** " 选项。

此操作会从帐户中彻底删除该语言模型。 使用已删除的语言模型的任何视频都将保留相同的索引，直到对视频重新建立索引。 如果对视频重新编制索引，则可以将新的语言模型分配给视频。 否则，视频索引器将使用其默认模型来重新索引视频。

## <a name="customize-language-models-by-correcting-transcripts"></a>通过更正脚本自定义语言模型

视频索引器支持根据用户对其视频的转录做出的实际更正，自动自定义语言模型。

1. 若要对脚本进行更正，请打开要从帐户视频中编辑的视频。 选择 " **时间线** " 选项卡。

    ![自定义语言模型时间线选项卡-视频索引器](./media/customize-language-model/timeline.png)

1. 选择铅笔图标以编辑脚本的脚本。

    ![自定义语言模型编辑脚本-视频索引器](./media/customize-language-model/edits.png)

    视频索引器捕获在视频中更正的所有行，并将它们自动添加到名为 "从脚本编辑" 的文本文件中。 这些编辑用于重新训练用于为此视频编制索引的特定语言模型。 
    
    还包括在 [小组件](video-indexer-embed-widgets.md) 时间线中进行的编辑。
    
    如果在对此视频编制索引时未指定语言模型，则此视频的所有编辑都将存储在检测到的视频语言内名为 "Account 适应" 的默认语言模型中。
    
    如果对同一行进行了多次编辑，则只会使用最新版本的已更正行来更新语言模型。  
    
    > [!NOTE]
    > 只有文本更正可用于自定义。 不包含实际词语 (例如，标点符号或空格) 不包括在内。
    
1. 你将在 "内容模型自定义" 页的 "语言" 选项卡中看到 "脚本更正"。

    ![自定义语言模型-视频索引器](./media/customize-language-model/customize.png)

   若要查看每个语言模型的 "从脚本编辑" 文件，请选择将其打开。

    ![From 脚本编辑-视频索引器](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>后续步骤

[使用 Api 自定义语言模型](customize-language-model-with-api.md)
