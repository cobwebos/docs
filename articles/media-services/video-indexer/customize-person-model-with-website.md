---
title: 使用视频索引器网站自定义人员模型 - Azure
titleSuffix: Azure Media Services
description: 本文介绍如何使用视频索引器网站自定义人员模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 060d94d6181e894c18d268845b48eb802c52730c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838278"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>使用视频索引器网站自定义人员模型

视频索引器支持名人识别视频内容。 名人识别功能基于通常请求的数据源（如 IMDB、Wikipedia 和 LinkedIn 影响者排名）涵盖约一百万张人脸。 有关详细概述，请参阅[在视频索引器中自定义人员模型](customize-person-model-overview.md)。

可以使用视频索引器网站编辑视频中检测到的人脸，如本主题所述。 你还可以使用 API，如[使用 Api 自定义人员模型](customize-person-model-with-api.md)中所述。

## <a name="central-management-of-person-models-in-your-account"></a>帐户中人员模型的集中管理

1. 若要查看、编辑和删除帐户中的人员模型，请浏览到视频索引器网站并登录。
2. 在页面的右上角，单击 "内容模型自定义" 按钮。

    ![内容模型自定义](./media/customize-face-model/content-model-customization.png)
3. 选择 "人员" 选项卡。

    你将在帐户中看到默认的 "人员" 模型。 默认人员模型包含你在创建索引期间未指定自定义人员模型的视频见解中编辑或更改的任何人脸。 

    如果创建了其他人员模型，还会在此页上列出它们。 

    ![内容模型自定义](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>创建新的人员模型

1. 单击 " **+ 添加模型**" 按钮。

    ![添加新人员](./media/customize-face-model/add-new-person.png)
2. 输入模型的名称，并单击名称旁边的复选按钮。

    ![添加新人员](./media/customize-face-model/add-new-person2.png)

    您已经创建了一个新的人员模型。 你现在可以将人脸添加到新人员模型中。
3. 单击 "列表" 菜单按钮，然后选择 " **+ 添加人员**"。

    ![添加新人员](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>向人员模型添加新人员

> [!NOTE]
> 视频索引器允许您在人员模型中添加具有相同名称的多个用户。 但建议为模型中的每个人员提供 unque 名称，以确保可用性和清晰度。

1. 若要将新的人脸添加到人员模型，请单击要向其添加人脸的人员模型旁边的 "列表" 菜单按钮。
1. 单击菜单中的 " **+ 添加人员**"。

    ![向人员添加新的人脸](./media/customize-face-model/add-new-face.png)
 
    弹出窗口会提示填写用户的详细信息。 键入人员姓名，然后单击 "检查" 按钮。

    ![向人员添加新的人脸](./media/customize-face-model/add-new-face2.png)
 
然后，你可以从文件资源管理器中进行选择，或者拖放人脸图像。 视频索引器将采用所有标准图像文件类型（例如： JPG、PNG 等）。

视频索引器应该能够在以后使用您向其中添加了此新表面的人员模型，检测到您索引的未来视频和您已经建立索引的当前视频。 在当前视频中，用户识别可能需要一些时间才能生效，因为这是一个批处理。


## <a name="rename-a-person-model"></a>重命名人员模型

您可以重命名帐户中的任何人员模型，包括默认人员模型。 即使重命名默认的人员模型，它仍将作为帐户中的默认人员模型。

1. 单击要重命名的人员模型旁边的 "列表" 菜单按钮。
2. 单击菜单中的 "**重命名**"。

    ![重命名人员](./media/customize-face-model/rename-person.png)
3. 单击模型的当前名称，然后键入新名称。

    ![重命名人员](./media/customize-face-model/rename-person2.png)
4. 单击要重命名的模型的复选按钮。

## <a name="delete-a-person-model"></a>删除人员模型

可以删除在帐户中创建的任何人员模型。 但是，您不能删除您的默认用户模型。

1. 单击菜单中的 "**删除**"。

    ![删除人员](./media/customize-face-model/delete-person.png)
    
    此时将显示一个弹出窗口，通知您此操作将删除该人员模型以及它所包含的所有用户和文件。 此操作不可撤消。 

    ![删除人员](./media/customize-face-model/delete-person2.png)
1. 如果确定，请再次单击 "删除"。

> [!NOTE]
> 使用此（现已删除）人员模型进行索引的现有视频将不支持更新视频中显示的面部名称。 仅在使用其他人员模式对其重新进行索引后，才能在这些视频中编辑面部的名称。 如果在未指定人员模型的情况下进行重新索引，将使用默认模型。 

## <a name="manage-existing-people-in-a-person-model"></a>管理人员模型中的现有人员

若要查看任何人员模型的内容，请单击人员模型名称旁边的箭头。
下拉箭头显示该特定人员模型中的所有人员。 如果单击每个用户旁边的列表菜单按钮，则会看到 "管理"、"重命名" 和 "删除" 选项。  

![向人员添加新的人脸](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>重命名人员

1. 若要重命名 Person 模型中的人员，请单击列表菜单按钮，然后从列表菜单中选择 "**重命名**"。
1. 单击联系人的当前名称，然后键入新名称。
1. 单击 "检查" 按钮，人员将重命名。

### <a name="delete-a-person"></a>删除人员

1. 若要从人员模型中删除人员，请单击 "列表" 菜单按钮，然后从 "列表" 菜单中选择 "**删除**"。
1. 弹出窗口会告诉您此操作将删除该人员并且此操作无法撤消。
1. 再次单击 "删除"，这将删除 Person 模型中的人员。

### <a name="manage-a-person"></a>管理人员

如果单击 "**管理**"，将看到此人员模型正在进行训练的所有人脸。 这些人脸是在使用此人员模型的视频中或从你手动上载的映像中出现的。 

可以通过单击 "添加图像" 向人员添加更多的人脸。

您可以使用 "管理" 窗格重命名该人员并从 "人员" 模型中删除该用户。

## <a name="use-a-person-model-to-index-a-video"></a>使用人员模型为视频编制索引

您可以使用人员模型在视频上传过程中通过分配人员模型来索引新视频。

若要在新视频上使用人员模型，请执行以下操作：

1. 单击页面顶部的 "**上传**" 按钮。

    ![上传](./media/customize-face-model/upload.png)
1. 将视频文件放在圆圈中或浏览文件。
1. 单击 "高级选项" 箭头。

    ![上传](./media/customize-face-model/upload2.png)
1. 单击下拉箭头，然后选择所创建的人员模型。

    ![上传](./media/customize-face-model/upload3.png)
1. 单击页面底部的 "上传" 选项，将使用人员模型为新视频编制索引。

如果在上传过程中未指定人员模型，则视频索引器将使用帐户中的默认人员模型为视频编制索引。

## <a name="use-a-person-model-to-reindex-a-video"></a>使用人员模型重新索引视频 

若要使用人员模型对集合中的视频重新编制索引，请在视频索引器主页上的 "帐户视频" 中，将鼠标悬停在要重新编制索引的视频的名称上。

你将看到用于编辑、删除和重新索引视频的选项。 

1. 单击该选项可重新索引视频。

    ![重新编制索引](./media/customize-face-model/reindex.png)

    你现在可以选择要为其重新编制视频索引的人员模型。
1. 单击下拉箭头，然后选择要使用的人员模型。 

    ![重新编制索引](./media/customize-face-model/reindex2.png)

1. 单击 "**重建索引**" 按钮，将使用用户型号重新编制索引视频。

你刚重新编制索引的视频中检测并识别的面部的任何新编辑都将保存在用于对视频重新建立索引的人员模型中。

## <a name="managing-people-in-your-videos"></a>管理视频中的人员

你可以管理检测到的人脸，还可以管理通过编辑和删除人脸索引的视频中识别的人。

删除人脸会从视频的见解中删除特定的人脸。

编辑人脸，重命名在视频中检测到并可能被识别的人脸。 编辑视频中的人脸时，该名称将作为人员条目保存在上传和索引期间分配给视频的人员模型中。

如果在上传过程中未将人员模型分配给视频，则会将编辑保存在帐户的默认人员模型中。

### <a name="edit-a-face"></a>编辑人脸


> [!NOTE]
> 如果人员模型有两个或多个具有相同名称的不同人员，你将无法在使用该人员模型的视频中标记该名称。 你只能在视频索引器的 "内容模型自定义" 页的 "人员" 选项卡中对共享该名称的人员进行更改。 出于此原因，建议你为人员模型中的每个人提供唯一的名称。

1. 浏览到视频索引器网站并登录。
1. 搜索要在帐户中查看并编辑的视频。
1. 若要编辑视频中的人脸，请前往 "见解" 选项卡，并单击窗口右上角的铅笔图标。

    ![编辑视频中的人脸](./media/customize-face-model/edit-face.png)
1. 单击检测到的任何人脸，将其名称从“未知 X”（或以前分配给该人脸的名称）更改为其他名称。 
1. 键入新名称后，单击新名称旁边的复选图标。 这将保存新名称，并在你的其他当前视频和你上传的未来视频中识别和命名此人脸的所有匹配项。 在其他当前视频中的人脸识别可能需要一些时间才能生效，因为这是一个批处理。

如果使用视频所使用的人员模型中的现有人员姓名来命名人脸，这会将此人的视频中检测到的人脸图像与模型中已经存在的内容合并在一起。 如果使用全新名称命名人脸，这会在使用视频的人员模型中创建一个新的 "用户" 条目。 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>删除人脸

若要删除视频中检测到的人脸，请前往 Insights 窗格，并单击窗格右上角的铅笔图标。 单击面部名称下面的 "删除" 选项。 这样就会从视频中删除该检测到的人脸。 在显示该人的其他视频中，还会检测该人的人脸，但在对其进行索引后，可以从这些视频中删除该人脸。 如果该用户已命名，则该人员还会继续存在于用于为删除该人脸的视频编制索引的人员模型中，除非您专门从 Person 模型中删除该人。

![删除视频中的人脸](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>后续步骤

[使用 API 自定义人员模型](customize-person-model-with-api.md)
