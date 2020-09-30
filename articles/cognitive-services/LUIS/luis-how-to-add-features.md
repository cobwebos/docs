---
title: 使用特征改进 LUIS 字词列表
titleSuffix: Azure Cognitive Services
description: 使用语言理解 (LUIS) 添加应用功能，可以改进对类别和模式的意向和实体的检测或预测
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 33435d2367b92f312d41f0e32fdbb3a93d59a9fd
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540874"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>使用特征增强字词列表的信号

可以将功能添加到 LUIS 应用以提高其准确性。 特征通过提供某些字词和短语是应用域词汇表的一部分的提示来帮助 LUIS。

查看[概念](luis-concept-feature.md)了解何时以及为何使用特征。

## <a name="add-phrase-list-as-a-feature"></a>添加短语列表作为特征

1. 登录到 [LUIS 门户](https://www.luis.ai)，选择“订阅”和“创作资源”以查看分配给该创作资源的应用。
1. 在“我的应用”页上选择应用名称以打开应用。
1. 选择“生成”，然后在应用的左面板中选择“特征” 。

1. 在“特征”页上，选择“+ 创建” 。

1. 在“新建短语列表特征”对话框中，输入名称（例如 `Cities`）。 在“值”框中，输入示例城市（例如 `Seattle`）。 可以一次键入一个值或者用逗号分隔的一组值，然后按 Enter****。

    > [!div class="mx-imgBorder"]
    > ![屏幕截图显示添加特征（短语列表）城市的操作](./media/luis-add-features/add-phrase-list-cities.png)

    为 LUIS 输入足够的值后，会显示建议。 对于建议的值，可以单击“+ 全部添加”，也可选择单个术语****。

1. 如果短语可以交换使用，则让“这些值可以交换”保持选中状态。

1. 可以使用“全局”设置将短语列表应用于整个应用，或应用于特定模型（意向或实体）。 如果创建短语列表，并作为意向或实体的特征，则切换后不会设置为全局。 在这种情况下，切换的意义在于该特征仅对该模型是本地的，因此对应用程序而言不是全局的。

1. 选择“完成”  。 新特征已添加到“ML 特征”页面。

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>


> [!Note]
> 可以在“ML 特征”页上，删除或停用上下文工具栏中的短语列表。

## <a name="global-phrase-list-applies-to-entire-app"></a>全局短语列表会应用于整个应用

应将短语列表应用于预期要帮助的意向或实体，但有时需要将短语列表应用于整个应用，充当全局特征。

在 ML 特征页面上，选择短语列表，然后在顶部的上下文工具栏中选择“全局应用”。

## <a name="model-as-a-feature"></a>作为特征的模型

实体可以是[意向或实体的特征](luis-concept-feature.md)。

若要将实体作为特征添加到意向，请从“意向”页面中选择意向，然后选择上下文工具栏上方的“+添加特征”。 该列表将包括所有可用作特征的短语列表和实体。

若要将实体作为特征添加到另一个实体，可以使用 [Entity Palette](label-entity-example-utterance.md#adding-entity-as-a-feature-from-the-entity-palette) 在“意向”详细信息页面上添加特征，也可以在“实体”详细信息页面上[添加特征](luis-how-to-add-entities.md#add-a-feature-to-a-machine-learned-entity)。

## <a name="next-steps"></a>后续步骤

添加、编辑、删除或停用特征后，再次[定型和测试应用](luis-interactive-test.md)，查看性能是否有所改善。
