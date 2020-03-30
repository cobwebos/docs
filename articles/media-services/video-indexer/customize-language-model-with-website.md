---
title: 使用视频索引器网站自定义语言模型
titleSuffix: Azure Media Services
description: 了解如何使用视频索引器网站自定义语言模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 8917a3ac302d18337d79bffce69bad108667b4d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128077"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>使用视频索引器网站自定义语言模型

视频索引器可以让你通过上传自适应文本（即其词汇需要引擎来适应的领域中的文本）创建自定义语言模型来自定义语音识别。 训练模型后，可以识别自适应文本中显示的新单词。

有关自定义语言模型的详细概述和最佳实践，请参阅[使用视频索引器自定义语言模型](customize-language-model-overview.md)。

可以使用视频索引器网站在帐户中创建和编辑自定义语言模型，如本主题所述。 也可使用 API，如[使用 API 自定义语言模型](customize-language-model-with-api.md)中所述。

## <a name="create-a-language-model"></a>创建语言模型

1. 转到[视频索引器](https://www.videoindexer.ai/)网站并登录。
2. 要自定义帐户中的模型，请选择页面右上角**的内容模型自定义**按钮。

   ![在视频索引器中自定义内容模型](./media/content-model-customization/content-model-customization.png)

3. 选择“语言”选项卡。****

    此时会看到受支持的语言的列表。

    ![视频索引器中的语言模型列表](./media/customize-language-model/customize-language-model.png)

4. 在所需的语言下，选择 **"添加模型**"。
5. 键入语言模型的名称，然后按 Enter。

    此步骤创建模型，并给出将文本文件上载到模型的选项。

6. 要添加文本文件，请选择"**添加文件**"。 您的文件资源管理器将打开。

7. 导航到文本文件并将其选中。 可以将多个文本文件添加到语言模型。

    您还可以通过选择语言模型右侧的 **...** 按钮并选择 **"添加文件**"来添加文本文件。

8. 上传完文本文件后，选择绿色 **"训练"** 选项。

    ![视频索引器中的训练语言模型](./media/customize-language-model/train-model.png)

训练过程可能需要数分钟。 训练完以后，模型旁边会出现“已训练”字样。**** 可以预览、下载和删除模型中的此文件。

![视频索引器中训练的语言模型](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>在新视频上使用语言模型

要在新视频上使用语言模型，请执行以下操作之一：

* 选择页面顶部的 **"上传**"按钮。

    ![上传按钮 视频索引器](./media/customize-language-model/upload.png)

* 将音频或视频文件放入圆圈或浏览文件。

    ![上传媒体文件 视频索引器](./media/customize-language-model/upload2.png)

您可以选择**视频源语言**。 选择下拉列表并选择从列表中创建的语言模型。 它会显示语言模型的语言，以及在括号中提供的名称。

选择页面底部的 **"上传**"选项，将使用语言模型对新视频进行索引。

### <a name="using-a-language-model-to-reindex"></a>使用语言模型重新编制索引

若要使用语言模型为集合中的视频重新编制索引，请转到****[视频索引器](https://www.videoindexer.ai/)主页上的“帐户视频”，将鼠标悬停在要重新编制索引的视频的名称上方。

您将看到编辑视频、删除视频以及重新索引视频的选项。 选择重新索引视频的选项。

![使用视频索引器重新索引](./media/customize-language-model/reindex1.png)

您可以选择**视频源语言**以重新索引视频。 选择下拉列表并选择从列表中创建的语言模型。 它会显示语言模型的语言，以及在括号中提供的名称。

![选择视频源语言 — 使用视频索引器重新索引视频](./media/customize-language-model/reindex.png)

选择 **"重新索引**"按钮，您的视频将使用语言模型重新编制索引。

## <a name="edit-a-language-model"></a>编辑语言模型

编辑语言模型时，可以更改其名称、向其添加文件，以及删除其中的文件。

如果从"语言"模型添加或删除文件，则必须通过选择绿色 **"训练"** 选项再次训练模型。

### <a name="rename-the-language-model"></a>重命名语言模型

您可以通过选择语言模型右侧的省略号 **（...）** 按钮并选择重命名来更改语言模型**的名称**。

键入新名称，然后按 Enter。

### <a name="add-files"></a>添加文件

要添加文本文件，请选择"**添加文件**"。 您的文件资源管理器将打开。

导航到文本文件并将其选中。 可以将多个文本文件添加到语言模型。

您还可以通过选择语言模型右侧的省略号 **（...）** 按钮并选择 **"添加文件**"来添加文本文件。

### <a name="delete-files"></a>删除文件

要从语言模型中删除文件，请选择文本文件右侧的省略号 **（...）** 按钮，然后选择 **"删除**"。 弹出一个新窗口，告诉您无法撤消删除。 在新窗口中选择 **"删除**"选项。

此操作会从语言模型中彻底删除该文件。

## <a name="delete-a-language-model"></a>删除语言模型

要从帐户中删除语言模型，请选择"语言模型"右侧的省略号 **（...）** 按钮，然后选择 **"删除**"。

弹出一个新窗口，告诉您无法撤消删除。 在新窗口中选择 **"删除**"选项。

此操作会从帐户中彻底删除该语言模型。 使用已删除的语言模型的任何视频都将保留相同的索引，直到重新索引视频。 如果重新对视频进行索引，则可以为视频分配新的语言模型。 否则，视频索引器将使用其默认模型重新索引视频。

## <a name="customize-language-models-by-correcting-transcripts"></a>通过更正成绩单自定义语言模型

视频索引器支持根据用户对其视频转录的实际更正自动自定义语言模型。

1. 要更正脚本，请打开要从帐户视频编辑的视频。 选择 **"时间轴"** 选项卡。

    ![自定义语言模型时间线选项卡_视频索引器](./media/customize-language-model/timeline.png)

1. 选择铅笔图标以编辑转录的抄本。

    ![自定义语言模型编辑转录器_视频索引器](./media/customize-language-model/edits.png)

    视频索引器捕获您在视频转录中更正的所有行，并自动将它们添加到名为"从脚本编辑"的文本文件中。 这些编辑用于重新训练用于索引此视频的特定语言模型。
    
    如果在索引此视频时未指定语言模型，则此视频的所有编辑都将存储在视频检测到的语言中称为"帐户适应"的默认语言模型中。
    
    如果对同一行进行了多次编辑，则仅使用已更正行的最后一个版本来更新语言模型。  
    
    > [!NOTE]
    > 仅用于自定义的文本更正。 不包括不涉及实际单词（例如，标点符号或空格）的更正。
    
1. 在"内容模型自定义"页的"语言"选项卡中将显示脚本更正。

    ![自定义语言模型—视频索引器](./media/customize-language-model/customize.png)

   要查看每个语言模型的"从脚本编辑"文件，请选择该文件以将其打开。

    ![从脚本编辑 — 视频索引器](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>后续步骤

[使用 API 自定义语言模型](customize-language-model-with-api.md)
