---
title: 教程：使用可视界面预测汽车价格
titleSuffix: Azure Machine Learning service
description: 了解如何使用拖放式可视界面来训练、评分和部署机器学习模型。 本教程是有关使用线性回归预测汽车价格的、由两个部分构成的教程系列的第一部分。
author: peterclu
ms.author: peterclu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/06/2019
ms.openlocfilehash: 21f5a2d93b708e93f124bd44177bb7852dfbd86a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67720518"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>教程：使用可视界面预测汽车价格

本教程详细介绍如何在 Azure 机器学习服务可视界面中开发预测解决方案。 本教程结束时，你将获得一个可以根据发送的技术规范预测任何汽车的价格的解决方案。

本教程的第一部分介绍如何：

> [!div class="checklist"]
> * 导入和清除数据
> * 训练机器学习模型
> * 评分和评估模型

本教程的[第二部分](ui-tutorial-automobile-price-deploy.md)介绍如何将预测模型部署为 Azure Web 服务。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GY]

我们提供了本教程的已完成版本作为示例试验。

若要进行查找，请从“试验页”  中选择“新增”  ，然后选择  “示例 1 - 回归: 汽车价格预测(基本)”试验。

## <a name="create-a-workspace"></a>创建工作区

如果你有一个 Azure 机器学习服务工作区，请跳至[下一部分](#open-the-visual-interface-webpage)。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="open-the-visual-interface-webpage"></a>打开可视界面网页

1. 在 [Azure 门户](https://portal.azure.com/)中打开你的工作区。

1. 在工作区中，选择“可视界面”。  然后选择“启动可视界面”。  

    ![Azure 门户的屏幕截图，其中显示了如何从机器学习服务工作区访问可视界面](./media/ui-tutorial-automobile-price-train-score/launch-ui.png)

## <a name="create-your-first-experiment"></a>创建第一个试验

此可视界面工具提供了一个交互式的可视场所，用于生成预测分析模型。 将数据集和分析模块拖放到交互式画布上，并将它们连接到一起以创建一个试验  。

1. 在可视界面窗口的底部选择“+新建”以创建新的试验。 

    ![添加新试验](./media/ui-tutorial-automobile-price-train-score/add-new.png)

1. 选择“空白试验”。 

1. 在画布顶部选择默认试验名称“... 创建的试验”，然后将它重命名为有意义的名称。  例如“汽车价格预测”。  名称不需唯一。

## <a name="add-data"></a>添加数据

需要用于机器学习的首先是数据。 可以使用本界面中包含的多个示例数据集。 还可以从现有源导入数据。 在本教程中，可以使用示例数据集“汽车价格数据(原始)”  。 

1. 试验画布左侧是数据集和模块的控制板。 选择“保存的数据集”，然后选择“示例”，以便查看可用的示例数据集。  

1. 选择数据集“汽车价格数据(原始)”，然后将其拖到画布上。 

   ![将数据拖到画布上](./media/ui-tutorial-automobile-price-train-score/drag-data.png)

## <a name="select-columns"></a>选择列

选择要处理哪些数据列。 若要开始操作，请将模块配置为显示所有可用列。

> [!TIP]
> 如果知道所需数据或模块的名称，请使用调色板顶部的搜索栏进行快速查找。 本教程的其余部分将使用此快捷方式。


1. 在“搜索”框中键入“选择”，以便查找“在数据集中选择列”模块。  

1. 单击“在数据集中选择列”，然后将其拖到画布上。  将模块拖到此前添加的数据集下方。

1. 将数据集连接到“在数据集中选择列”：  单击数据集的输出端口，将其拖到“在数据集中选择列”的输入端口，然后放开鼠标按钮。  即便是在画布上来回移动，数据集和模块仍保持连接。

    > [!TIP]
    > 数据集和模块都有由小圆圈表示的输入和输出端口 - 输入端口位于顶部，输出端口位于底部。 将一个模块的输出端口连接到另一个模块的输入端口时，即可通过试验创建数据流。
    >

    ![连接模块](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

    红色感叹号表示尚未设置该模块的属性。

1. 选择“在数据集中选择列”模块。 

1. 在画布右侧的“属性”窗格中，选择“编辑列”。  

    在“选择列”对话框中选择“所有列”，并包括“所有功能”。    此对话框应如下所示：

     ![列选择器](./media/ui-tutorial-automobile-price-train-score/select-all.png)

1. 在右下角，选择“确定”以关闭列选择器。 

## <a name="run-the-experiment"></a>运行试验

在任何时刻，单击数据集或模块的输出端口即可查看数据流中的数据在该时刻的情形。 如果“可视化”选项已禁用，则先需要运行此试验。 

试验在计算目标上运行，该目标是附加到工作区的计算资源。 一旦创建了计算目标，就可以在以后的运行中重用它。

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

在计算目标可用后，试验就会运行。 在运行完成后，每个模块上都会显示一个绿色的对勾标记。


## <a name="preview-the-data"></a>预览数据

运行了初始试验后，可以直观显示数据，以详细了解需要处理的数据集。

1. 选择“在数据集中选择列”底部的输出端口，然后选择“可视化”。  

1. 单击数据窗口中的不同列，查看有关该列的信息。

    在此数据集中，每行代表一辆汽车，与每辆汽车关联的变量显示为列。 此数据集中有 205 行和 26 列。

     每次单击某个数据列时，就会在左侧显示该列的**统计**信息和**可视化**图像。 例如，单击 **num-of-doors** 时，会看到它有 2 个唯一值和 2 个缺失值。 向下滚动即可看到值：两个门和四个门。

     ![预览数据](./media/ui-tutorial-automobile-price-train-score/preview-data.gif)

1. 单击每一列以了解有关数据集的更多信息，并考虑这些列对预测汽车价格是否有用。

## <a name="prepare-data"></a>准备数据

数据集通常需要经过一定的预处理才能进行分析。 在直观显示数据集时，你可能已经注意到某些值缺失。 需要清除这些缺失值，使模型能够正确分析数据。 将删除任何有缺失值的行。 另外，**normalized-losses** 列有大比例的缺失值，因此你将从模型中完全排除该列。

> [!TIP]
> 使用大多数模块时，都必须从输入数据中清除缺失值。

### <a name="remove-column"></a>删除列

首先，彻底删除 **normalized-losses** 列。

1. 选择“在数据集中选择列”模块。 

1. 在画布右侧的“属性”窗格中，选择“编辑列”。  

    * 让“使用规则”和“所有列”处于选中状态   。

    * 在下拉列表中，选择“排除”  和“列名称”  ，并在文本框内部单击。 键入“normalized-losses”。 

    * 在右下角，选择“确定”以关闭列选择器。 

    ![排除列](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    现在，“在数据集中选择列”的属性窗格指示它会传递数据集中除 **normalized-losses** 外的所有列。
        
    属性窗格显示 **normalized-losses** 列已排除。
        
    ![属性窗格](./media/ui-tutorial-automobile-price-train-score/property-pane.png)
        
    可以双击模块并输入文本，为模块添加注释。 这有助于快速查看模块在实验中的运行情况。 

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

    这些选项指导“清理缺失数据”来清理数据，方法是删除有缺失值的行。 

1. 双击该模块并键入注释“删除缺失值行”。
 
    ![删除行](./media/ui-tutorial-automobile-price-train-score/remove-rows.png)

    试验现在应该如下所示：
    
    ![选择列](./media/ui-tutorial-automobile-price-train-score/experiment-clean.png)

## <a name="visualize-the-results"></a>可视化结果

由于对试验中的模块进行了更改，因此状态已变为“处于草稿状态”。  若要将新的干净数据可视化，先需再次运行试验。

1. 选择底部的“运行”，运行此试验。 

    这次可以重复使用此前创建的计算目标。

1. 选择对话框中的“运行”。 

   ![运行试验](./media/ui-tutorial-automobile-price-train-score/select-compute.png)

1. 当运行完成以后，右键单击“清理缺失数据”模块，将新的干净数据可视化。 

    ![可视化干净数据](./media/ui-tutorial-automobile-price-train-score/visualize-cleaned.png)

1. 单击已清理数据窗口中的不同列，查看数据的改变情况。

    ![可视化干净数据](media/ui-tutorial-automobile-price-train-score/visualize-result.png)

    现在有 193 行和 25 列。

    单击 **num-of-doors** 时，可以看到它仍有 2 个唯一值，但现在有 0 个缺失值。 通过单击浏览所有其余列以查看数据集中是否有缺失的值。 

## <a name="train-the-model"></a>训练模型

数据准备就绪后，可以构造一个预测模型。 你将使用自己的数据来训练模型。 然后将测试模型，以确定它预测价格的准确性。

**分类** 和**回归** 是两种监督式机器学习算法。 **分类**可以从一组定义的类别预测答案，例如颜色（红、蓝或绿）。 **回归**用于预测数字。

由于你要预测价格（一个数字），因此可以使用回归算法。 本示例将使用线性回归模型。

在模型中提供一组包含价格的数据以对其进行训练。 该模型会扫描数据，查找汽车特征与其价格之间的关联。 然后按以下方式测试模型：为模型提供一组熟悉的汽车特征，并确定该模型预测已知价格的准确性如何。

将数据拆分为单独的训练数据集和测试数据集，用于模型训练和测试。

1. 在搜索框中键入“拆分数据”找到“拆分数据”模块，然后将其连接到“清理缺失数据”模块的左端口。   

1. 选择刚刚连接到的“拆分数据”模块。  在“属性”窗格中，将“第一个输出集中的行部分”设置为 0.7。 这样，我们将使用 70% 的数据来训练模型，保留 30% 的数据用于测试。

    ![显示属性窗格的正确配置的屏幕截图。 “拆分数据”的值应是“拆分行”、“0.7”、“随机拆分”、“0”、“False”。](./media/ui-tutorial-automobile-price-train-score/split-data.png)

1. 双击“拆分数据”并键入注释“将数据集拆分为训练集(0.7)和测试集(0.3)” 

1. 若要选择学习算法，请清除模块控制板搜索框。

1. 依次展开“机器学习”、“初始化模型”。   此时会显示多个可用于初始化机器学习算法的模块类别。

1. 对于本试验，请选择“回归” > “线性回归”，并将该模块拖到试验画布上。  

    ![显示属性窗格的正确配置的屏幕截图。 “拆分数据”的值应是“拆分行”、“0.7”、“随机拆分”、“0”、“False”。](./media/ui-tutorial-automobile-price-train-score/linear-regression-module.png)

1. 找到“训练模型”模块并将其拖到试验画布上。  将“线性回归”模块的输出连接到“训练模型”模块左侧的输入，将“拆分数据”模块的训练数据输出（左端口）连接到“训练模型”模块右侧的输入。  

    ![显示“训练模型”模块的正确配置的屏幕截图。 “线性回归”模块将连接到“训练模型”模块的左端口，“拆分数据”模块将连接到“训练模型”的右端口](./media/ui-tutorial-automobile-price-train-score/train-model.png)

1. 选择“训练模型”模块。  在“属性”窗格中选择“启动列选择器”，然后在“包括列名称”的旁边键入**价格**。  价格是模型要预测的值。

    ![显示列选择器模块的正确配置的屏幕截图。 “使用规则”>“包括列名称”>“价格”](./media/ui-tutorial-automobile-price-train-score/select-price.png)

    试验应该如下所示：

    ![显示添加“训练模型”模块后试验的正确配置的屏幕截图。](./media/ui-tutorial-automobile-price-train-score/train-graph.png)

### <a name="run-the-training-experiment"></a>运行训练试验

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

## <a name="score-and-evaluate-the-model"></a>评分和评估模型

使用 70% 的数据训练模型后，可以使用该模型为另外 30% 的数据评分，确定模型的运行情况。

1. 在搜索框中键入“评分模型”找到“评分模型”模块，并将该模块拖到试验画布上。   将“训练模型”模块的输出连接到“评分模型”的左侧输入端口。   将“拆分数据”模型的测试数据输出（右端口）连接到“评分模型”的右侧输入端口。  

1. 在搜索框中键入“评估”找到“评估模型”，并将该模块拖到试验画布上。   将“评分模型”模块的输出连接到“评估模型”的左侧输入。   最终试验看起来应与下图类似：

    ![显示试验的最终正确配置的屏幕截图。](./media/ui-tutorial-automobile-price-train-score/final-graph.png)

1. 使用前面所用的同一计算目标运行试验。

1. 依次选择“评分模型”的输出端口和“可视化”来查看“评分模型”模块的输出。    输出显示价格预测值，以及来自测试数据的已知值。

    ![输出可视化效果的屏幕截图，其中突出显示了“评分标签”列](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. 要查看“评估模型”模块的输出，请选择输出端口，然后选择“可视化”。

    ![显示最终试验评估结果的屏幕截图。](./media/ui-tutorial-automobile-price-train-score/evaluate-result.png)

针对模型显示了以下统计信息：

* **平均绝对误差(MAE)** ：绝对误差的平均值（误差是指预测值与实际值之间的差）。
* **均方根误差(RMSE)** ：对测试数据集所做预测的平均误差的平方根。
* **相对绝对误差**：相对于实际值与所有实际值平均值之间的绝对差异的绝对误差平均值。
* **相对平方误差**：相对于实际值与所有实际值平均值之间的平方差异的平方误差平均值。
* **决定系数**：也称为 R 平方值，这是一个统计指标，表示模型的数据拟合度。

每个误差统计值越小越好。 值越小，表示预测越接近实际值。 对于决定系数，其值越接近 1 (1.0)，预测就越精确。

## <a name="manage-experiments-in-azure-machine-learning-service-workspace"></a>在 Azure 机器学习服务工作区中管理试验

可以通过 Azure 机器学习服务工作区管理在可视界面中创建的试验。 使用工作区可以查看个人试验运行、诊断日志、执行图等其他详细信息。

1. 在 [Azure 门户](https://portal.azure.com/)中打开你的工作区。  

1. 在工作区中选择“试验”。  然后选择创建的试验。

    ![显示如何在 Azure 门户中导航到试验的屏幕截图](./media/ui-tutorial-automobile-price-train-score/portal-experiments.png)

    此页会显示试验及其最新运行的概览。

    ![显示 Azure 门户中试验统计信息概览的屏幕截图](./media/ui-tutorial-automobile-price-train-score/experiment-overview.png)

1. 选择某个运行编号可以查看有关特定执行的更多详细信息。

    ![详细运行报告的屏幕截图](./media/ui-tutorial-automobile-price-train-score/run-details.png)

    运行报告会实时更新。 如果你在试验中使用了“执行 Python 脚本”模块，则可以在“日志”选项卡中指定要输出的脚本日志。  

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
