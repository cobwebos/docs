---
title: 使用视频索引器 API 自定义人员模型
titleSuffix: Azure Media Services
description: 了解如何使用视频索引器 API 自定义人员模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 09366dea1a0d77052b6f99e9f5ab52c270e341b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87047009"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>使用视频索引器 API 自定义人员模型

视频索引器支持视频内容的人脸检测和名人识别。 名人识别功能涉及到基于常见请求的数据源（例如 IMDB、维基百科和热门 LinkedIn 类）的大约1000000面。 检测到名人识别功能无法识别的人脸，但仍未命名。 将视频上传到视频索引器并返回结果后，可以返回并命名无法识别的人脸。 使用名称标记人脸后，人脸和名称将添加到帐户的人员模型中。 然后，视频索引器会在将来的视频和过去的视频中识别出此人脸。

可以使用视频索引器 API 编辑视频中检测到的人脸，如本主题所述。 也可使用视频索引器网站，如[使用视频索引器网站自定义人员模型](customize-person-model-with-api.md)中所述。

## <a name="managing-multiple-person-models"></a>管理多个人员模型

视频索引器支持每个帐户使用多个人员模型。 目前只能通过视频索引器 API 使用此功能。

如果帐户需要面对不同的用例方案，则可能需要为每个帐户创建多个人员模型。 例如，如果内容与体育相关，则可以为每个运动创建单独的人员模型 (橄榄球、篮球、足球等) 。

创建模型后，即可在上传/索引或重新索引视频时提供特定人员模型的模型 ID，这样就可以使用该模型。 为视频训练新的人脸时，会更新与该视频相关联的具体自定义模型。

每个帐户的限制为 50 个人员模型。 如果不需要多人员模型支持，请不要在上传/索引或重新创建索引时向视频分配人员模型 ID。 在这种情况下，视频索引器会使用帐户中的默认自定义人员模型。

## <a name="create-a-new-person-model"></a>创建新的人员模型

若要在指定的帐户中创建新的人员模型，请使用 [创建人员模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?) API。

响应提供刚刚按照以下示例的格式创建的人员模型的名称和生成的模型 ID。

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

然后，在[上传视频以对视频进行索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)或重新索引时，使用[reindexing a video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) **personModelId**参数的**id**值。

## <a name="delete-a-person-model"></a>删除人员模型

若要从指定的帐户中删除自定义人员模型，请使用 [删除人员模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?) API。

成功删除人员模型以后，曾经使用已删除模型的当前视频的索引会保持不变，直至你重新对其进行索引。 重建索引后，在已删除模型中命名的人不会被当前视频中使用该模型编制索引的视频索引器识别，但仍会检测到面部。 使用已删除模型进行索引的当前视频现在会使用帐户的默认人员模型。 如果已删除模型中的人脸也在帐户的默认模型中命名，则可继续识别视频中的这些人脸。

成功删除人员模型后，不会返回任何内容。

## <a name="get-all-person-models"></a>获取所有人员模型

若要获取指定帐户中的所有人员模型，请使用 [获取人员模型](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?) API。

响应提供帐户中所有人员模型的列表 (包括指定帐户中的默认人员模型) 及其名称和 Id 都遵循以下示例格式。

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

在 `id` `personModelId` [上传视频以对视频进行索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)或重新索引时[reindexing a video](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)，可以通过使用参数的 Person 模型值选择要用于视频的模型。

## <a name="update-a-face"></a>更新人脸

此命令允许将视频 ID 和人脸 ID 配合使用，以便通过名称来更新视频中的人脸。 然后，此操作将更新在上传/索引或重新索引时与视频关联的人员模型。 如果未分配人员模型，则会更新帐户的默认人员模型。

然后，系统会识别共享同一人员模型的其他当前视频中出现的相同面。 识别其他当前视频中的人脸可能需要一定的时间，因为这是一个批处理过程。

可以使用新的名称来更新通过视频索引器识别为名人的人脸。 提供的新名称优先于内置的名人识别功能。

若要更新图符，请使用 [更新视频人脸](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?) API。

名称对于人员模型是唯一的，因此，如果您在同一人员模型中为同一参数值提供两个不同的人脸，则 " `name` 视频索引器" 将以相同的方式查看人脸并在对视频重新建立索引后将其聚合起来。

## <a name="next-steps"></a>后续步骤

[使用视频索引器网站自定义人员模型](customize-person-model-with-website.md)
