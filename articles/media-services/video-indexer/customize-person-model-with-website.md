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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499972"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>使用视频索引器网站自定义人员模型

视频索引器支持名人识别视频内容。 名人识别功能基于通常请求的数据源（如 IMDB、Wikipedia 和 LinkedIn 影响者排名）涵盖约一百万张人脸。 有关详细概述，请参阅[在视频索引器中自定义人员模型](customize-person-model-overview.md)。

可以使用视频索引器网站编辑视频中检测到的人脸，如本主题所述。 你还可以使用 API，如[使用 Api 自定义人员模型](customize-person-model-with-api.md)中所述。

## <a name="central-management-of-person-models-in-your-account"></a>帐户中人员模型的集中管理

1. 若要查看、编辑和删除帐户中的人员模型，请浏览到视频索引器网站并登录。

2. 在页面的右上角选择 "内容模型自定义" 按钮。

    ![内容模型自定义](./media/customize-face-model/content-model-customization.png)

3. 选择 "人员" 选项卡。

    你将在帐户中看到默认的 "人员" 模型。 默认人员模型包含你在创建索引期间未指定自定义人员模型的视频见解中可能已编辑或更改的任何人脸。

    如果创建了其他人员模型，则这些模型也将在此页上列出。

    ![内容模型自定义](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>创建新的人员模型

1. 选择 " **+ 添加模型**" 按钮。

    ![添加新人员模型](./media/customize-face-model/add-new-person.png)

2. 输入模型的名称，并选中该名称旁边的复选按钮。

    ![添加新人员模型](./media/customize-face-model/add-new-person2.png)

    您已经创建了一个新的人员模型。 你现在可以将人脸添加到新人员模型中。

3. 选择 "列表" 菜单按钮，然后选择 " **+ 添加人员**"。

    ![添加新人员模型](./media/customize-face-model/add-new-person3.png)

## <a name="add-a-new-person-to-a-person-model"></a>向人员模型添加新人员

> [!NOTE]
> 视频索引器允许您在人员模型中添加具有相同名称的多个用户。 但建议为模型中的每个人员提供唯一的名称，以实现可用性和清晰性。

1. 若要将新的人脸添加到人员模型，请选择要向其添加人脸的人员模型旁边的 "列表" 菜单按钮。

1. 从菜单中选择 " **+ 添加人员**"。

    ![向人员添加新的人脸](./media/customize-face-model/add-new-face.png)

    弹出窗口会提示填写用户的详细信息。 键入人员姓名，并选中 "检查" 按钮。

    ![向人员添加新的人脸](./media/customize-face-model/add-new-face2.png)

    然后，你可以从文件资源管理器中进行选择，或者拖放人脸图像。 视频索引器将采用所有标准图像文件类型（例如： JPG、PNG 等）。

    视频索引器可以使用您向其中添加了此新人脸的人员模型，在您为你建立索引的未来视频中检测此人的出现次数，并使用你已建立索引的当前视频。 在当前视频中，用户识别可能需要一些时间才能生效，因为这是一个批处理。

## <a name="rename-a-person-model"></a>重命名人员模型

您可以重命名帐户中的任何人员模型，包括默认人员模型。 即使重命名默认的人员模型，它仍将作为帐户中的默认人员模型。

1. 选择要重命名的人员模型旁边的 "列表" 菜单按钮。
2. 从菜单中选择“重命名”****。

    ![重命名人员模型](./media/customize-face-model/rename-person.png)

3. 选择模型的当前名称，然后键入新名称。

    ![重命名人员模型](./media/customize-face-model/rename-person2.png)

4. 选择要重命名的模型的复选按钮。

## <a name="delete-a-person-model"></a>删除人员模型

可以删除在帐户中创建的任何人员模型。 但是，您不能删除您的默认用户模型。

1. 从菜单中选择“删除”****。

    ![删除人员模型](./media/customize-face-model/delete-person.png)

    此时将显示一个弹出窗口，通知您此操作将删除该人员模型以及它所包含的所有用户和文件。 此操作无法撤消。

    ![删除人员模型](./media/customize-face-model/delete-person2.png)

1. 如果确定，请再次选择 "删除"。

> [!NOTE]
> 使用此（现已删除）人员模型进行索引的现有视频不支持更新出现在视频中的面部名称的功能。 仅在使用其他人员模式对其重新进行索引后，才能在这些视频中编辑面部的名称。 如果在未指定人员模型的情况下进行重新索引，将使用默认模型。

## <a name="manage-existing-people-in-a-person-model"></a>管理人员模型中的现有人员

若要查看任何人员模型的内容，请选择人员模型名称旁边的箭头。 下拉箭头显示该特定人员模型中的所有人员。 如果你选择每个用户旁边的列表菜单按钮，你将看到 "管理"、"重命名" 和 "删除" 选项。  

![向人员添加新的人脸](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>重命名人员

1. 若要重命名 Person 模型中的人员，请选择 "列表" 菜单按钮，然后从 "列表" 菜单中选择 "**重命名**"。
1. 选择用户的当前名称，然后键入新名称。
1. 选中 "检查" 按钮，人员将重命名。

### <a name="delete-a-person"></a>删除人员

1. 若要从人员模型中删除人员，请从列表菜单中选择 "列表" 菜单按钮，然后选择 "**删除**"。
1. 弹出窗口会告诉您此操作将删除该人员并且此操作无法撤消。
1. 再次选择 "**删除**"，这将删除 person 模型中的人员。

### <a name="manage-a-person"></a>管理人员

如果选择 "**管理**"，则会看到此人员模型正在进行训练的所有人脸。 这些人脸是在使用此人员模型的视频中或从手动上载的图像中出现的。

可以通过选择 "**添加图像**" 向人员添加更多的人脸。

您可以使用 "管理" 窗格重命名该人员并从 "人员" 模型中删除该用户。

## <a name="use-a-person-model-to-index-a-video"></a>使用人员模型为视频编制索引

您可以使用人员模型在视频上传过程中通过分配人员模型来索引新视频。

若要在新视频上使用人员模型，请执行以下步骤：

1. 选择页面顶部的 "**上传**" 按钮。

    ![上载人员模型](./media/customize-face-model/upload.png)

1. 将视频文件放在圆圈中或浏览文件。
1. 选择 "**高级选项**" 箭头。

    ![上载人员模型](./media/customize-face-model/upload2.png)

1. 选择下拉箭头，然后选择所创建的人员模型。

    ![上载人员模型](./media/customize-face-model/upload3.png)

1. 选择页面底部的 "**上传**" 选项，将使用人员模型为新视频编制索引。

如果在上传过程中未指定人员模型，则视频索引器将使用帐户中的默认人员模型为视频编制索引。

## <a name="use-a-person-model-to-reindex-a-video"></a>使用人员模型重新索引视频

若要使用人员模型对集合中的视频重新编制索引，请在视频索引器主页上的 "帐户视频" 中，将鼠标悬停在要重新编制索引的视频的名称上。

你将看到用于编辑、删除和重新索引视频的选项。

1. 选择该选项以重新索引视频。

    ![使用人员模型重新索引视频](./media/customize-face-model/reindex.png)

    你现在可以选择要为其重新编制视频索引的人员模型。
1. 选择下拉箭头，然后选择要使用的人员模型。

    ![使用人员模型重新索引视频](./media/customize-face-model/reindex2.png)

1. 选择 "**重建索引**" 按钮，您的视频将使用您的个人型号重新编制索引。

你刚重新编制索引的视频中检测并识别的面部的任何新编辑都将保存在用于对视频重新建立索引的人员模型中。

## <a name="managing-people-in-your-videos"></a>管理视频中的人员

您可以管理检测到的人脸，还可以通过编辑和删除面部来管理在您编制索引的视频中识别的人。

删除人脸会删除视频见解中的特定面。

编辑人脸会重命名在视频中检测到并可能被识别的人脸。 编辑视频中的人脸时，该名称将作为人员条目保存在上传和索引期间分配给视频的人员模型中。

如果在上传过程中未将人员模型分配给视频，则会将编辑保存在帐户的默认人员模型中。

### <a name="edit-a-face"></a>编辑人脸

> [!NOTE]
> 如果人员模型有两个或多个具有相同名称的不同人员，你将无法在使用该人员模型的视频中标记该名称。 你只能在视频索引器的 "内容模型自定义" 页的 "人员" 选项卡中对共享该名称的人员进行更改。 出于此原因，建议你为人员模型中的每个人提供唯一的名称。

1. 浏览到视频索引器网站并登录。
1. 搜索要在帐户中查看并编辑的视频。
1. 若要编辑视频中的人脸，请前往 "见解" 选项卡，然后选择窗口右上角的铅笔图标。

    ![编辑视频中的人脸](./media/customize-face-model/edit-face.png)

1. 选择任何检测到的人脸，并将其名称从 "Unknown #X" 更改（或之前分配给人脸的名称）。
1. 键入新名称后，选择新名称旁边的复选图标。 此操作将保存新名称，并在你的其他当前视频和你上传的未来视频中识别和命名此人脸的所有匹配项。 识别其他当前视频中的人脸可能需要一定的时间，因为这是一个批处理过程。

如果使用视频所使用的人员模型中的现有人员姓名来命名人脸，则此人视频中检测到的人脸图像将与模型中已存在的内容合并。 如果使用新名称命名人脸，则会在视频使用的人员模型中创建一个新的 Person 条目。

![编辑视频中的人脸](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>删除人脸

若要删除视频中检测到的人脸，请前往 Insights 窗格，并选择窗格右上角的铅笔图标。 选择面部名称下面的 "**删除**" 选项。 此操作将从视频中删除检测到的人脸。 在显示该人的其他视频中，还会检测该人的人脸，但你也可以从这些视频中删除该人脸并将其编入索引。

如果用户已命名为，则还会继续存在于人员模型中，该模型用于为你删除人脸的视频编制索引，除非你明确地从 Person 模型中删除该人。

![删除视频中的人脸](./media/customize-face-model/delete-face.png)

## <a name="next-steps"></a>后续步骤

[使用 API 自定义人员模型](customize-person-model-with-api.md)
