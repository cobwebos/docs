---
title: 使用 Azure 机器学习设计器（预览版）重新训练模型
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习设计器（预览版）中通过已发布的管道重新训练模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 11e04d0aa313a005cfd13bca134c75bb712fd234
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661620"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>使用 Azure 机器学习设计器（预览版）重新训练模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

本操作说明文章介绍了如何使用 Azure 机器学习设计器重新训练机器学习模型。 你将使用已发布的管道自动执行工作流，并设置参数以使用新数据训练模型。 

在本文中，学习如何：

> [!div class="checklist"]
> * 训练机器学习模型。
> * 创建管道参数。
> * 发布训练管道。
> * 使用新参数重新训练模型。

## <a name="prerequisites"></a>先决条件

* 使用企业 SKU 的 Azure 机器学习工作区。
* 请完成本操作说明系列的第 1 部分“[在设计器中转换数据](how-to-designer-transform-data.md)”。

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

本文还假定你已掌握在设计器中生成管道的基本知识。 如需了解引导式简介，请完成[教程](tutorial-designer-automobile-price-train-score.md)。 

### <a name="sample-pipeline"></a>示例管道

本文中使用的管道是设计器主页中的示例管道 [收入预测](samples-designer.md#classification) 的更改版本。 该管道使用[导入数据](algorithm-module-reference/import-data.md)模块，而不是用于演示如何使用自己的数据训练模型的示例数据集。

![屏幕截图显示了修改后的示例管道，并使用方框突出显示“导入数据”模块](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>创建管道参数

创建管道参数，以在运行时动态设置变量。 此例将训练数据路径从固定值更改为参数，这样便可以使用不同的数据重新训练模型。

1. 选择“导入数据”模块。

    > [!NOTE]
    > 此例使用“导入数据”模块访问已注册数据存储中的数据。 但如果使用备用的数据访问模式，则可以遵循类似的步骤操作。

1. 在画布右侧的模块详细信息窗格中，选择数据源。

1. 输入数据的路径。 还可以选择“浏览路径”，以浏览文件树。 

1. 将鼠标悬停在“路径”字段，然后选择显示的“路径”字段上方的省略号 。

    ![屏幕截图显示了如何创建管道参数](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. 选择“添加到管道参数”。

1. 提供参数名称和默认值。

   > [!NOTE]
   > 你可以选择管道草稿标题旁边的“设置”齿轮图标来检查和编辑管道参数。 

1. 选择“保存”。

1. 提交管道运行。

## <a name="find-a-trained-model"></a>查找定型的模型

设计器会将所有管道输出（包括定型的模型）保存到默认工作区存储帐户。 你也可以直接在设计器中访问定型的模型：

1. 等待管道完成运行。
1. 选择**训练模型**模块。
1. 在画布右侧的模块详细信息窗格中，选择“输出 + 日志”。
1. 在“其他输出”中可以找到模型以及运行日志。
1. 或者，也可选择“查看输出”图标。 在这里，可以按照对话框中的说明直接导航到数据存储。 

> [!div class="mx-imgBorder"]
> ![屏幕截图显示了如何下载定型的模型](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>发布训练管道

将管道发布到管道终结点，便于将来轻松地重新使用管道。 管道终结点会创建 REST 终结点，供将来调用管道。 在此例中，借助管道终结点，你可以重新使用管道来根据不同的数据重新训练模型。

1. 选择设计器画布上方的“发布”。
1. 选择或创建管道终结点。

   > [!NOTE]
   > 可将多个管道发布到一个终结点。 给定终结点中的每个管道都有一个版本号，你可以在调用管道终结点时指定该版本号。

1. 选择“发布”。

## <a name="retrain-your-model"></a>重新训练模型

现在你已经有了一个已发布的训练管道，接下来就可以使用它来根据新数据重新训练模型。 你可以从工作室工作区或以编程方式通过管道终结点提交运行。

### <a name="submit-runs-by-using-the-studio-portal"></a>使用 studio 门户提交运行

使用以下步骤从 studio 门户提交参数化管道终结点：

1. 转到工作室工作区中的“终结点”页。
1. 选择“管道终结点”选项卡。然后，选择管道终结点。
1. 选择“已发布的管道”选项卡。然后，选择要运行的管道版本。
1. 选择“提交”。
1. 在“设置”对话框中，可以为运行指定参数值。 对于本例，请更新数据路径，使用非美国数据集来训练模型。

![屏幕截图显示了如何在设计器中设置参数化管道运行](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>使用代码提交运行

在“概述”面板中可以找到已发布管道的 REST 终结点。 通过调用终结点，可重新训练已发布的管道。

若要进行 REST 调用，需要 OAuth 2.0 持有者类型的身份验证标头。 要了解如何设置针对工作区的身份验证并执行参数化 REST 调用，请参阅[生成用于批量评分的 Azure 机器学习管道](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用设计器创建参数化训练管道终结点。

有关如何部署模型以执行预测的完整演示，请参阅[设计器教程](tutorial-designer-automobile-price-train-score.md)以训练和部署回归模型。
