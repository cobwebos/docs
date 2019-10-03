---
title: 使用视频索引器网站自定义人员模型 - Azure
titlesuffix: Azure Media Services
description: 本文介绍如何使用视频索引器网站自定义人员模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 37e556ca458a5b0a171664e089d39cfb448d5f96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65800116"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>使用视频索引器网站自定义人员模型

视频索引器支持的视频内容的名人识别。 名人识别功能基于通常请求的数据源（如 IMDB、Wikipedia 和 LinkedIn 影响者排名）涵盖约一百万张人脸。 有关详细的概述，请参阅[自定义视频索引器中的人员模型](customize-person-model-overview.md)。

可以使用视频索引器网站编辑视频中检测到的人脸，如本主题所述。 也可以使用 API，如中所述[自定义使用 Api 的人模型](customize-person-model-with-api.md)。

## <a name="central-management-of-person-models-in-your-account"></a>在你的帐户的人员模型的集中管理

1. 若要查看、 编辑和删除你的帐户中的人员模型中，浏览到视频索引器网站和登录。
2. 单击页面右上角中的内容模型自定义按钮。

    ![内容模型自定义](./media/customize-face-model/content-model-customization.png)
3. 选择人员选项卡。

    你的帐户中，会看到默认个人模型。 默认个人模型包含可能已编辑或更改为其未指定自定义的个人模型在索引期间对视频的见解中的任何人脸。 

    如果您创建其他人模型，它们将在这个页面上列出。 

    ![内容模型自定义](./media/customize-face-model/content-model-customization-people-tab.png)

## <a name="create-a-new-person-model"></a>创建新的人员模型

1. 单击 **+ 添加模型**按钮。

    ![添加新人员](./media/customize-face-model/add-new-person.png)
2. 输入模型的名称，然后单击名称旁边的复选按钮。

    ![添加新人员](./media/customize-face-model/add-new-person2.png)

    您已创建新的个人模型。 你现在可以向新的个人模型添加人脸。
3. 单击列表菜单按钮，然后选择 **+ 添加人员**。

    ![添加新人员](./media/customize-face-model/add-new-person3.png)
    
## <a name="add-a-new-person-to-a-person-model"></a>将新的用户添加到个人模型

> [!NOTE]
> 视频索引器，可在个人模型中添加具有相同名称的多个人员。 但是，建议，您为 unque 的名称的每个人在模型中为了实现可用性和清晰性。

1. 若要将新的人脸添加到个人模型中，单击你想要添加到人脸的人员模型旁边的列表菜单按钮。
1. 单击 **+ 添加人员**菜单中。

    ![将新的人脸添加到人员](./media/customize-face-model/add-new-face.png)
 
    一个弹出窗口，将提示你填写联系人的详细信息。 键入联系人的名称，然后单击检查按钮。

    ![将新的人脸添加到人员](./media/customize-face-model/add-new-face2.png)
 
然后可以从在文件资源管理器中选择或拖动拖放的人脸的人脸图像。 视频索引器将采用所有标准图像文件类型 (例如：JPG、 PNG、 和的详细信息）。

视频索引器应能够未来的视频中检测此人的匹配项，则索引和你有已编制索引，使用个人模型添加到此新人脸最新视频。 中最新视频的人员识别可能需要一些时间才会生效，因为这是一个批处理过程。


## <a name="rename-a-person-model"></a>重命名个人模型

在你的帐户包括默认个人模型中，可以重命名任何个人模型。 即使您重命名默认人员模型，它将仍作为你的帐户中的默认用户模型。

1. 单击你想要重命名个人模型旁边的列表菜单按钮。
2. 单击**重命名**菜单中。

    ![重命名一个人](./media/customize-face-model/rename-person.png)
3. 单击该模型的当前名称，并键入新名称。

    ![重命名一个人](./media/customize-face-model/rename-person2.png)
4. 单击检查按钮为您要重命名的模型。

## <a name="delete-a-person-model"></a>删除人员模型

您可以删除你的帐户中创建任何个人模型。 但是，不能删除默认人员模型。

1. 单击**删除**菜单中。

    ![删除用户](./media/customize-face-model/delete-person.png)
    
    一个弹出窗口将显示并通知您此操作将删除个人模型及其所有的人员和它所包含的文件。 此操作不可撤消。 

    ![删除用户](./media/customize-face-model/delete-person2.png)
1. 如果确定，请再次单击删除。

> [!NOTE]
> 使用此模型中 （现在已删除） 人员编制索引的现有视频将不支持的功能，以便更新在视频中出现的人脸的名称。 你将能够仅后重新使用另一个个人模型进行索引编辑这些视频中的人脸的名称。 如果您重新索引而无需指定个人模型，将使用默认模型。 

## <a name="manage-existing-people-in-a-person-model"></a>管理人员模型中的现有用户

若要查看的人员模型中的任何内容，请单击个人模型名称旁边的箭头。
下拉列表显示了您的所有人访问该特定人员模型中。 如果单击每个用户旁边的列表菜单按钮时，你将看到管理、 重命名和删除选项。  

![将新的人脸添加到人员](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>重命名一个人

1. 若要重命名个人模型中的用户，请单击列表菜单按钮，然后选择**重命名**列表菜单中。
1. 单击相关人和键入新名称的当前名称。
1. 单击检查按钮，并将重命名该人员。

### <a name="delete-a-person"></a>删除用户

1. 若要从用户模型中删除用户，单击列表菜单按钮，然后选择**删除**列表菜单中。
1. 弹出消息告诉您此操作将删除用户，此操作无法撤消。
1. 再次单击删除，这将从个人模型中删除用户。

### <a name="manage-a-person"></a>管理人员

如果单击**管理**，请参阅从定型此个人模型的所有人脸。 这些人脸来自此人使用此模型中人员的视频中的匹配项或从手动上传的图像。 

通过单击添加映像，可以将更多的人脸添加到 person。

若要重命名该人员并从个人模型中删除用户，可以使用管理窗格。

## <a name="use-a-person-model-to-index-a-video"></a>使用个人模型编制索引视频

个人模型可用于为新视频通过视频上载过程中分配个人模型编制索引。

若要使用新的视频的人员模型，请执行以下操作：

1. 单击**上传**页面顶部的按钮。

    ![上载](./media/customize-face-model/upload.png)
1. 圆周中删除视频文件或浏览你的文件。
1. 单击高级的选项箭头。

    ![上载](./media/customize-face-model/upload2.png)
1. 单击下拉列表并选择你创建的 Person 模型。

    ![上载](./media/customize-face-model/upload3.png)
1. 单击页面底部中的上传选项，并且将使用的人员的模型进行索引新视频。

如果在上传期间未指定个人模型，视频索引器将索引默认个人模型使用您的帐户中的视频。

## <a name="use-a-person-model-to-reindex-a-video"></a>使用个人模型重新编制索引视频 

若要使用个人模型重新编制索引在集合中的视频，转到帐户视频视频索引器的主页上并将鼠标悬停在想要重新索引视频的名称。

看到用于编辑、 删除和重新索引视频的选项。 

1. 单击以重新编制索引视频的选项。

    ![重新编制索引](./media/customize-face-model/reindex.png)

    现在，您可以选择个人模型重新编制索引的视频。
1. 单击下拉列表并选择你想要使用个人模型。 

    ![重新编制索引](./media/customize-face-model/reindex2.png)

1. 单击**Reindex**按钮，然后你的视频将重新编制索引使用的人员的模型。

对人脸检测和识别您只需重新编制索引的视频中所做的任何新编辑将保存在用于重新索引视频的人员模型。

## <a name="managing-people-in-your-videos"></a>在视频中的管理人员

你可以管理的人脸检测到的和已识别视频中，索引通过编辑和删除人脸的人员。

人脸，删除特定的人脸从视频的见解。

编辑人脸，重命名的是检测到且可能在你的视频中识别人脸。 编辑时人脸在视频中，该名称将保存为个人模型上传和编制索引过程中分配到视频中的人员项。

如果您没有分配个人模型与视频上传期间，您的编辑将保存在你的帐户的默认个人模型。

### <a name="edit-a-face"></a>编辑人脸


> [!NOTE]
> 如果个人模型具有两个或多个不同的人有相同的名称，将无法标记该名称中使用该人员模型的视频。 您将只能对共享视频索引器中的内容模型自定义页的人员选项卡中该名称的人进行更改。 出于此原因，建议您个人的模型中为唯一的名称提供给每个人。

1. 浏览到视频索引器网站并登录。
1. 搜索要在帐户中查看并编辑的视频。
1. 若要编辑人脸在视频中的，转到见解选项卡，并单击窗口右上角的铅笔图标。

    ![编辑视频中的人脸](./media/customize-face-model/edit-face.png)
1. 单击检测到的任何人脸，将其名称从“未知 X”（或以前分配给该人脸的名称）更改为其他名称。 
1. 键入新名称后，单击新名称旁边的复选图标。 这会保存新的名称和识别并命名此人脸在其他最新视频和将来你上传的视频中的所有匹配项。 在其他当前视频人脸识别可能需要一些时间才会生效，因为这是一个批处理过程。

如果使用视频的人员模型中的现有用户的名称与名称人脸，这会合并本视频中的内容模型中已存在具有该人员的人脸检测到的映像。 如果具有全新名称名称人脸，这将使用视频的人员模型中创建新用户条目。 

    ![Edit a face in your video](./media/customize-face-model/edit-face2.png)

### <a name="delete-a-face"></a>删除人脸

若要删除在视频中的检测到人脸，转到见解窗格中，并单击在上窗格右上角的铅笔图标。 单击人脸的名称下的删除选项。 这样就会从视频中删除该检测到的人脸。 仍在它出现的其他视频中检测到的人员的人脸，但它们已建立索引后，可以从这些视频也删除人脸。 人员，如果有名称，将还继续存在于用于编制索引从中删除人脸除非专门从个人模型中删除该人员的视频的人员模型。

![删除在视频中的人脸](./media/customize-face-model/delete-face.png)


## <a name="next-steps"></a>后续步骤

[使用 API 自定义人员模型](customize-person-model-with-api.md)
