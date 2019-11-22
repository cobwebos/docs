---
title: 教程：使用设计器部署机器学习模型
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习设计器中生成预测分析解决方案。 通过使用拖放式模块定型、评分和部署机器学习模型。
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 724a38cb516e5689f817e9ddeaa867b17274971b
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932043"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>教程：使用设计器部署机器学习模型（预览版）
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

可以部署在[本教程第一部分](tutorial-designer-automobile-price-train-score.md)开发的预测模型供其他人使用。 在第一部分中，你已定型了模型。 现在，让我们基于用户输入生成新的预测。 本教程的此部分介绍如何：

> [!div class="checklist"]
> * 创建实时推理管道。
> * 创建推理群集。
> * 部署实时终结点。
> * 测试实时终结点。

## <a name="prerequisites"></a>先决条件

完成[教程的第一部分](tutorial-designer-automobile-price-train-score.md)，了解如何在设计器中训练机器学习模型并为其评分。

## <a name="create-a-real-time-inference-pipeline"></a>创建实时推理管道

若要部署管道，必须先将训练管道转换为实时推理管道。 此过程会删除训练模块，并为推理请求添加输入和输出。

### <a name="create-a-real-time-inference-pipeline"></a>创建实时推理管道

1. 在管道画布上方，选择“创建推理管道” > “实时推理管道”   。

    管道现在应如下所示： 

   ![显示做好部署准备后管道的预期配置的屏幕截图](./media/ui-tutorial-automobile-price-deploy/real-time-inference-pipeline.png)

    选择“创建推理管道”时，会发生一些事情： 
    
    * 训练的模型在模块调色板中存储为“数据集”模块。  可以在“我的数据集”下找到它。 
    * 将删除“训练模型”和“拆分数据”等训练模块。  
    * 保存的训练模型已添加回管道中。
    * 已添加“Web 服务输入”  和  “Web 服务输出”模块。 这些模块显示用户数据进入模型的位置，以及返回数据的位置。

    > [!NOTE]
    > *训练管道*保存在管道画布顶部的新选项卡下。 它也可作为已发布管道显示在设计器中。
    >

1. 选择“运行”，并使用在第一部分使用过的相同计算目标和试验  。

1. 选择“评分模型”模块。 

1. 在属性窗格中选择“输出” > “可视化”，验证模型是否仍有效。   此时会显示原始数据，以及预测的价格（“评分标签”）。

1. 选择“部署”。 

## <a name="create-an-inferencing-cluster"></a>创建推理群集

在显示的对话框中，可以从任何现有的 Azure Kubernetes 服务 (AKS) 群集中进行选择，以便部署模型。 如果没有 AKS 群集，可通过以下步骤创建一个。

1. 在显示的对话框中选择“计算”，转到“计算”页。  

1. 在导航功能区中，选择“推理群集” > “+ 新建”   。

    ![显示如何转到新的推理群集窗格的屏幕截图](./media/ui-tutorial-automobile-price-deploy/new-inference-cluster.png)

1. 在推理群集窗格中，配置新的 Kubernetes 服务。

1. 输入“aks-compute”作为“计算名称”   。
    
1. 对于“区域”，选择可用的邻近区域  。

1. 选择“创建”  。

    > [!NOTE]
    > 创建新的 AKS 服务大约需要 15 分钟。 可在“推理群集”页上查看预配状态  。
    >

## <a name="deploy-the-real-time-endpoint"></a>部署实时终结点

在 AKS 服务完成预配以后，请返回到实时推理管道，以便完成部署。

1. 选择画布上面的“部署”。 

1. 选择“部署新的实时终结点”。  

1. 选择已创建的 AKS 群集。

1. 选择“部署”。 

    ![屏幕截图，显示如何设置新的实时终结点](./media/ui-tutorial-automobile-price-deploy/setup-endpoint.png)

    部署完成后，将在画布上方显示成功通知。 这可能需要几分钟时间。

## <a name="test-the-real-time-endpoint"></a>测试实时终结点

部署完成后，可通过转到“终结点”页来测试实时终结点  。

1. 在“终结点”页上，选择已部署的终结点。 

    ![显示“实时终结点”选项卡的屏幕截图，其中突出显示了最近创建的终结点](./media/ui-tutorial-automobile-price-deploy/endpoints.png)

1. 选择“测试”。 

1. 可以手动输入测试数据或使用自动填充的示例数据，然后选择“测试”  。

    门户会将测试请求提交到终结点并显示结果。 尽管为输入数据生成了价格值，但它不用于生成预测值。

    ![显示如何测试实时终结点的屏幕截图，其中突出显示了价格的评分标签](./media/ui-tutorial-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何在设计器中创建、部署和使用机器学习模型的重要步骤。 若要详细了解如何使用设计器解决其他类型的问题，请查看其他示例管道。

> [!div class="nextstepaction"]
> [信用风险分类示例](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
