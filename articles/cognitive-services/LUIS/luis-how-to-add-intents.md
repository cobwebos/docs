---
title: 在 LUIS 应用程序中添加意向 | Microsoft Docs
description: 使用语言理解 (LUIS) 添加意向，帮助了解用户请求并做出合理回应。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 0ebf15ea49467674ab3c56aa7983131593cf5c9a
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225880"
---
# <a name="manage-intents"></a>管理意向 
向 LUIS 应用添加[意向](luis-concept-intent.md)，识别具有相同意向的问题或命令组。 

单击 LUIS 左侧面板中的“意向”可打开“意向”页，可在其中添加和管理意向。 

以下过程演示如何在 TravelAgent 应用中添加“Bookflight”意向。

## <a name="add-intent"></a>添加意向

1. 通过单击“我的应用”页中的应用名称来打开应用（如 TravelAgent），然后单击左侧面板中的“意向”。 
2. 在“意向”页上，单击“创建新意向”。

    ![意向列表](./media/luis-how-to-add-intents/IntentsList.png)
3. 在“创建新意向”对话框中，键入意向名称“BookFlight”，然后单击“完成”。

    ![添加意向](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    在新添加意向的意向详细信息页上，[添加表述](#add-an-utterance-on-intent-page)。

## <a name="rename-intent"></a>重命名意向

1. 在“意向”页上，单击意向名称旁边的“重命名”图标 ![重命名意向](./media/luis-how-to-add-intents/Rename-Intent-btn.png)。 

2. “意向”页上的对话框中显示当前的意向名称。 编辑意向名称，然后按 Enter。 将保存新名称并在意向页上显示。

    ![编辑意向](./media/luis-how-to-add-intents/EditIntent-dialogbox.png)

## <a name="delete-intent"></a>删除意向
删除 None 意向以外的意向时，可将所有表述添加到 None 意向。 需要移动而非删除表述时，此方法非常有用。   

1. 在“意向”页上，单击意向名称右侧的“删除意向”按钮。 

    ![“删除意向”按钮](./media/luis-how-to-add-intents/DeleteIntent.png)

2. 单击确认对话框上的“确定”按钮。

<!--
    TBD: waiting for confirmation about which delete dialog is going to be in //BUILD

    ![Delete Intent Dialog](./media/luis-how-to-add-intents/DeleteIntent-Confirmation.png)
-->


## <a name="add-an-utterance-on-intent-page"></a>在意向页添加表述

在意向页的意向名称下的文本框中，输入预期用户会输入的相关表述，例如 `book 2 adult business tickets to Paris tomorrow on Air France`，然后按 Enter。 
 
>[!NOTE]
>LUIS 会将所有表述转换为小写。

![意向详细信息页的屏幕截图，其中突出显示了表述](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

表述随即添加到当前意向的表述列表中。 添加表述后，在表述中[为任何实体添加标记](luis-how-to-add-example-utterances.md)，然后[培训](luis-how-to-train.md)应用。 

## <a name="create-a-pattern-from-an-utterance"></a>通过表述创建模式
请参阅[在意向或实体页上通过现有表述添加模式](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page)。

## <a name="edit-an-utterance-on-intent-page"></a>在意向页编辑表述

要编辑话语，请选择该话语所在行最右侧的省略号 (...) 按钮，然后选择“编辑”。 修改文本，然后在键盘上按 Enter。

![意向详细信息页的屏幕截图，其中突出显示了省略号按钮](./media/luis-how-to-add-intents/edit-utterance.png) 

## <a name="reassign-utterances-on-intent-page"></a>在意向页上重新分配表述
通过将表述重新分配给其他意向，可更改一个或多个表述的意向。 

若要将一个表述重新分配给其他意向，请在该表述所在行的最右侧，从“已标记意向”列下选择正确的意向名称。 该表述随即从当前意向的表述列表中删除。 

![BookFlight 意向页的屏幕截图，其中选中了“已标记意向”列下的一个表述的意向](./media/luis-how-to-add-intents/reassign-1-utterance.png)

要更改多个表述的意向，请选择表述左侧的复选框，然后选择“重新分配意向”。 从列表中选择正确的意向。

![BookFlight 意向页的屏幕截图，其中选中了一个表述并且突出显示了“重新分配意向”按钮](./media/luis-how-to-add-intents/delete-several-utterances.png) 

## <a name="delete-utterances-on-intent-page"></a>删除意向页上的表述

要删除话语，请选择该话语所在行最右侧的省略号 (...) 按钮，然后选择“删除”。 该表述随即从列表和 LUIS 应用中删除。

![意向详细信息页的屏幕截图，其中突出显示了“删除”选项](./media/luis-how-to-add-intents/delete-utterance-ddl.png)

要删除多个表述：

1. 选中表述左侧的复选框，然后选择“删除表述”。 

    ![意向详细信息页的屏幕截图，其中选中了多个表述并突出显示了“删除表述”按钮](./media/luis-how-to-add-intents/delete-several-utterances.png)

2. 在“删除表述?”弹出对话框中，选择“完成”。

## <a name="search-in-utterances-on-intent-page"></a>在意向页搜索表述
在意向中，可以搜索包含文本（字词或短语）的表述。 例如，你可能会发现涉及某个特定字词的错误，并且希望找出包含该特定字词的所有示例。 

1. 选择工具栏中的放大镜图标。

    ![意向页的屏幕截图，其中突出显示了放大镜搜索图标](./media/luis-how-to-add-intents/magnifying-glass.png)

2. 搜索文本框随即显示。 在表述列表右上角的搜索框中键入字词或短语。 表述列表将更新，仅显示包含搜索文本的表述。 

    ![意向页屏幕截图，其中突出显示了搜索文本框](./media/luis-how-to-add-intents/search-textbox.png)

    若要取消搜索并还原表述的完整列表，请删除键入的搜索文本。 若要关闭搜索文本框，请再次选择工具栏中的放大镜图标。

## <a name="prediction-discrepancy-errors-on-intent-page"></a>意向页上的预测偏差错误
意向中的表述可能在所选意向和预测分数之间存在偏差。 LUIS 用带红框的分数表示此偏差。 

![BookFlight 意向页的屏幕截图，其中突出显示了预测偏差分数](./media/luis-how-to-add-intents/score-discrepancy.png) 

## <a name="filter-by-intent-prediction-discrepancy-errors-on-intent-page"></a>在意向页上按意向预测偏差错误进行筛选
要筛选表述列表以仅显示具有意向预测偏差的表述，请将工具栏中的“显示全部”切换到“仅错误”。 

## <a name="filter-by-entity-type-on-intent-page"></a>在意向页上按实体类型进行筛选
使用工具栏上的“实体筛选器”下拉列表，按实体筛选表述。 

![意向页的屏幕截图，其中突出显示了实体类型筛选器](./media/luis-how-to-add-intents/filter-by-entities.png) 

若要删除该筛选器，请选择工具栏下包含字词或短语的蓝色筛选框。  
<!-- TBD: waiting for ux fix - bug in ux of prebuit entity number -- when filtering by it, it doesn't show the list -->

## <a name="switch-to-token-view-on-intent-page"></a>在意向页上切换到标记视图
切换“标记视图”，查看标记而非实体类型名称。 还可使用键盘上的 Ctrl+E 切换视图。 

![BookFlight 意向的屏幕截图，其中突出显示了“标记视图”](./media/luis-how-to-add-intents/toggle-tokens-view.png)

## <a name="train-your-app-after-changing-model-with-intents"></a>在对模型进行意向更改后对应用进行培训
添加、编辑或删除意向后，请针对更改来[培训](luis-how-to-train.md)和[发布](luis-how-to-publish-app.md)应用以影响终结点查询。 

## <a name="next-steps"></a>后续步骤

将意向添加到应用后，下一个任务是开始为添加的意向添加[示例表述](luis-how-to-add-example-utterances.md)。 
