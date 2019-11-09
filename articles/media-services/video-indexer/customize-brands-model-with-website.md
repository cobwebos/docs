---
title: 使用视频索引器网站自定义品牌模型 - Azure
titleSuffix: Azure Media Services
description: 本文介绍如何使用视频索引器网站自定义品牌模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: f4b2f7a8b43c1723beb0dd8da82814f74cd7b135
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838348"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>使用视频索引器网站自定义品牌模型

在对视频和音频内容进行索引和重新索引的过程中，可以使用视频索引器从语音和视觉文本中检测品牌。 品牌检测功能可以确定内容中是否提到必应品牌数据库建议的产品、服务和公司。 例如，如果在某个视频或音频内容中提到了 Microsoft，或者 Microsoft 出现在视频的视觉文本中，则视频索引器会将其作为内容中的品牌检测到。 使用自定义品牌模型时，可以选择是否让视频索引器检测必应品牌数据库中的品牌；可以排除某些品牌，不让其检测到（实质上是创建一个品牌的方块列表）；还可以添加一些品牌，这些品牌应该在你的模型中，但可能不在必应的品牌数据库中（实质上是创建一个品牌的允许列表）。

如需详细的概述，请参阅[概述](customize-brands-model-overview.md)。

可以使用视频索引器网站来创建、使用和编辑视频中检测到的自定义品牌模型，如本主题所述。 也可使用 API，如[使用 API 自定义品牌模型](customize-brands-model-with-api.md)中所述。

## <a name="edit-the-settings-of-the-brands-model"></a>编辑品牌模型的设置  

可以选择设置是否需要检测必应品牌数据库中的品牌。 为此，需编辑品牌模型的设置。

1. 浏览到[视频索引器](https://www.videoindexer.ai/)网站并登录。
2. 若要在帐户中自定义某个模型，请单击页面右上角的“内容模型自定义”按钮。
 
   ![自定义内容模型](./media/content-model-customization/content-model-customization.png) 
3. 若要编辑品牌，请选择“品牌”选项卡。

    ![自定义品牌模型](./media/customize-brand-model/customize-brand-model.png)
4. 如果需要让视频索引器包括必应建议的品牌，请勾选“显示必应建议的品牌”选项。 如果不希望视频索引器在内容中检测必应建议的品牌，请让此选项保持未选中状态。 

## <a name="include-brands-in-the-model"></a>在模型中包括品牌

“包括品牌”部分代表需要让视频索引器检测的自定义品牌，即使这些品牌不是必应建议的。  

### <a name="add-a-brand"></a>添加品牌

1. 单击“+ 添加品牌”。

    ![自定义品牌模型](./media/customize-brand-model/add-brand.png)

    提供名称（必填）、类别（可选）、说明（可选）和引用 URL（可选）。
    类别字段用于对品牌进行标记。 在使用视频索引器 API 时，此字段显示为品牌的标记。 例如，品牌“Azure”可以标记或归类为“云”。

    引用 URL 字段可以是品牌的任何引用网站，例如其维基百科页的链接。
2. 单击“添加品牌”，此时会看到品牌已添加到“包括品牌”列表中。

### <a name="edit-a-brand"></a>编辑品牌

1. 单击要编辑的品牌旁边的铅笔图标。

    可以更新品牌的类别、说明或引用 URL。 不能更改品牌的名称，因为品牌的名称是唯一的。 如果需要更改品牌名称，请删除整个品牌（参见下一部分），然后使用新名称创建一个新品牌。
2. 单击“更新”按钮，使用新信息更新品牌。

### <a name="delete-a-brand"></a>删除品牌

1. 单击要删除的品牌旁边的回收站图标。
2. 单击“删除”，品牌将不再出现在“包括品牌”列表中。

## <a name="exclude-brands-from-the-model"></a>从模型中排除品牌

“排除品牌”部分代表不希望让视频索引器检测的品牌。

### <a name="add-a-brand"></a>添加品牌

1. 单击“+ 添加品牌”。

    提供名称（必填）、类别（可选）。
2. 单击“添加品牌”，此时会看到品牌已添加到“排除品牌”列表中。

### <a name="edit-a-brand"></a>编辑品牌

1. 单击要编辑的品牌旁边的铅笔图标。

    只能更新品牌的类别。 不能更改品牌的名称，因为品牌的名称是唯一的。 如果需要更改品牌名称，请删除整个品牌（参见下一部分），然后使用新名称创建一个新品牌。
2. 单击“更新”按钮，使用新信息更新品牌。

### <a name="delete-a-brand"></a>删除品牌

1. 单击要删除的品牌旁边的回收站图标。
2. 单击“删除”，品牌将不再出现在“排除品牌”列表中。

## <a name="next-steps"></a>后续步骤

[使用 API 自定义品牌模型](customize-brands-model-with-api.md)
