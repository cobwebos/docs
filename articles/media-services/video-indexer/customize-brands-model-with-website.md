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
ms.author: kumud
ms.openlocfilehash: 5bd88493324867dc957922a732506f5cfb8bbc20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361228"
---
# <a name="customize-a-brands-model-with-the-video-indexer-website"></a>使用视频索引器网站自定义品牌模型

在对视频和音频内容进行索引和重新索引的过程中，可以使用视频索引器从语音和视觉文本中检测品牌。 品牌检测功能可以确定内容中是否提到必应品牌数据库建议的产品、服务和公司。 例如，如果 Microsoft 是在视频或音频内容中提到的，或者如果显示在视频的视频文本中，则视频索引器会将其作为内容中的品牌来检测。

自定义品牌模型允许：

- 如果希望视频索引器检测来自必应品牌数据库的品牌，请选择。
- 如果希望视频索引器排除检测到某些品牌 (实质上是创建品牌) 的拒绝列表，请选择 "确定"。
- 如果希望视频索引器包含的品牌应为可能不在 Bing 品牌数据库中的模型的一部分， (实质上是创建品牌) 的接受列表。

有关详细概述，请参阅此 [概述](customize-brands-model-overview.md)。

可以使用视频索引器网站来创建、使用和编辑视频中检测到的自定义品牌模型，如本主题所述。 也可使用 API，如[使用 API 自定义品牌模型](customize-brands-model-with-api.md)中所述。

## <a name="edit-brands-model-settings"></a>编辑品牌模型设置

可以选择设置是否需要检测必应品牌数据库中的品牌。 若要设置此选项，需要编辑品牌模型的设置。 执行以下步骤:

1. 请参阅 [视频索引器](https://www.videoindexer.ai/) 网站并登录。
2. 若要在帐户中自定义模型，请在页面的右上角选择 " **内容模型自定义** " 按钮。

   ![自定义视频索引器中的内容模型](./media/content-model-customization/content-model-customization.png)

3. 若要编辑品牌，请选择“品牌”选项卡。****

    ![屏幕截图显示 "内容模型自定义" 对话框的 "品牌" 选项卡。](./media/customize-brand-model/customize-brand-model.png)

4. 如果希望视频索引器检测 Bing 建议的品牌，请选中 " **显示按 bing 推荐的品牌** " 选项，如果不是，则将该选项保持未选中状态。

## <a name="include-brands-in-the-model"></a>在模型中包括品牌

" **包含品牌** " 部分表示你希望视频索引器检测的自定义品牌，即使它们不是必应的。  

### <a name="add-a-brand-to-include-list"></a>将品牌添加到包含列表

1. 选择 " **+ 添加品牌**"。

    ![屏幕截图显示 "包含品牌" 对话框，你可以在其中添加品牌。](./media/customize-brand-model/add-brand.png)

    提供名称（必填）、类别（可选）、说明（可选）和引用 URL（可选）。
    类别字段用于对品牌进行标记。 在使用视频索引器 API 时，此字段显示为品牌的标记。** 例如，品牌“Azure”可以标记或归类为“云”。

    "引用 URL" 字段可以是品牌 (的任何参考网站，如) 的维基百科页面的链接。

2. 选择 " **添加品牌** "，你会看到已将品牌添加到 " **包含品牌** " 列表。

### <a name="edit-a-brand-on-the-include-list"></a>编辑 "包含列表" 上的品牌

1. 选择要编辑的品牌旁边的铅笔图标。

    可以更新品牌的类别、说明或引用 URL。 不能更改品牌的名称，因为品牌的名称是唯一的。 如果需要更改品牌名称，请删除整个品牌（参见下一部分），然后使用新名称创建一个新品牌。

2. 选择 " **更新** " 按钮以用新信息更新品牌。

### <a name="delete-a-brand-on-the-include-list"></a>删除包含列表中的品牌

1. 选择要删除的品牌旁边的 "垃圾桶" 图标。
2. 选择 " **删除** "，该品牌将不再出现在 " *包含品牌* " 列表中。

## <a name="exclude-brands-from-the-model"></a>从模型中排除品牌

" **排除品牌** " 部分表示不希望视频索引器检测的品牌。

### <a name="add-a-brand-to-exclude-list"></a>向排除列表添加品牌

1. 选择 " **+ 添加品牌"。**

    提供名称（必填）、类别（可选）。

2. 选择 " **添加品牌** "，你会看到已将品牌添加到 " *排除品牌* " 列表中。

### <a name="edit-a-brand-on-the-exclude-list"></a>编辑排除列表上的品牌

1. 选择要编辑的品牌旁边的铅笔图标。

    只能更新品牌的类别。 不能更改品牌的名称，因为品牌的名称是唯一的。 如果需要更改品牌名称，请删除整个品牌（参见下一部分），然后使用新名称创建一个新品牌。

2. 选择 " **更新** " 按钮以用新信息更新品牌。

### <a name="delete-a-brand-on-the-exclude-list"></a>删除排除列表上的品牌

1. 选择要删除的品牌旁边的 "垃圾桶" 图标。
2. 选择 " **删除** "，该品牌将不再显示在 " *排除品牌* " 列表中。

## <a name="next-steps"></a>后续步骤

[使用 API 自定义品牌模型](customize-brands-model-with-api.md)
