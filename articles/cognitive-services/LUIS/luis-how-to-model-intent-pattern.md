---
title: 在 LUIS 应用中添加模式模板 | Microsoft Docs
titleSuffix: Azure
description: 了解如何在语言理解 (LUIS) 应用程序中添加模式模板，提高预测准确性。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: 68c0ea1fd3f2e60e0adec631f33c8bd09a3d9960
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35371065"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>如何添加模式以提高预测准确性
在 LUIS 应用接收到终结点表述后，请使用模式的[概念](luis-concept-patterns.md)提高表述（按字词顺序和字词选择呈现模式）的预测准确性。 模式使用[实体](luis-concept-entity-types.md)及其角色并通过特定模式语法提取数据。 

## <a name="add-template-utterance-to-create-pattern"></a>添加模板表述以创建模式
1. 通过选择“我的应用”页上的应用名称来打开应用，然后选择“提高应用性能”下左侧面板中的“模式”。

    ![模式列表的屏幕截图](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. 为模式选择正确意向。 

    ![选择意向](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. 在模板文本框中，键入模板表述，然后按 Enter。 如果要输入实体名称，请使用正确的模式实体语法。 使用以 `{` 开头的实体语法。 实体列表随即显示。 选择正确的实体，然后按 Enter。 

    ![模式实体的屏幕截图](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    如果实体中包含角色，请在实体名称后用一个冒号 `:` 指示该角色，例如 `{Location:Origin}`。 实体的角色列表将以列表形式显示。 选择该角色，然后按 Enter。 

    ![包含角色的实体的屏幕截图](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    选择正确的实体后，请完成模式输入，然后按 Enter。 模式输入完成后，请[培训](luis-how-to-train.md)应用。

    ![包含两种实体类型的已输入模式的屏幕截图](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="search-patterns"></a>搜索模式
通过搜索可找到包含某些给定文本的模式。  

1. 选择放大镜图标。

    ![模式页的屏幕截图，其中突出显示了搜索工具图标](./media/luis-how-to-model-intent-pattern/search-icon.png)

    在模式列表右上角的搜索框中键入搜索文本，然后按 Enter。 模式列表将更新，仅显示包含搜索文本的模式。

    ![模式页的屏幕截图，其中突出显示了搜索框中的搜索文本](./media/luis-how-to-model-intent-pattern/search-text.png)

    若要取消搜索并还原模式的完整列表，请删除键入的搜索文本。

<!-- TBD: should I be able to click on the magnifying glass again to close the search box? It doesn't reset the list. -->

## <a name="edit-a-pattern"></a>编辑模式
1. 要编辑模式，请选择该模式所在行最右侧的三点 (...) 图标，然后选择“编辑”。 

    ![模式行中“编辑”菜单项的屏幕截图](./media/luis-how-to-model-intent-pattern/patterns-three-dots.png) 

2. 在文本框中输入任何更改。 完成后，请按“Enter”。 完成模式编辑后，请[培训](luis-how-to-train.md)应用。

    ![编辑模式的屏幕截图](./media/luis-how-to-model-intent-pattern/edit-pattern.png)

## <a name="reassign-individual-pattern-to-different-intent"></a>为其他意向重新分配一个模式

若要为其他意向重新分配一个模式，请选择模式文本右侧的意向列表框，然后选择一个其他意向。

![为其他意向重新分配一个模式的屏幕截图](./media/luis-how-to-model-intent-pattern/reassign-individual-pattern.png)

## <a name="reassign-several-patterns-to-different-intent"></a>为其他意向重新分配多个模式

若要为其他意向重新分配多个模式，请选择每个模式左侧的复选框或选择顶部的复选框。 “重新分配意向”选项在工具栏上显示。 为模式选择正确意向。 

![为其他意向重新分配多个模式的屏幕截图](./media/luis-how-to-model-intent-pattern/reassign-many-patterns.png)

## <a name="delete-a-single-pattern"></a>删除一个模式

1. 要删除模式，请选择该模式所在行最右侧的三点 (...) 图标，然后选择“删除”。 

    ![删除表述的屏幕截图](./media/luis-how-to-model-intent-pattern/patterns-three-dots-ddl.png)

2. 选择“确定”以确认删除。

    ![删除确认屏幕截图](./media/luis-how-to-model-intent-pattern/confirm-delete.png)

## <a name="delete-several-patterns"></a>删除多个模式

1. 若要删除多个模式，请选择每个模式左侧的复选框或选择顶部的复选框。 “删除模式”选项在工具栏上显示。 选择“删除模式”。  

    ![删除多个模式的屏幕截图](./media/luis-how-to-model-intent-pattern/delete-many-patterns.png)

2. “删除模式”确认对话框随即显示。 选择“确定”完成删除。

    ![删除多个模式的屏幕截图](./media/luis-how-to-model-intent-pattern/delete-many-patterns-confirmation.png)

## <a name="filter-pattern-list-by-entity"></a>按实体筛选模式列表

若要按特定实体筛选模式列表，请在模式上方的工具栏中选择“实体筛选器”。 

![按实体筛选模式的屏幕截图](./media/luis-how-to-model-intent-pattern/filter-entities-1.png)

应用筛选器后，实体名称将在工具栏下方显示。 

## <a name="filter-pattern-list-by-intent"></a>按意向筛选模式列表

若要按特定意向筛选模式列表，请在模式上方的工具栏中选择“意向筛选器”。 

![按意向筛选模式的屏幕截图](./media/luis-how-to-model-intent-pattern/filter-intents-1.png)

应用筛选器后，意向名称将在工具栏下方显示。 

## <a name="remove-entity-or-intent-filter"></a>删除实体或意向筛选器
筛选模式列表后，实体或意向名称将在工具栏下方显示。 若要删除筛选器，请选择名称。

![按实体筛选出的模式的屏幕截图](./media/luis-how-to-model-intent-pattern/filter-entities-2.png)

筛选器将被删除，同时显示所有模式。 

## <a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>在意向或实体页上通过现有表述添加模式
可在“意向”或“实体”页上通过现有表述创建模式。 任何意向或实体页上的所有表述都在列表中显示，可在列表的右侧列中访问表述级选项，例如“编辑”、“删除”和“添加为模式”。

1. 在所选的表述行上，选择表述右侧的三个点 (...)，然后选择“添加为模式”。

    [![](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png "表述表的屏幕截图，其中突出显示了选项菜单中的“添加为模式”")](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png)

2. 根据[语法规则](luis-concept-patterns.md#pattern-syntax)修改模式。 如果所选表述已使用实体进行标记，则表示这些实体已在具有正确语法的模式中。

    ![按实体筛选出的模式的屏幕截图](./media/luis-how-to-model-intent-pattern/confirm-patterns-modal.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>在对模型进行模式更改后对应用进行培训
添加、编辑、删除或重新分配模式后，请针对更改来[培训](luis-how-to-train.md)和[发布](PublishApp.md)应用以影响终结点查询。 

## <a name="next-steps"></a>后续步骤

* 了解如何[生成模式](luis-tutorial-pattern.md)（具有 pattern.any 和角色）。
* 了解如何[培训](luis-how-to-train.md)应用。