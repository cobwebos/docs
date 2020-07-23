---
title: 训练模型 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 生成翻译模型时，对模型进行训练是一个重要步骤。 训练将根据你选择用于训练的文档进行。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: c29a0b8b429705bb0315c37fc6fe63eb8d77511f
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996697"
---
# <a name="train-a-model"></a>训练模型

对模型进行训练是用于生成翻译模型的重要步骤，因为不进行训练便无法生成模型。 训练将根据你选择用于训练的文档进行。

若要训练模型，请执行以下操作：

1.  选择要在其中生成模型的项目。

2.  项目的“数据”选项卡将显示项目语言对的所有相关文档。 手动选择要用来训练模型的文档。 可以从此屏幕选择训练、优化和测试文档。 另外，你只需要选择训练集并让自定义翻译为你创建优化和测试集。

    -  文档名称：文档的名称。

    -  配对：如果此文档是一个并行或多语言文档。 通常不支持使用多语言文档进行训练。

    -  文档类型：可以是训练、优化、测试或字典。

    -  语言对：这显示项目的源和目标语言。

    -  源句子：显示从源文件中提取的句子数。

    -  目标句子：显示从目标文件中提取的句子数。

    ![定型模型](media/how-to/how-to-train-model.png)

3.  单击“训练”按钮。

4.  在对话框中，为你的模型指定一个名称。

5.  单击“训练模型”。

    ![“训练模型”对话框](media/how-to/how-to-train-model-2.png)

6.  自定义翻译将提交训练，并在“模型”选项卡中显示训练状态。

    ![“训练模型”页面](media/how-to/how-to-train-model-3.png)

>[!Note]
>自定义转换器在某个时间点在工作区中支持10个并发培训。


## <a name="edit-a-model"></a>编辑模型

可以使用“模型详细信息”页面上的“编辑”链接来编辑模型。

1.  单击铅笔图标。

    ![编辑模型](media/how-to/how-to-edit-model.png)

2.  在对话框中，更改

    1.  模型名称（必需）：为你的模型提供一个有意义的名称。

        ![“编辑模型”对话框](media/how-to/how-to-edit-model-dialog.png)

3.  单击“保存”。


## <a name="next-steps"></a>后续步骤

- 了解[如何查看模型详细信息](how-to-view-model-details.md)。
