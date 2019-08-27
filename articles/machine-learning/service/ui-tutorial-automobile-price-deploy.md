---
title: 教程：使用可视界面部署机器学习模型
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure 机器学习服务可视界面中生成预测分析解决方案。 使用拖放式模块训练、评分和部署机器学习模型。 本教程是有关使用线性回归预测汽车价格的、由两个部分构成的教程系列的第二部分。
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: 1e30650f932d15d23d7ffe7bd9b9fe07e9872511
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891621"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>教程：使用可视界面部署机器学习模型

为使其他人可以使用[教程的第一部分](ui-tutorial-automobile-price-train-score.md)开发的预测模型，可将它部署为 Azure Web 服务。 截至目前，我们一直在试验如何训练模型。 现在，让我们基于用户输入生成新的预测。 本教程部分介绍以下操作：

> [!div class="checklist"]
> * 准备好要部署的模型
> * 部署 Web 服务
> * 测试 Web 服务
> * 管理 Web 服务
> * 使用 Web 服务

## <a name="prerequisites"></a>先决条件

完成[教程的第一部分](ui-tutorial-automobile-price-train-score.md)，了解如何在可视界面中训练机器学习模型并为其评分。

## <a name="prepare-for-deployment"></a>准备部署

在将试验部署为 Web 服务之前，首先需将训练试验  转换成预测试验  。

1. 选择试验画布底部的“创建预测试验”*  。

    ![显示如何将训练试验自动转换为预测试验的 Gif 动画](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    选择“创建预测试验”时，会发生一些事情： 
    
    * 训练的模型在模块调色板中存储为“训练的模型”模块。  可以在“训练的模型”下找到它。 
    * 用于训练的模块被删除，具体包括：
      * 训练模型
      * 拆分数据
      * 评估模型
    * 保存的训练模型已添加回到试验中。
    * 已添加 **Web 服务输入**和 **Web 服务输出**模块。 这些模块标识用户数据进入模型的位置，以及返回数据的位置。

    **训练试验**仍保存在试验画布顶部的新选项卡下。

1. **运行** 试验。

1. 若要验证模型是否仍然正常工作，请选择“评分模型”模块的输出，然后选择“查看结果”。   此时会显示原始数据，以及预测的价格（“评分标签”）。

试验现在应如下所示：  

![显示做好部署准备后试验的预期配置的屏幕截图](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>部署 Web 服务

1. 选择画布下面的“部署 Web 服务”。 

1. 选择要运行 Web 服务的**计算目标**。

    目前，可视界面仅支持部署到 Azure Kubernetes 服务 (AKS) 计算目标。 可以从机器学习服务工作区内的可用 AKS 计算目标中进行选择，或者使用显示的对话框中的步骤配置新的 AKS 环境。

    ![显示新计算目标的可能配置的屏幕截图](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. 选择“部署 Web 服务”  。 部署完成后，将看到以下通知。 部署可能需要几分钟时间。

    ![显示成功部署确认消息的屏幕截图。](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>测试 Web 服务

可以导航到“Web 服务”选项卡，以便测试和管理可视界面 Web 服务。 

1. 转到“Web 服务”部分。 将会看到所部署的名为“教程 - 预测汽车价格 [预测开支]”的 Web 服务。 

     ![显示“Web 服务”选项卡的屏幕截图，其中突出显示了最近创建的 Web 服务](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. 选择 Web 服务名称可查看更多详细信息。

1. 选择“测试”。 

    [![显示 Web 服务测试页的屏幕截图](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)](./media/ui-tutorial-automobile-price-deploy/web-service-test.png#lightbox)

1. 输入测试数据或使用自动填充的示例数据，然后选择“测试”。 

    测试请求将提交到 Web 服务，结果将显示在页面上。 尽管为输入数据生成了价格值，但它不用于生成预测值。

## <a name="consume-the-web-service"></a>使用 Web 服务

用户现在可以将 API 请求发送到 Azure Web 服务并接收结果，以便预测新汽车的价格。

**请求/响应** - 用户可以使用 HTTP 协议向该服务发送一行或多行汽车数据。 该服务将使用一个或多个结果集做出响应。

可以在 Web 服务详细信息页的“使用”选项卡中找到示例 REST 调用。 

   ![显示用户可在“使用”选项卡中找到的示例 REST 调用的屏幕截图](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

导航到“API 文档”选项卡可以找到更多 API 详细信息。 

## <a name="manage-models-and-deployments"></a>管理模型和部署

也可通过 Azure 机器学习服务工作区管理在可视界面中创建的模型和 Web 服务部署。

1. 在 [Azure 门户](https://portal.azure.com/)中打开你的工作区。  

1. 在工作区中选择“模型”。  然后选择创建的试验。

    ![显示如何在 Azure 门户中导航到试验的屏幕截图](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    在此页上，可以看到有关该模型的其他详细信息。

1. 选择“部署”；随即会列出使用该模型的所有 Web 服务。  选择 Web 服务名称会转到 Web 服务详细信息页。 在此页中，可以获取 Web 服务的更多详细信息。

    [![详细运行报告的屏幕截图](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)](./media/ui-tutorial-automobile-price-deploy/deployment-details.png#lightbox)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍了在可视界面中创建、部署和使用机器学习模型的重要步骤。 若要详细了解如何使用可视界面来解决其他类型的问题，请查看其他示例试验。

> [!div class="nextstepaction"]
> [信用风险分类示例](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
