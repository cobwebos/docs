---
title: 在 LUIS 应用程序中添加功能 | Microsoft Docs
description: 使用语言理解 (LUIS) 添加应用功能，可以改进对类别和模式的意向和实体的检测或预测
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/30/2018
ms.author: diberry
ms.openlocfilehash: 5ec75436c7df5c08f5507794229bec1f9adb2804
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222947"
---
# <a name="use-features-to-improve-your-luis-apps-performance"></a>使用相关功能来改进 LUIS 应用的性能  

可以将功能添加到 LUIS 应用以提高其准确性。 功能通过提供某些字词和短语属于某个类别的提示来帮助 LUIS。 如果 LUIS 了解如何识别该类别的一个成员，它能够以同样方式处理其他成员。

## <a name="add-phrase-list"></a>添加短语列表

1. 单击“我的应用”页上的名称打开应用，单击“构建”，然后单击应用左侧面板中的“短语列表”。 

    ![短语列表导航](./media/luis-add-features/phrase-list-nav.png)

2. 在“短语列表”页上，单击“新建短语列表”。 
 
    ![新建短语列表](./media/luis-add-features/create-new-phrase-list.png)
    
3. 在“添加短语列表”对话框中，键入“城市”作为短语列表的名称。 在“值”框中，键入短语列表的值。 可以一次键入一个值或者用逗号分隔的一组值，然后按 Enter。

    ![添加短语列表“城市”](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS 可以建议相关值，用于添加到短语列表。 单击“建议”，获取一组与添加的值在语义上相关的建议值。 可以单击任何建议的值，或单击“全部添加”添加所有值。

    ![短语列表建议的值](./media/luis-add-features/related-values.png)

5. 如果添加的短语列表值是可交换使用的替代值，则单击“这些值可以交换”。

    ![短语列表建议的值](./media/luis-add-features/interchangeable.png)

6. 单击“ **保存**”。 将“城市”短语列表添加到“短语列表”页。

    ![已添加的短语列表](./media/luis-add-features/phrase-list-cities.png)

## <a name="edit-phrase-list"></a>编辑短语列表

单击“短语列表”页上短语列表的名称。 在打开的“编辑短语列表”对话框中，进行任何所需的编辑更改，然后单击“保存”。

 ![已添加的短语列表](./media/luis-add-features/edit-phrase-list.png)

## <a name="delete-phrase-list"></a>删除短语列表 

单击行末尾的省略号 (...) 按钮，然后选择“删除”。

 ![删除已添加的列表](./media/luis-add-features/delete-phrase-list.png)

## <a name="deactivate-phrase-list"></a>停用短语列表 

单击行末尾的省略号 (...) 按钮，然后选择“停用”。

 ![停用已添加的列表](./media/luis-add-features/deactivate-phrase-list.png)

## <a name="pattern-regular-expression-feature"></a>模式（正则表达式）功能 
此功能已弃用。 新的模式功能无法添加到 LUIS。 对任何现有模式功能的支持将截止到 2018 年 5 月。 有助于将标准 LUIS 正则表达式与 PR 匹配到[识别器-文本 Github 存储库](https://github.com/Microsoft/Recognizers-Text)。 

## <a name="next-steps"></a>后续步骤

添加、编辑、删除或停用短语列表后，再次[定型和测试应用](luis-interactive-test.md)，查看性能是否有所改善。
