---
title: 使用 Azure 机器学习设计器重新训练模型（预览）
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习设计器（预览版）中发布的管道重新训练模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.openlocfilehash: fb0122833dabe4030232efb3ca07557ce1826aff
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82981895"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>使用 Azure 机器学习设计器重新训练模型（预览版）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

本操作方法文章介绍如何使用 Azure 机器学习设计器重新训练机器学习模型。 您将使用已发布的管道自动执行工作流，并设置参数以针对新数据定型模型。 

在本文中，学习如何：

> [!div class="checklist"]
> * 训练机器学习模型。
> * 创建管道参数。
> * 发布训练管道。
> * 通过新参数重新训练模型。

## <a name="prerequisites"></a>先决条件

* 具有企业版 SKU 的 Azure 机器学习工作区。
* 完成本操作方法系列的第1部分，[在设计器中转换数据](how-to-designer-transform-data.md)。

本文还假定你具有在设计器中生成管道的基本知识。 对于引导式简介，请完成[教程](tutorial-designer-automobile-price-train-score.md)。 

### <a name="sample-pipeline"></a>示例管道

本文中使用的管道是[示例3：收入预测](samples-designer.md#classification)的更改版本。 管道使用[导入数据](algorithm-module-reference/import-data.md)模块，而不是示例数据集来向您演示如何使用自己的数据训练模型。

![用突出显示导入数据模块的框显示修改后的示例管道的屏幕截图](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>创建管道参数

创建管道参数以在运行时动态设置变量。 在此示例中，您将定型数据路径从固定值更改为参数，以便您可以对不同的数据重新训练模型。

1. 选择“导入数据”**** 模块。

    > [!NOTE]
    > 此示例使用 "导入数据" 模块访问已注册数据存储中的数据。 但是，如果使用备用的数据访问模式，则可以执行类似的步骤。

1. 在 "模块详细信息" 窗格的画布右侧，选择数据源。

1. 输入数据的路径。 还可以选择 "**浏览路径**" 以浏览文件树。 

1. 将鼠标悬停在 "**路径**" 字段上，然后选择显示的 "**路径**" 字段上方的省略号。

    ![显示如何创建管道参数的屏幕截图](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. 选择 "**添加到管道参数**"。

1. 提供参数名称和默认值。

   > [!NOTE]
   > 可以通过选择管道草稿标题旁边的 "**设置**" 齿轮图标来检查和编辑管道参数。 

1. 选择“保存”  。

1. 提交管道运行。

## <a name="find-a-trained-model"></a>查找训练的模型

设计器将所有管道输出（包括训练的模型）保存到默认工作区存储帐户。 您还可以直接在设计器中访问定型模型：

1. 等待管道完成运行。
1. 选择**训练模型**模块。
1. 在 "模块详细信息" 窗格中，选择 "**输出 + 日志**"。
1. 可在**其他输出**中找到模型以及运行日志。
1. 或者，选择 "**查看输出**" 图标。 在此处，你可以按照对话框中的说明直接导航到你的数据存储。 

![显示如何下载定型模型的屏幕截图](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>发布训练管道

将管道发布到管道终结点，以便在将来轻松重用管道。 管道终结点创建 REST 终结点，以便在将来调用管道。 在此示例中，您的管道终结点允许您重新使用您的管道重新训练不同数据上的模型。

1. 选择设计器画布上方的“发布”****。
1. 选择或创建管道终结点。

   > [!NOTE]
   > 可将多个管道发布到一个终结点。 给定终结点中的每个管道都提供一个版本号，你可以在调用管道终结点时指定该版本号。

1. 选择“发布”  。

## <a name="retrain-your-model"></a>重新训练模型

现在您已经有了一个已发布的训练管道，您可以使用它来重新训练新数据的模型。 你可以从 "工作室" 工作区或以编程方式从管道终结点提交运行。

### <a name="submit-runs-by-using-the-designer"></a>使用设计器提交运行

使用以下步骤从设计器提交参数化管道终结点：

1. 在工作室工作区中转到 "**终结点**" 页。
1. 选择 "**管道终结点**" 选项卡。然后，选择管道终结点。
1. 选择 "**已发布管道**" 选项卡。然后，选择要运行的管道版本。
1. 选择“提交”  。
1. 在 "设置" 对话框中，可以指定运行的参数值。 对于本示例，请更新数据路径以使用非美国数据集来训练模型。

![显示如何在设计器中设置参数化管道运行的屏幕截图](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>使用代码提交运行

可以在 "概述" 面板中找到已发布管道的 REST 终结点。 通过调用终结点，你可以重新训练已发布的管道。

若要进行 REST 调用，需要一个 OAuth 2.0 持有者类型的身份验证标头。 有关设置工作区身份验证和进行参数化 REST 调用的信息，请参阅[生成用于批处理评分的 Azure 机器学习管道](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用设计器创建参数化定型管道终结点。

有关如何部署模型以进行预测的完整演练，请参阅[设计器教程](tutorial-designer-automobile-price-train-score.md)，了解如何定型和部署回归模型。
