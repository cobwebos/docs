---
title: 使用视频索引器网站自定义语言模型 - Azure
titlesuffix: Azure Media Services
description: 本文介绍如何使用视频索引器网站自定义语言模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/03/2018
ms.author: anzaman
ms.openlocfilehash: 9bf48a994c3efeb433bcb99342f7a477d87858eb
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283583"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>使用视频索引器网站自定义语言模型

视频索引器可以让你创建自定义语言模型来自定义语音识别，所用方法是上传自适应文本，即其词汇需要引擎来适应的领域中的文本。 训练模型以后，就可以识别出现在自适应文本中的新词汇。 

有关自定义语言模型的详细概述和最佳做法，请参阅[使用视频索引器自定义语言模型](customize-language-model-overview.md)。

可以使用视频索引器网站在帐户中创建和编辑自定义语言模型，如本主题所述。 也可使用 API，如[使用 API 自定义语言模型](customize-language-model-with-api.md)中所述。

## <a name="create-a-language-model"></a>创建语言模型

1. 浏览到[视频索引器](https://www.videoindexer.ai/)网站并登录。
2. 若要在帐户中自定义某个模型，请单击页面右上角的“内容模型自定义”按钮。

   ![自定义内容模型](./media/content-model-customization/content-model-customization.png)

3. 选择“语言”选项卡。

    此时会看到受支持的语言的列表。 

    ![自定义语言模型](./media/customize-language-model/customize-language-model.png)

4. 在所需语言下单击“添加模型”。
5. 键入语言模型的名称，然后按 Enter。

    这样就可以创建模型并提供将文本文件上传到模型的选项。
6. 若要添加文本文件，请单击“添加文件”。 此时会打开文件资源管理器。

7. 导航到文本文件并将其选中。 可以将多个文本文件添加到语言模型。

    也可单击语言模型右侧的“...”按钮，然后选择“添加文件”，以这种方式添加文本文件。
8. 上传完文本文件以后，请单击绿色的“训练”选项。

    ![训练语言模型](./media/customize-language-model/train-model.png)

训练过程可能需要数分钟。 训练完以后，模型旁边会出现“已训练”字样。 可以预览、下载和删除模型中的此文件。

![已训练的语言模型](./media/customize-language-model/preview-model.png)

### <a name="using-a-language-model-on-a-new-video"></a>在新视频上使用语言模型

若要在新视频上使用语言模型，请执行以下操作之一：

* 单击页面顶部的“上传”按钮 

    ![上载](./media/customize-language-model/upload.png)
* 将音频或视频文件置于圆圈中，或者以浏览方式查找文件

    ![上载](./media/customize-language-model/upload2.png)

这样即可选择“视频源语言”。 单击下拉列表，从其中选择已创建的语言模型。 它会显示语言模型的语言，以及在括号中提供的名称。

在页面底部单击“上传”选项，系统就会使用语言模型为新视频编制索引。

### <a name="using-a-language-model-to-reindex"></a>使用语言模型重新编制索引

若要使用语言模型为集合中的视频重新编制索引，请转到[视频索引器](https://www.videoindexer.ai/)主页上的“帐户视频”，将鼠标悬停在要重新编制索引的视频的名称上方。

此时会看到用于编辑视频、删除视频以及为视频重新编制索引的选项。 单击为视频重新编制索引的选项。

![重新编制索引](./media/customize-language-model/reindex1.png)

这样即可选择为视频重新编制索引时使用的“视频源语言”。 单击下拉列表，从其中选择已创建的语言模型。 它会显示语言模型的语言，以及在括号中提供的名称。

![重新编制索引](./media/customize-language-model/reindex.png)

单击“重新编制索引”按钮，系统就会使用语言模型为视频重新编制索引。

## <a name="edit-a-language-model"></a>编辑语言模型

编辑语言模型时，可以更改其名称、向其添加文件，以及删除其中的文件。

如果在语言模型中添加或删除文件，则需单击绿色的“训练”选项，再次训练模型。

### <a name="rename-the-language-model"></a>重命名语言模型

可以更改语言模型的名称，方法是单击语言模型右侧的“...”，然后选择“重命名”。 

键入新名称，然后按 Enter。

### <a name="add-files"></a>添加文件

若要添加文本文件，请单击“添加文件”。 此时会打开文件资源管理器。

导航到文本文件并将其选中。 可以将多个文本文件添加到语言模型。

也可单击语言模型右侧的“...”按钮，然后选择“添加文件”，以这种方式添加文本文件。

### <a name="delete-files"></a>删除文件

若要删除语言模型中的文件，请单击文本文件右侧的“...”按钮，然后选择“删除”。 此时会出现一个新窗口，指出删除操作无法撤消。 在新窗口中单击“删除”选项。

此操作会从语言模型中彻底删除该文件。

## <a name="delete-a-language-model"></a>删除语言模型

若要删除帐户中的语言模型，请单击语言模型右侧的“...”按钮，然后选择“删除”。

此时会出现一个新窗口，指出删除操作无法撤消。 在新窗口中单击“删除”选项。

此操作会从帐户中彻底删除该语言模型。 使用已删除语言模型的视频会保留相同的索引，直至你为该视频重新编制索引。 如果重新为视频编制索引，则可为视频分配新的语言模型。 否则，视频索引器会使用其默认模型重新为视频编制索引。 

## <a name="next-steps"></a>后续步骤

[使用 API 自定义语言模型](customize-language-model-with-api.md)
