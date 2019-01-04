---
title: 预生成的实体
titleSuffix: Azure Cognitive Services
description: LUIS 包括一组预构建实体，用于识别常见类型的信息，如日期、时间、数字、度量和货币。 预构建实体支持因 LUIS 应用的区域性而异。
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/05/2018
ms.author: diberry
ms.openlocfilehash: 9e0d1ae39431ca75b43680981802b82f16703d4d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103925"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>用于识别常见数据类型的预生成实体

LUIS 包括一组预生成实体，用于识别常见类型的信息，如日期、时间、数字、度量和货币。 

## <a name="add-a-prebuilt-entity"></a>添加预构建实体

1. 单击“我的应用”页上的应用名称打开应用，然后单击左侧的“实体”。 

1. 在“实体”页上，单击“添加预构建实体”。

1. 在“添加预构建实体”对话框中，选择 datetimeV2 预构建实体。 

    ![“添加预构建实体”对话框](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. 选择“完成”。

## <a name="publish-the-app"></a>发布应用

查看预构建实体的值的最简单方法是从已发布的终结点进行查询。 

1. 在顶部工具栏中，选择“发布”。 发布到“生产”。 

1. 当出现绿色的成功通知后，选择“参阅终结点列表”链接来查看终结点。

1. 选择一个终结点。 将会在新的浏览器选项卡中打开该终结点。 使浏览器选项卡保持打开状态，并继续执行“测试”部分。

## <a name="test"></a>测试
添加实体后，你不需要训练应用。 

通过为 **q** 参数添加值在终结点上测试新意向。 使用下表为 **q** 提供建议的话语：

|测试话语|实体值|
|--|:--|
|预订明天的航班|2018-10-19|
|取消在 3 月 3 日的约会|LUIS 返回了已过去的最近的 3 月 3 日 (2018-03-03) 和将来的 3 月 3 日 (2019-03-03)，因为话语没有指定年份。|
|安排在上午 10 点开会|10:00:00|

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>标记包含预生成实体令牌的实体
 如果要将 `HH-1234` 等文本标记为自定义实体并将[预生成数字](luis-reference-prebuilt-number.md)添加到模型，则无法在 LUIS 门户中标记自定义实体。 可以使用 API 进行标记。 

 若要标记此类型的令牌（已使用预生成实体标记其中一部分），请从 LUIS 应用中删除预生成实体。 无需训练该应用。 然后，使用你自己的自定义实体标记令牌。 再将预生成实体添加回 LUIS 应用。

 有关其他示例，请将话语视为类首选项的列表：`I want first year spanish, second year calculus, and fourth year english lit.` 如果 LUIS 应用已添加预生成序号，则将使用序号标记 `first`、`second` 和 `fourth`。 如果要捕获序号和类，则可以创建复合实体并围绕预生成序号和类名称的自定义实体进行包装。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [预构建实体参考](./luis-reference-prebuilt-entities.md)
