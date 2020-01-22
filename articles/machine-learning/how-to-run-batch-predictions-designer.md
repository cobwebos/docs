---
title: 使用 Azure 机器学习设计器运行批量预测
titleSuffix: Azure Machine Learning
description: 了解如何使用设计器训练模型并设置批量预测管道。 将管道部署为参数化 Web 服务，该服务可从任何 HTTP 库触发。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 01/13/2020
ms.custom: Ignite2019
ms.openlocfilehash: d2653699a69cb468e8490c2cba579b73e526d1ed
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311880"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>使用 Azure 机器学习设计器运行批量预测
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何使用设计器创建批处理预测管道。 批处理预测使你可以使用可从任何 HTTP 库触发的 web 服务连续评分大数据集。

本操作指南介绍了如何执行以下任务：

> [!div class="checklist"]
> * 创建并发布批处理推理管道
> * 使用管道终结点
> * 管理终结点版本

若要了解如何使用 SDK 设置批处理计分服务，请参阅随附的操作[方法](how-to-run-batch-predictions.md)。

## <a name="prerequisites"></a>必备组件

本操作说明假定你已经有了一个训练管道。 有关设计器的指导介绍，请完成[部分设计器教程](tutorial-designer-automobile-price-train-score.md)。 

## <a name="create-a-batch-inference-pipeline"></a>创建批量推理管道

训练管道至少必须运行一次才能创建推断管道。

1. 请在工作区中转到 "**设计器**" 选项卡。

1. 选择定型管道，定型模型要用于进行预测。

1. **运行**管道。

    ![运行管道](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

现在训练管道已运行，可以创建批处理推理管道。

1. 在 "**运行**" 旁边，选择新的 dropdown **Create 推理管道**。

1. 选择 "**批处理推理管道**"。

    ![创建批量推理管道](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
结果为默认批量推理管道。 

### <a name="add-a-pipeline-parameter"></a>添加管道参数

若要创建对新数据的预测，可以手动连接此管道草稿视图中的不同数据集，也可以创建数据集的参数。 参数使你可以在运行时更改 batch 推断进程的行为。

在本部分中，将创建一个数据集参数，以便指定要对其进行预测的不同数据集。

1. 选择数据集模块。

1. 画布右侧将显示一个窗格。 在窗格底部，选择 "**设置为管道参数**"。
   
    输入参数的名称，或接受默认值。

## <a name="publish-your-batch-inferencing-pipeline"></a>发布 batch 推断管道

现在，你已准备好部署推断管道。 这会部署管道，使其可供其他人使用。

1. 选择“发布”按钮。

1. 在出现的对话框中，展开 " **PipelineEndpoint**" 下拉箭头，然后选择 "**新建 PipelineEndpoint**"。

1. 提供终结点名称和可选说明。

    在对话框底部附近，可以看到使用培训期间使用的数据集 ID 的默认值配置的参数。

1. 选择“发布”。

![发布管道](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>使用终结点

现在，你已有一个带有 dataset 参数的已发布管道。 管道将使用在定型管道中创建的训练模型来评分作为参数提供的数据集。

### <a name="submit-a-pipeline-run"></a>提交管道运行 

在本部分中，您将设置手动管道运行，并将管道参数更改为对新数据进行评分。 

1. 部署完成后，请切换到 "**终结点**" 部分。

1. 选择**管道终结点**。

1. 选择已创建的终结点的名称。

![终结点链接](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. 选择 "**已发布管道**"。

    此屏幕显示此终结点下发布的所有已发布管道。

1. 选择已发布的管道。

    "管道详细信息" 页显示了管道的详细运行历史记录和连接字符串信息。 
    
1. 选择 "**运行**"，创建管道的手动运行。

    ![管道详细信息](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. 更改参数以使用不同的数据集。
    
1. 选择 "**运行**" 以运行管道。

### <a name="use-the-rest-endpoint"></a>使用 REST 终结点

可以在 "**终结点**" 部分找到有关如何使用管道终结点和已发布管道的信息。

你可以在 "运行概述" 面板中找到管道终结点的 REST 终结点。 通过调用终结点，你将使用其默认的已发布管道。

还可以在 "**已发布管道**" 页中使用已发布的管道。 选择已发布的管道，并查找它的 REST 终结点。 

![Rest 终结点详细信息](./media/how-to-run-batch-predictions-designer/rest-endpoint-details.png)

若要进行 REST 调用，你将需要 OAuth 2.0 持有者类型身份验证标头。 请参阅以下[教程部分](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)，以详细了解如何设置工作区的身份验证并进行参数化 REST 调用。

## <a name="versioning-endpoints"></a>版本控制终结点

设计器将一个版本分配给发布到终结点的每个后续管道。 可以指定要作为 REST 调用中的参数执行的管道版本。 如果未指定版本号，设计器将使用默认管道。

在发布管道时，可以选择将其设为该终结点的新默认管道。

![设置默认管道](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

你还可以在终结点的 "**已发布管道**" 选项卡中设置新的默认管道。

![设置默认管道](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>后续步骤

按照设计器[教程](tutorial-designer-automobile-price-train-score.md)训练和部署回归模型。
