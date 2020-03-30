---
title: 使用视频索引器网站自定义品牌模型
titleSuffix: Azure Media Services
description: 了解如何使用视频索引器网站自定义品牌模型。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 81df3897dff13823e4b97e10bc91d3a22b0e1b0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128045"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>使用视频索引器网站自定义品牌模型

在对视频和音频内容进行索引和重新索引的过程中，可以使用视频索引器从语音和视觉文本中检测品牌。 品牌检测功能可以确定内容中是否提到必应品牌数据库建议的产品、服务和公司。 例如，如果视频或音频内容中提到 Microsoft，或者在视频中以可视文本显示，视频索引器会将其检测为内容中的品牌。

自定义品牌模型允许您：

- 是否希望视频索引器从必应品牌数据库中检测品牌，请选择该选择。
- 如果希望视频索引器排除检测到某些品牌（实质上是创建品牌拒绝列表），请选择该列表。
- 如果您希望视频索引器包含应该成为您的模型一部分的品牌（基本上创建接受品牌列表），请选择该品牌。

有关详细概述，请参阅此[概述](customize-brands-model-overview.md)。

可以使用视频索引器网站来创建、使用和编辑视频中检测到的自定义品牌模型，如本主题所述。 也可使用 API，如[使用 API 自定义品牌模型](customize-brands-model-with-api.md)中所述。

## <a name="edit-brands-model-settings"></a>编辑品牌模型设置

可以选择设置是否需要检测必应品牌数据库中的品牌。 要设置此选项，您需要编辑品牌模型的设置。 执行以下步骤:

1. 转到[视频索引器](https://www.videoindexer.ai/)网站并登录。
2. 要自定义帐户中的模型，请选择页面右上角**的内容模型自定义**按钮。

   ![在视频索引器中自定义内容模型](./media/content-model-customization/content-model-customization.png)

3. 若要编辑品牌，请选择“品牌”选项卡。****

    ![自定义视频索引器中的品牌模型](./media/customize-brand-model/customize-brand-model.png)

4. 如果您希望视频索引器检测必应建议的品牌，请检查**必应建议的"显示"品牌**，如果您不希望选中该选项，则不选中该选项。

## <a name="include-brands-in-the-model"></a>在模型中包括品牌

**"包括品牌**"部分表示您希望视频索引器检测的自定义品牌，即使必应未建议这些品牌也是如此。  

### <a name="add-a-brand-to-include-list"></a>添加包含列表的品牌

1. 选择 **= 添加品牌**。

    ![自定义视频索引器中的品牌模型](./media/customize-brand-model/add-brand.png)

    提供名称（必填）、类别（可选）、说明（可选）和引用 URL（可选）。
    类别字段用于对品牌进行标记。 在使用视频索引器 API 时，此字段显示为品牌的标记。** 例如，品牌“Azure”可以标记或归类为“云”。

    参考 URL 字段可以是该品牌的任何参考网站（如指向其维基百科页面的链接）。

2. 选择 **"添加品牌**"，您将看到该品牌已添加到"**包括品牌**"列表中。

### <a name="edit-a-brand-on-the-include-list"></a>编辑包含列表中的品牌

1. 选择要编辑的品牌旁边的铅笔图标。

    可以更新品牌的类别、说明或引用 URL。 不能更改品牌的名称，因为品牌的名称是唯一的。 如果需要更改品牌名称，请删除整个品牌（参见下一部分），然后使用新名称创建一个新品牌。

2. 选择 **"更新**"按钮以使用新信息更新品牌。

### <a name="delete-a-brand-on-the-include-list"></a>删除包含列表中的品牌

1. 选择要删除的品牌旁边的垃圾回收图标。
2. 选择 **"删除**"，品牌将不再显示在 *"包括"品牌*列表中。

## <a name="exclude-brands-from-the-model"></a>从模型中排除品牌

**"排除品牌**"部分表示您不希望视频索引器检测到的品牌。

### <a name="add-a-brand-to-exclude-list"></a>添加要排除列表的品牌

1. 选择 **= 添加品牌。**

    提供名称（必填）、类别（可选）。

2. 选择 **"添加品牌**"，您将看到该品牌已添加到 *"排除品牌*"列表中。

### <a name="edit-a-brand-on-the-exclude-list"></a>编辑排除列表中的品牌

1. 选择要编辑的品牌旁边的铅笔图标。

    只能更新品牌的类别。 不能更改品牌的名称，因为品牌的名称是唯一的。 如果需要更改品牌名称，请删除整个品牌（参见下一部分），然后使用新名称创建一个新品牌。

2. 选择 **"更新**"按钮以使用新信息更新品牌。

### <a name="delete-a-brand-on-the-exclude-list"></a>删除排除列表中的品牌

1. 选择要删除的品牌旁边的垃圾回收图标。
2. 选择 **"删除**"，该品牌将不再显示在 *"排除"品牌*列表中。

## <a name="next-steps"></a>后续步骤

[使用 API 自定义品牌模型](customize-brands-model-with-api.md)
