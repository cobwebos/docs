---
title: 使用视频索引器网站自定义人员模型 - Azure
titlesuffix: Azure Media Services
description: 本文介绍如何使用视频索引器网站自定义人员模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/03/2018
ms.author: anzaman
ms.openlocfilehash: 321db75b1a286e7de1e38ed3b382ee7dc9fe5902
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283743"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>使用视频索引器网站自定义人员模型

视频索引器支持视频内容的人脸检测和名人识别。 名人识别功能基于通常请求的数据源（如 IMDB、Wikipedia 和 LinkedIn 影响者排名）涵盖约一百万张人脸。 会检测到名人识别功能未识别的人脸；但是，这些人脸会保留未命名状态。 将视频上传到视频索引器并获得结果后，即可返回并命名未识别的人脸。 使用名称标记人脸后，人脸和名称将添加到帐户的人员模型中。 然后，视频索引器会在将来的视频和过去的视频中识别出此人脸。

可以使用视频索引器网站编辑视频中检测到的人脸，如本主题所述。 也可使用 API，如[使用 API 自定义人员模型](customize-person-model-with-api.md)中所述。

## <a name="edit-a-face"></a>编辑人脸

> [!NOTE]
> 在人员模型中，名称是唯一的，因此如果为两个不同的人脸提供相同的名称，视频索引器会将这两个人脸视为同一人，并会在完成视频的重新索引后将其聚合在一起。 如果发现视频索引器检测到同一人脸多次出现，请为其提供同一名称，并重新编制视频的索引。
> 可以使用新的名称来更新通过视频索引器识别为名人的人脸。 提供的新名称优先于内置的名人识别功能。

1. 浏览到[视频索引器](https://www.videoindexer.ai/)网站并登录。
2. 搜索要在帐户中查看并编辑的视频。
3. 若要编辑视频中的人脸，请转到“见解”选项卡，单击窗口右上角的铅笔图标。

    ![自定义人员模型](./media/customize-face-model/customize-face-model.png)

4. 单击检测到的任何人脸，将其名称从“未知 X”（或以前分配给该人脸的名称）更改为其他名称。
5. 键入新名称后，单击新名称旁边的复选图标。 这样就会保存新名称，以及识别并命名在其他当前视频和上传的未来视频中出现的此人脸。 识别其他当前视频中的人脸可能需要一定的时间，因为这是一个批处理过程。 

    ![保存更新](./media/customize-face-model/save-update.png)

## <a name="delete-a-face"></a>删除人脸

若要删除视频中检测到的人脸，请转到“见解”窗格，单击窗格右上角的铅笔图标。 单击人脸名称下的“删除”选项。 这样就会从视频中删除该检测到的人脸。 此人脸仍会在出现此人脸的其他视频中检测到，但是你可以在完成人脸索引后从这些视频中删除此人脸。 如果在删除此人脸之前已将其命名，则此人脸仍会存在于帐户的人员模型中。

## <a name="next-steps"></a>后续步骤

[使用 API 自定义人员模型](customize-person-model-with-api.md)
