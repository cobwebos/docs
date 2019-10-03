---
title: 教程：使用可视界面预测汽车价格
titleSuffix: Azure Machine Learning
description: 了解如何使用拖放式可视界面来训练、评分和部署机器学习模型。 本教程是有关使用线性回归预测汽车价格的、由两个部分构成的教程系列的第一部分。
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 08/16/2019
ms.openlocfilehash: 11c65c217ef6c150c47f387f7f80070488a8df89
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996781"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>教程：使用可视界面预测汽车价格

本教程分为两部分，介绍如何使用 Azure 机器学习可视界面来开发和部署预测分析解决方案，以预测任何汽车的价格。 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

在第一部分中，你将设置环境，将数据集和分析模块拖放到交互式画布上，并将它们连接到一起以创建一个试验。 

本教程的第一部分介绍如何：

> [!div class="checklist"]
> * 创建新实验
> * 导入数据
> * 准备数据
> * 训练机器学习模型
> * 评估机器学习模型

在本教程的[第二部分](ui-tutorial-automobile-price-deploy.md)中，你将学习如何将预测模型部署为 Azure Web 服务，以便可以根据发送它的技术规范来预测任何汽车的价格。 

我们提供了本教程的已完成版本作为示例试验。

若要进行查找，请从“试验页”  中选择“新增”  ，然后选择  “示例 1 - 回归: 汽车价格预测(基本)”试验。

## <a name="create-a-new-experiment"></a>创建新实验

若要创建可视界面试验，首先需要 Azure 机器学习服务工作区。 本部分介绍如何创建这两个资源。

### <a name="create-a-new-workspace"></a>创建新的工作区

如果你有 Azure 机器学习工作区，请跳至下一部分。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="create-an-experiment"></a>创建试验

1. 在 [Azure 门户](https://portal.azure.com/)中打开你的工作区。

1. 在工作区中，选择“可视界面”。  然后选择“启动可视界面”。  

    ![Azure 门户的屏幕截图，其中显示了如何从机器学习服务工作区访问可视界面](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

1. 在可视界面窗口的底部选择“+新建”以创建新的试验。 

1. 选择“空白试验”。 

1. 在画布顶部选择默认试验名称“Experiment created on ...”，然后将它重命名为有意义的名称。  例如“汽车价格预测”。  名称不需唯一。

## <a name="import-data"></a>导入数据

机器学习依赖于数据。 幸运的是，此界面中包含多个样本数据集可供你进行试验。 在本教程中，可以使用示例数据集“汽车价格数据(原始)”  。 

1. 试验画布左侧是数据集和模块的控制板。 选择“保存的数据集”，然后选择“示例”，以便查看可用的示例数据集。  

1. 选择数据集“汽车价格数据(原始)”，然后将其拖到画布上。 

   ![将数据拖到画布上](./media/ui-tutorial-automobile-price-train-score/drag-data.gif)

1. 选择要处理哪些数据列。 在面板顶部的搜索框中键入“选择”，以查找“选择数据集中的列”   模块。

1. 单击“选择数据集中的列”模块，然后将其拖到画布上。  将该模块放在数据集模块下面。

1. 通过单击和拖动将先前添加的数据集连接到“选择数据集中的列”  模块。 从数据集的输出端口（画布上数据集底部的小圆圈）一直拖到“选择数据集中的列”  的输入端口（模块顶部的小圆圈）。

    > [!TIP]
    > 将一个模块的输出端口连接到另一个模块的输入端口时，即可通过试验创建数据流。
    >

    ![连接模块](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    红色感叹号表示尚未设置该模块的属性。

1. 选择“在数据集中选择列”模块。 

1. 在画布右侧的“属性”窗格中，选择“编辑列”。  

    在“选择列”对话框中选择“所有列”，并包括“所有功能”。    此对话框应如下所示：

     ![列选择器](./media/ui-tutorial-automobile-price-train-score/select-all.gif)

1. 在右下角，选择“确定”以关闭列选择器。 

### <a name="run-the-experiment"></a>运行试验

在任何时刻，单击数据集或模块的输出端口即可查看数据流中的数据在该时刻的情形。 如果“可视化”选项已禁用，则先需要运行此试验。 

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

在计算目标可用后，试验就会运行。 在运行完成后，每个模块上都会显示一个绿色的对勾标记。


### <a name="visualize-the-data"></a>可视化数据

运行了初始试验后，可以直观显示数据，以详细了解你使用的数据集。

1. 选择“在数据集中选择列”底部的输出端口，然后选择“可视化”。  

1. 单击数据窗口中的不同列，查看有关该列的信息。

    在此数据集中，每行代表一辆汽车，与每辆汽车关联的变量显示为列。 此数据集中有 205 行和 26 列。

    每次单击某个数据列时，就会在左侧显示该列的**统计**信息和**可视化**图像。

    [![预览数据](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)](./media/ui-tutorial-automobile-price-train-score/preview-data.gif#lightbox)

1. 单击每一列以了解有关数据集的更多信息，并考虑这些列对预测汽车价格是否有用。

## <a name="prepare-data"></a>准备数据

通常，数据集需要进行一些预处理才能进行分析。 在直观显示数据集时，你可能已经注意到某些值缺失。 需要清除这些缺失值，使模型能够正确分析数据。 将删除任何有缺失值的行。 另外，**normalized-losses** 列有大比例的缺失值，因此你将从模型中完全排除该列。

> [!TIP]
> 使用大多数模块时，都必须从输入数据中清除缺失值。

### <a name="remove-column"></a>删除列

首先，彻底删除 **normalized-losses** 列。

1. 选择“在数据集中选择列”模块。 

1. 在画布右侧的“属性”窗格中，选择“编辑列”。  

    * 让“使用规则”和“所有列”处于选中状态   。

    * 在下拉列表中，选择“排除”  和“列名称”  ，并在文本框内部单击。 键入“normalized-losses”。 

    * 在右下角，选择“确定”以关闭列选择器。 

    ![排除列](./media/ui-tutorial-automobile-price-train-score/exclude-column.gif)
        
    现在，“在数据集中选择列”的属性窗格指示它会传递数据集中除 **normalized-losses** 外的所有列。
        
    属性窗格显示 **normalized-losses** 列已排除。

1. 双击“在数据集中选择列”模块，键入注释“排除规范化的损失”。  
    
    键入注释后，在模块外单击。 此时会显示一个向下箭头，表明模块包含注释。

1. 单击向下箭头，显示注释。

    模块现在显示向上箭头，隐藏注释。
        
    ![注释](./media/ui-tutorial-automobile-price-train-score/comments.png)

### <a name="clean-missing-data"></a>清理缺失数据

训练模型时，必须对缺少的数据执行某些操作。 在本例中，你将添加一个模块以删除任何缺少数据的剩余行。

1. 在“搜索”框中键入“清理”，查找“清理缺失数据”模块。  

1. 将“清理缺失数据”模块拖到试验画布上，然后将其连接到“在数据集中选择列”模块。   

1. 在“属性”窗格中，选择“清理模式”下的“删除整个行”。  

1. 双击该模块并键入注释“删除缺失值行”。

    试验现在应该如下所示：
    
    ![选择列](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="train-a-machine-learning-model"></a>训练机器学习模型

数据准备就绪后，可以构造一个预测模型。 你将使用自己的数据来训练模型。 然后将测试模型，以确定它预测价格的准确性。

### <a name="select-an-algorithm"></a>选择一个算法

**分类** 和**回归** 是两种监督式机器学习算法。 **分类**可以从一组定义的类别预测答案，例如颜色（红、蓝或绿）。 **回归**用于预测数字。

由于你要预测价格（一个数字），因此可以使用回归算法。 本示例将使用线性回归模型。

### <a name="split-the-data"></a>拆分数据

将数据拆分为单独的训练数据集和测试数据集，用于模型训练和测试。

1. 在搜索框中键入“拆分数据”找到“拆分数据”模块，然后将其连接到“清理缺失数据”模块的左端口。   

1. 选择“拆分数据”  模块。 在“属性”窗格中，将“第一个输出集中的行部分”设置为 0.7。 这样，我们将使用 70% 的数据来训练模型，保留 30% 的数据用于测试。

1. 双击“拆分数据”并键入注释“将数据集拆分为训练集(0.7)和测试集(0.3)” 

### <a name="train-the-model"></a>训练模型

在模型中提供一组包含价格的数据以对其进行训练。 该模型会扫描数据，查找汽车特征与其价格之间的关联。

1. 若要选择学习算法，请清除模块控制板搜索框。

1. 依次展开“机器学习”、“初始化模型”。   此时会显示多个可用于初始化机器学习算法的模块类别。

1. 对于本试验，请选择“回归” > “线性回归”，并将该模块拖到试验画布上。  

1. 找到 **训练模型** 模块并将其拖到试验画布上。 将“线性回归”模块的输出连接到“训练模型”模块左侧的输入，将“拆分数据”模块的训练数据输出（左端口）连接到“训练模型”模块右侧的输入。  

    ![显示“训练模型”模块的正确配置的屏幕截图。 “线性回归”模块将连接到“训练模型”模块的左端口，“拆分数据”模块将连接到“训练模型”的右端口](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. 选择“训练模型”模块。  在“属性”窗格中选择“启动列选择器”，然后在“包括列名称”的旁边键入**价格**。  价格是模型要预测的值。

    ![显示列选择器模块的正确配置的屏幕截图。 “使用规则”>“包括列名称”>“价格”](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    试验应该如下所示：

    ![显示添加“训练模型”模块后试验的正确配置的屏幕截图。](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>评估机器学习模型

使用 70% 的数据训练模型后，可以使用该模型为另外 30% 的数据评分，确定模型的运行情况。

1. 在搜索框中键入“评分模型”找到“评分模型”模块，并将该模块拖到试验画布上。   将“训练模型”模块的输出连接到“评分模型”的左侧输入端口。   将 **拆分数据** 模型的测试数据输出（右端口）连接到 **评分模型** 的右侧输入端口。

1. 在搜索框中键入“评估”找到“评估模型”，并将该模块拖到试验画布上。   将“评分模型”模块的输出连接到“评估模型”的左侧输入。   最终试验看起来应与下图类似：

    ![显示试验的最终正确配置的屏幕截图。](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. 使用之前创建的计算资源运行试验。

1. 依次选择“评分模型”的输出端口和“可视化”来查看“评分模型”模块的输出。    输出显示价格预测值，以及来自测试数据的已知值。

    ![输出可视化效果的屏幕截图，其中突出显示了“评分标签”列](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. 要查看“评估模型”  模块的输出，请选择输出端口，然后选择“可视化”  。

    ![显示最终试验评估结果的屏幕截图。](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

针对模型显示了以下统计信息：

* **平均绝对误差(MAE)** ：绝对误差的平均值（误差是指预测值与实际值之间的差）。
* **均方根误差(RMSE)** ：对测试数据集所做预测的平均误差的平方根。
* **相对绝对误差**：相对于实际值与所有实际值平均值之间的绝对差异的绝对误差平均值。
* **相对平方误差**：相对于实际值与所有实际值平均值之间的平方差异的平方误差平均值。
* **决定系数**：也称为 R 平方值，这是一个统计指标，表示模型的数据拟合度。

每个误差统计值越小越好。 值越小，表示预测越接近实际值。 对于决定系数，其值越接近 1 (1.0)，预测就越精确。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本教程系列的第一部分，你已完成以下步骤：

* 已创建试验
* 准备数据
* 训练模型
* 评分和评估模型

第二部分介绍如何将模型部署为 Azure Web 服务。

> [!div class="nextstepaction"]
> [继续部署模型](ui-tutorial-automobile-price-deploy.md)
