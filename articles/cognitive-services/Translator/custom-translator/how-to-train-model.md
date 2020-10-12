---
title: 训练模型 - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 生成翻译模型时，对模型进行训练是一个重要步骤。 训练将根据你选择用于训练的文档进行。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 824516a327d45feb5b6a084a113633bd3a486abe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88508327"
---
# <a name="train-a-model"></a>训练模型

定型模型是生成翻译模型的第一步和最重要的一步，否则无法生成模型。 训练将根据你选择用于训练的文档进行。 选择 "训练" 文档类型的文档时，请注意10000并行句子的最低要求。 选择文档时，会显示指导您的训练句子总数。 如果只选择字典文档类型的文档来训练模型，则此要求不适用。

若要训练模型，请执行以下操作：

1. 选择要在其中生成模型的项目。

2. 项目的“数据”选项卡将显示项目语言对的所有相关文档。 手动选择要用来训练模型的文档。 可以从此屏幕选择训练、优化和测试文档。 另外，你只需要选择训练集并让自定义翻译为你创建优化和测试集。

    - 文档名称：文档的名称。

    - 配对：如果此文档是一个并行或多语言文档。 通常不支持使用多语言文档进行训练。

    - 文档类型：可以是训练、优化、测试或字典。

    - 语言对：这显示项目的源和目标语言。

    - 源句子：显示从源文件中提取的句子数。

    - 目标句子：显示从目标文件中提取的句子数。

    ![定型模型](media/how-to/how-to-train-model.png)

3. 单击 "创建模型" 按钮。

4. 在对话框中，为您的模型指定名称。 默认情况下，当您单击 "创建模型" 按钮时，将选择 "立即训练" 以启动训练管道。 您可以选择 "另存为草稿" 来创建模型元数据并将模型置于草稿状态，但不会启动模型定型。 稍后，您必须手动选择处于草稿状态的模型进行定型。

5. 单击 "创建模型" 按钮。

    ![“训练模型”对话框](media/how-to/how-to-train-model-2.png)

6. 自定义转换器将提交训练，并在 "模型" 选项卡中显示训练状态。

    ![“训练模型”页面](media/how-to/how-to-train-model-3.png)

>[!Note]
>自定义转换器在某个时间点在工作区中支持10个并发培训。

## <a name="modify-a-model-name"></a>修改模型名称

您可以从模型详细信息页中修改模型名称。

1. 从 "项目" 页上，单击模型所在的项目名称。
2. 单击 "模型" 选项卡。
3. 单击模型名称以查看模型详细信息。
4. 单击铅笔图标。

    ![编辑模型](media/how-to/how-to-edit-model.png)

5. 在对话框中，更改模型名称并为模型指定一个有意义的名称。

    ![“编辑模型”对话框](media/how-to/how-to-edit-model-dialog.png)

6. 单击“保存”。

## <a name="next-steps"></a>后续步骤

- 了解[如何查看模型详细信息](how-to-view-model-details.md)。
