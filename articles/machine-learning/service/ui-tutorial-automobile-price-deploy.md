---
title: 教程：使用可视界面部署机器学习模型
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习可视界面中生成预测分析解决方案。 使用拖放式模块训练、评分和部署机器学习模型。
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 10/22/2019
ms.openlocfilehash: 6f8717f70a2cb03a7fd683cfe61f1198461f4305
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792673"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>教程：使用可视界面部署机器学习模型

为使其他人可以使用[教程的第一部分](ui-tutorial-automobile-price-train-score.md)开发的预测模型，可将它部署为实时终结点。 在第 1 部分，我们训练了模型。 现在，让我们基于用户输入生成新的预测。 本教程部分介绍以下操作：

> [!div class="checklist"]
> * 部署实时终结点
> * 创建推理群集
> * 测试实时终结点

## <a name="prerequisites"></a>先决条件

完成[教程的第一部分](ui-tutorial-automobile-price-train-score.md)，了解如何在可视界面中训练机器学习模型并为其评分。

## <a name="deploy-a-real-time-endpoint"></a>部署实时终结点

若要部署管道，必须执行以下操作：

1. 将训练管道转换成实时推理管道，以便删除训练模块并添加推理请求的输入和输出。
1. 部署推理管道。

### <a name="create-a-real-time-inference-pipeline"></a>创建实时推理管道

1. 在管道画布顶部，选择“创建推理管道”   >   “实时推理管道”

    选择“创建推理管道”时，会发生一些事情： 
    
    * 训练的模型在模块调色板中存储为“数据集”模块。  可以在“我的数据集”下找到它。 
    * 用于训练的模块（例如“训练模型”和“拆分数据”）被删除。  
    * 保存的训练模型已添加回管道中。
    * 已添加“Web 服务输入”  和  “Web 服务输出”模块。 这些模块标识用户数据进入模型的位置，以及返回数据的位置。

    > [!Note]
    > **训练管道**保存在管道画布顶部的新选项卡下。 它也可作为已发布管道显示在视觉界面中。
    >

    管道现在应如下所示：  

   ![显示做好部署准备后管道的预期配置的屏幕截图](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

1. 选择“运行”，  使用在第 1 部分使用过的计算目标和试验。

1. 选择“评分模型”模块。 

1. 在属性窗格中选择“输出” > “可视化”，验证模型是否仍有效。   此时会显示原始数据，以及预测的价格（“评分标签”）。

1. 选择“部署”。 

### <a name="create-an-inferencing-cluster"></a>创建推理群集

在显示的对话框中，可以从工作区的现有 Azure Kubernetes 服务 (AKS) 群集中进行选择，以便部署模型。 如果没有 AKS 群集，可通过以下步骤创建一个。

1. 在对话框中选择“计算”，导航到“计算”页。  

1. 在导航功能区中，选择“推理群集” > “+ 新建”。  

    ![屏幕截图，显示如何导航到新的推理群集窗格](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. 在推理群集窗格中，配置新的 Kubernetes 服务。

1. 输入“aks-compute”作为“计算名称”。 
    
1. 选择附近的可用“区域”。 

1. 选择“创建”  。

    > [!Note]
    > 创建新的 AKS 服务大约需要 15 分钟。 可以在“推理群集”页上查看预配状态 
    >

### <a name="deploy-the-real-time-endpoint"></a>部署实时终结点

在 AKS 服务完成预配以后，请返回到实时推理管道，以便完成部署。

1. 选择画布上面的“部署”。 

1. 选择“部署新的实时终结点”。  

1. 选择已创建的 AKS 群集。

1. 选择“部署”。 

    ![屏幕截图，显示如何设置新的实时终结点](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    部署完成后，会在画布上方显示一个成功通知，这可能需要数分钟的时间。

## <a name="test-the-real-time-endpoint"></a>测试实时终结点

可以导航到左侧工作区导航窗格中的“终结点”页，对实时终结点进行测试。 

1. 在“终结点”页上，选择已部署的终结点。 

    ![显示“实时终结点”选项卡的屏幕截图，其中突出显示了最近创建的终结点](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. 选择“测试”。 

1. 输入测试数据或使用自动填充的示例数据，然后选择“测试”。 

    测试请求将提交到终结点，结果将显示在页面上。 尽管为输入数据生成了价格值，但它不用于生成预测值。

    ![显示如何测试实时终结点的屏幕截图，其中突出显示了价格的评分标签](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍了在可视界面中创建、部署和使用机器学习模型的重要步骤。 若要详细了解如何使用可视界面来解决其他类型的问题，请查看其他示例管道。

> [!div class="nextstepaction"]
> [信用风险分类示例](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
