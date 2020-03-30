---
title: 使用视频索引器网站自定义人员模型
titleSuffix: Azure Media Services
description: 了解如何使用视频索引器网站自定义人员模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: f29e651f5c8542722f0dc2c9878184ac0d2a6a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499972"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>使用视频索引器网站自定义人员模型

视频索引器支持名人对视频内容的识别。 名人识别功能基于通常请求的数据源（如 IMDB、Wikipedia 和 LinkedIn 影响者排名）涵盖约一百万张人脸。 有关详细概述，请参阅[在视频索引器中自定义人员模型](customize-person-model-overview.md)。

可以使用视频索引器网站编辑视频中检测到的人脸，如本主题所述。 您还可以使用 API，如[使用 API 自定义人员模型](customize-person-model-with-api.md)中所述。

## <a name="central-management-of-person-models-in-your-account"></a>帐户中人员模型的集中管理

1. 要查看、编辑和删除帐户中的"人"模型，请浏览到视频索引器网站并登录。

2. 选择页面右上角的内容模型自定义按钮。

    ![内容模型自定义](./media/customize-face-model/content-model-customization.png)

3. 选择"人员"选项卡。

    您将在帐户中看到"默认人员"模型。 默认人员模型包含您可能编辑或更改的视频见解中的任何面孔，而您在索引期间未为其指定自定义"人"模型。

    如果您创建了其他 Person 模型，它们也会在此页上列出。

    ![内容模型自定义](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>创建新的人员模型

1. 选择 **"添加模型"** 按钮。

    ![添加新的人员模型](./media/customize-face-model/add-new-person.png)

2. 输入模型的名称，然后选择名称旁边的检查按钮。

    ![添加新的人员模型](./media/customize-face-model/add-new-person2.png)

    您已经创建了一个新的人员模型。 您现在可以向新的"人"模型添加面。

3. 选择列表菜单按钮，然后选择 **"添加人员**"。

    ![添加新的人员模型](./media/customize-face-model/add-new-person3.png)

## <a name="add-a-new-person-to-a-person-model"></a>将新人添加到人员模型

> [!NOTE]
> 视频索引器允许您在人员模型中添加多个同名人员。 但是，建议您为模型中的每个人提供唯一的名称，以确保可用性和清晰度。

1. 要向"人"模型添加新面，请选择要向其添加面的"人"模型旁边的列表菜单按钮。

1. 从菜单中选择**和添加人员**。

    ![向人添加新面孔](./media/customize-face-model/add-new-face.png)

    弹出窗口将提示您填写此人的详细信息。 键入人员的姓名并选择检查按钮。

    ![向人添加新面孔](./media/customize-face-model/add-new-face2.png)

    然后，您可以从文件资源管理器中进行选择，也可以拖放人脸图像。 视频索引器将采用所有标准图像文件类型（例如：JPG、PNG 等）。

    视频索引器可以使用添加到此新面孔的人员模型，检测此人在将来视频中的发生情况，以及您已索引的当前视频。 识别当前视频中的人员可能需要一些时间才能生效，因为这是一个批处理过程。

## <a name="rename-a-person-model"></a>重命名人员模型

您可以重命名帐户中的任意人员模型，包括"默认人员"模型。 即使您重命名默认的"人"模型，它仍将作为帐户中的默认人员模型。

1. 选择要重命名的"人员"模型旁边的列表菜单按钮。
2. 从菜单中选择“重命名”****。

    ![重命名人员模型](./media/customize-face-model/rename-person.png)

3. 选择模型的当前名称，并在新名称中键入。

    ![重命名人员模型](./media/customize-face-model/rename-person2.png)

4. 选择要重命名模型的检查按钮。

## <a name="delete-a-person-model"></a>删除人员模型

您可以删除您在帐户中创建的任意人员模型。 但是，您无法删除默认人员模型。

1. 从菜单中选择“删除”****。

    ![删除人员模型](./media/customize-face-model/delete-person.png)

    将显示一个弹出窗口，并通知您此操作将删除"人"模型及其包含的所有人员文件。 无法撤消此操作。

    ![删除人员模型](./media/customize-face-model/delete-person2.png)

1. 如果确定，请选择"删除"。

> [!NOTE]
> 使用此（现已删除）人员模型编制索引的现有视频不支持您更新视频中显示的人脸名称的能力。 只有在使用其他"人"模型重新索引这些视频中的人脸名称后，才能编辑这些视频中的人脸名称。 如果不指定"人"模型，则重新编制索引，将使用默认模型。

## <a name="manage-existing-people-in-a-person-model"></a>在人员模型中管理现有人员

要查看任何"人"模型的内容，请选择"人"模型名称旁边的箭头。 下拉列表显示该特定人物模型中的所有人员。 如果选择每个人旁边的列表菜单按钮，您将看到管理、重命名和删除选项。  

![向人添加新面孔](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>重命名人员

1. 要重命名"人"模型中的人员，请选择列表菜单按钮，然后从列表菜单中选择 **"重命名**"。
1. 选择此人的当前姓名并键入新名称。
1. 选择检查按钮，该人员将被重命名。

### <a name="delete-a-person"></a>删除某人

1. 要从"人人"模型中删除人员，请选择列表菜单按钮，然后从列表菜单中选择 **"删除**"。
1. 弹出窗口将告诉您将删除此人，并且无法撤消此操作。
1. 再次选择 **"删除**"，这将从"人员"模型中删除此人。

### <a name="manage-a-person"></a>管理人员

如果选择 **"管理**"，您将看到正在训练此人员模型的所有面。 这些人脸来自使用此人员模型的视频或手动上传的图像中该人员的发生率。

您可以通过选择 **"添加图像**"向人添加更多面。

您可以使用管理窗格重命名人员并从人员模型中删除该人员。

## <a name="use-a-person-model-to-index-a-video"></a>使用人员模型对视频进行索引

您可以在视频上传期间分配"人"模型，使用"人"模型为新视频编制索引。

要在新视频上使用您的人员模型，请执行以下步骤：

1. 选择页面顶部的 **"上传**"按钮。

    ![上传人员模型](./media/customize-face-model/upload.png)

1. 将视频文件放在圆圈中或浏览文件。
1. 选择 **"高级"选项**箭头。

    ![上传人员模型](./media/customize-face-model/upload2.png)

1. 选择下拉列表并选择您创建的"人"模型。

    ![上传人员模型](./media/customize-face-model/upload3.png)

1. 选择页面底部的 **"上传**"选项，将使用"人"模型对新视频进行索引。

如果在上传过程中未指定"人"模型，视频索引器将使用帐户中的"默认人员"模型对视频进行索引。

## <a name="use-a-person-model-to-reindex-a-video"></a>使用人员模型重新索引视频

要使用 Person 模型重新索引集合中的视频，请转到视频索引器主页上的帐户视频，并悬停在要重新索引的视频名称上。

您将看到用于编辑、删除和重新索引视频的选项。

1. 选择重新索引视频的选项。

    ![使用人员模型重新索引视频](./media/customize-face-model/reindex.png)

    您现在可以选择"人员"模型来重新索引视频。
1. 选择下拉列表并选择要使用的人员模型。

    ![使用人员模型重新索引视频](./media/customize-face-model/reindex2.png)

1. 选择 **"重新索引**"按钮，您的视频将使用"人"模型重新编制索引。

对视频中检测到和识别的刚刚重新编制索引的人脸进行的任何新编辑都将保存在用于重新索引视频的 Person 模型中。

## <a name="managing-people-in-your-videos"></a>管理视频中的人

您可以通过编辑和删除人脸来管理检测到的人脸以及您索引的视频中识别的人。

删除面会从视频的见解中删除特定面。

编辑人脸会重命名视频中检测到并可能识别的人脸。 编辑视频中的人脸时，该名称将保存为在上传和索引期间分配给视频的人员模型中的人员条目。

如果在上传过程中未为视频分配"人员"模型，则您的编辑将保存在帐户的"默认人员"模型中。

### <a name="edit-a-face"></a>编辑人脸

> [!NOTE]
> 如果 Person 模型具有两个或多个具有相同名称的人员，则您将无法在使用该 Person 模型的视频中标记该名称。 您只能在视频索引器中内容模型自定义页的"人员"选项卡中对共享该名称的用户进行更改。 因此，建议您为 Person 模型中的每个人提供唯一名称。

1. 浏览到视频索引器网站并登录。
1. 搜索要在帐户中查看并编辑的视频。
1. 要编辑视频中的人脸，请转到"见解"选项卡，然后选择窗口右上角的铅笔图标。

    ![编辑视频中的人脸](./media/customize-face-model/edit-face.png)

1. 选择任何检测到的面，并从"未知#X"（或以前分配给面的名称）更改其名称。
1. 键入新名称后，选择新名称旁边的检查图标。 此操作将保存新名称，并在其他当前视频和您上传的未来视频中识别和命名此人脸的所有事件。 识别其他当前视频中的人脸可能需要一定的时间，因为这是一个批处理过程。

如果为视频正在使用的"人"模型中现有人员的姓名命名，则从该人员的此视频中检测到的人脸图像将与模型中已有的人脸图像合并。 如果使用新名称命名人脸，则在视频正在使用的"人"模型中创建新的"人"条目。

![编辑视频中的人脸](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>删除人脸

要删除视频中检测到的人脸，请转到"见解"窗格并选择窗格右上角的铅笔图标。 选择面名下方的 **"删除**"选项。 此操作从视频中删除检测到的面。 在出现该人脸的其他视频中，仍会检测到此人的面部，但您可以在这些视频编制索引后从这些视频中删除该人脸。

此人（如果已命名）还将继续存在用于索引从中删除人脸的视频的 Person 模型中，除非您从"人"模型中专门删除此人。

![删除视频中的面部](./media/customize-face-model/delete-face.png)

## <a name="next-steps"></a>后续步骤

[使用 API 自定义人员模型](customize-person-model-with-api.md)
