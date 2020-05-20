---
title: 特征 - LUIS
titleSuffix: Azure Cognitive Services
description: 使用语言理解 (LUIS) 添加应用功能，可以改进对类别和模式的意向和实体的检测或预测
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: 8b52102ec26ec94097e4b5b9aa1b1730787cfa4b
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654099"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>使用功能提升单词列表的信号

可以将功能添加到 LUIS 应用以提高其准确性。 特征通过提供某些字词和短语是应用域词汇表的一部分的提示来帮助 LUIS。

查看[概念](luis-concept-feature.md)以了解何时以及为何使用功能。

## <a name="add-phrase-list-as-a-feature"></a>添加短语列表作为功能

1. 登录到[LUIS 门户](https://www.luis.ai)，选择**订阅**和**创作资源**，查看分配给该创作资源的应用。
1. 通过在 **"我的应用**" 页上选择应用程序的名称来打开应用。
1. 选择 "**生成**"，然后在应用的左侧面板中选择 "**功能**"。

1. 在 "**功能**" 页上，选择 " **+ 创建**"。

1. 在 "**创建新短语列表功能**" 对话框中，输入名称，例如 `Cities` 。 在 "**值**" 框中，输入城市的示例，例如 `Seattle` 。 可以一次键入一个值或者用逗号分隔的一组值，然后按 Enter****。

    > [!div class="mx-imgBorder"]
    > ![添加功能（短语列表）城市的屏幕截图](./media/luis-add-features/add-phrase-list-cities.png)

    为 LUIS 输入足够的值后，会显示建议。 对于建议的值，可以单击“+ 全部添加”，也可选择单个术语****。

1. 如果可互换使用这些短语，请检查**这些值的可交换值**。

1. 短语列表可应用于具有**全局**设置的整个应用或特定模型（意向或实体）。 如果创建短语列表作为意向或实体中的_功能_，则不会为 global 设置切换。 在这种情况下，切换的含义是该功能仅对该模型是本地的，因此，它不是应用程序的_全局性_。

1. 选择“完成”。 新功能将添加到**ML 功能**页面。

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>


> [!Note]
> 你可以从**ML 功能**页面上的上下文工具栏中删除或停用词组列表。

## <a name="global-phrase-list-applies-to-entire-app"></a>全局短语列表适用于整个应用

短语列表应应用于它旨在帮助的目的或实体，但有时，短语列表应作为**全局**功能应用到整个应用。

在 "ML 功能" 页上，选择 "短语" 列表，然后在顶部的上下文工具栏中选择 "**设为全局性**"。

## <a name="model-as-a-feature"></a>作为特征的模型

实体可以是[意向或实体的功能](luis-concept-feature.md)。

若要将实体作为功能添加到意向，请从 "意向" 页中选择意向，然后在上下文工具栏上方选择 " **+ 添加功能**"。 此列表将包含可应用为功能的所有短语列表和实体。

若要将实体作为功能添加到另一个实体，可以使用 "[实体" 调色板](label-entity-example-utterance.md#adding-entity-as-a-feature-from-the-entity-palette)在 "目的详细信息" 页上添加该功能，也可以在 "实体详细信息" 页上[添加](luis-how-to-add-entities.md#add-a-feature-to-a-machine-learned-entity)该功能。

## <a name="next-steps"></a>后续步骤

添加、编辑、删除或停用功能后，请再次[训练并测试应用](luis-interactive-test.md)，以查看性能是否提高。
