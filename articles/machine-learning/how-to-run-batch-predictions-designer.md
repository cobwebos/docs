---
title: 使用 Azure 机器学习设计器运行批量预测（预览版）
titleSuffix: Azure Machine Learning
description: 了解如何使用设计器训练模型并设置批量预测管道。 将管道部署为参数化 Web 服务，该服务可从任何 HTTP 库触发。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 02/24/2020
ms.custom: Ignite2019
ms.openlocfilehash: 01d69bffcf2c17abceba8ba2e0893360bead8b12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477215"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer-preview"></a>使用 Azure 机器学习设计器运行批量预测（预览版）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

本文介绍如何使用设计器创建批量预测管道。 批量预测使你能够按需使用可从任何 HTTP 库触发的 Web 服务持续为大型数据集评分。

本操作指南将介绍如何执行以下任务：

> [!div class="checklist"]
> * 创建并发布批量推理管道
> * 使用管道终结点
> * 管理终结点版本

若要了解如何使用 SDK 设置批量评分服务，请参阅随附的[操作指南](how-to-run-batch-predictions.md)。

## <a name="prerequisites"></a>必备条件

本操作指南假设已有了一个训练管道。 有关设计器的引导式介绍，请参阅[设计器教程的第一部分](tutorial-designer-automobile-price-train-score.md)。 

## <a name="create-a-batch-inference-pipeline"></a>创建批量推理管道

必须至少运行训练管道一次，才能创建推断管道。

1. 转到工作区中的“设计器”选项卡。 

1. 选择训练管道，该管道将训练用于做出预测的模型。

1. **提交**管道。

    ![提交管道](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

运行训练管道后，接下来可以创建批量推理管道。

1. 在 "**提交**" 旁边，选择新的 dropdown **Create 推理管道**。

1. 选择“批量推理管道”。 

    ![创建批量推理管道](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
结果为默认批量推理管道。 

### <a name="add-a-pipeline-parameter"></a>添加管道参数

若要对新数据创建预测，可以在此管道草稿视图中手动连接一个不同的数据集，或者创建数据集的参数。 参数可以在运行时更改批量推理过程的行为。

在本部分，你将创建一个数据集参数，用于指定预测时所要针对的不同数据集。

1. 选择数据集模块。

1. 画布右侧将显示一个窗格。 在该窗格的底部，选择“设为管道参数”。 
   
    输入参数的名称，或接受默认值。

## <a name="publish-your-batch-inferencing-pipeline"></a>发布批量推断管道

现已准备好部署推断管道。 此操作将部署该管道，并使其可供其他人使用。

1. 选择“发布”按钮  。

1. 在显示的对话框中，展开“管道终结点”对应的下拉列表，然后选择“新建管道终结点”。  

1. 提供终结点的名称和可选说明。

    在对话框底部附近，可以看到使用训练期间通过数据集 ID 默认值配置的参数。

1. 选择“发布”  。

![发布管道](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>使用终结点

现已发布一个具有数据集参数的管道。 该管道将使用训练管道中创建的已训练模型来评分以参数形式提供的数据集。

### <a name="submit-a-pipeline-run"></a>提交管道运行 

在本部分，你将设置一个手动管道运行，并更改管道参数，以便为新数据评分。 

1. 部署完成后，转到“终结点”部分。 

1. 选择“管道终结点”。 

1. 选择创建的终结点的名称。

![终结点链接](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. 选择“已发布的管道”。 

    此屏幕将显示此终结点下发布的所有管道。

1. 选择你发布的管道。

    管道详细信息页将显示该管道的详细运行历史记录和连接字符串信息。 
    
1. 选择 "**提交**" 以创建管道的手动运行。

    ![管道详细信息](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. 更改参数以使用不同的数据集。
    
1. 选择 "**提交**" 以运行管道。

### <a name="use-the-rest-endpoint"></a>使用 REST 终结点

可以在“终结点”部分找到有关如何使用管道终结点和已发布管道的信息。 

可以在运行概述面板中找到管道终结点的 REST 终结点。 调用终结点即会使用其默认的已发布管道。

也可以在“已发布的管道”页中使用已发布的管道。  选择已发布的管道，并查找其 REST 终结点。 

![REST 终结点详细信息](./media/how-to-run-batch-predictions-designer/rest-endpoint-details.png)

若要发出 REST 调用，需要 OAuth 2.0 持有者类型身份验证标头。 请参阅以下[教程部分](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)，以详细了解如何设置工作区的身份验证并进行参数化 REST 调用。

## <a name="versioning-endpoints"></a>对终结点进行版本控制

设计器会将一个版本分配给要发布到终结点的每个后续管道。 可以指定要在 REST 调用中作为参数执行的管道版本。 如果未指定版本号，设计器将使用默认管道。

发布管道时，可以选择将其设为该终结点的新默认管道。

![设置默认管道](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

也可以在终结点的“已发布的管道”选项卡中设置新的默认管道。 

![设置默认管道](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>后续步骤

按照设计器[教程](tutorial-designer-automobile-price-train-score.md)训练和部署回归模型。
''