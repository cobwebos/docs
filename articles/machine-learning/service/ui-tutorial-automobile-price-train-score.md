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
ms.date: 10/22/2019
ms.openlocfilehash: 3852531615418ffe5397295bc194de34139d6e81
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792651"
---
# <a name="tutorial-predict-automobile-price-with-the-visual-interface"></a>教程：使用可视界面预测汽车价格

本教程分为两部分，介绍如何使用 Azure 机器学习可视界面来开发和部署预测分析解决方案，以预测任何汽车的价格。 

在第一部分，你将设置环境，将模块拖放到交互式画布上，并将它们连接到一起以创建一个 Azure 机器学习管道。

本教程的第一部分介绍如何：

> [!div class="checklist"]
> * 创建新管道
> * 导入数据
> * 准备数据
> * 训练机器学习模型
> * 评估机器学习模型

本教程的[第二部分](ui-tutorial-automobile-price-deploy.md)介绍如何将预测模型部署为 Azure Web 服务，以根据发送它的技术规范来预测任何汽车的价格。 

> [!Note]
>我们提供了本教程的已完成版本作为示例管道。
>
>若要查找此管道，请转到**工作区中的可视界面**。 在“新建管道部分，选择”示例 1 - 回归:   汽车价格预测(基本)”。

## <a name="create-a-new-pipeline"></a>创建新管道

Azure 机器学习管道可将多个相关的机器学习和数据处理步骤组织成单个资源。 管道可帮助你在不同的项目和用户之间组织、管理与重用复杂的机器学习工作流。 若要创建 Azure 机器学习管道，需要一个 Azure 机器学习服务工作区。 本部分介绍如何创建这两个资源。

### <a name="create-a-new-workspace"></a>创建新的工作区

如果你有一个 Azure 机器学习服务工作区，请跳至下一部分。

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="create-the-pipeline"></a>创建管道

1. 登录到 [ml.azure.com](https://ml.azure.com)，选择要使用的工作区。

1. 选择“可视界面”。 

    ![显示如何访问可视界面的可视工作区屏幕截图](./media/ui-tutorial-automobile-price-train-score/launch-visual-interface.png)

1. 选择“易用的预生成模块”。 

1. 在画布顶部选择默认管道名称“Pipeline-Created-on ...”，并将它重命名为有意义的名称。  例如“汽车价格预测”。  名称不需唯一。

## <a name="import-data"></a>导入数据

此可视界面中包含多个示例数据集可供你进行试验。 在本教程中，可以使用示例数据集“汽车价格数据(原始)”  。 

1. 管道画布左侧是数据集和模块的控制板。 选择“数据集”，然后查看“示例”部分以查看可用的示例数据集。  

1. 选择数据集“汽车价格数据(原始)”，然后将其拖到画布上。 

   ![将数据拖到画布上](./media/ui-tutorial-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>可视化数据

可将数据可视化以了解要使用的数据集。

1. 选择“汽车价格数据(原始)”模块。 

1. 在画布右侧的“属性”窗格中，选择“输出”。  

1. 选择图形图标以可视化数据。

    ![可视化数据](./media/ui-tutorial-automobile-price-train-score/visualize-data.png)

1. 选择数据窗口中的不同列，查看有关每个列的信息。

    每行代表一辆汽车，与每辆汽车关联的变量显示为列。 此数据集中有 205 行和 26 列。

## <a name="prepare-data"></a>准备数据

数据集通常需要在分析之前经过某种预处理。 在直观显示数据集时，你可能已经注意到某些值缺失。 需要清除这些缺失值，使模型能够正确分析数据。 删除缺失许多值的列，并删除缺失值的每个行。

### <a name="remove-a-column"></a>删除列

训练模型时，必须对缺失的数据执行某些操作。 在此数据集中，**normalized-losses** 列缺失许多值，因此需要从模型中完全排除该列。

1. 选择要处理哪些数据列。 在面板顶部的搜索框中键入“选择”，以查找“选择数据集中的列”   模块。

1. 单击“选择数据集中的列”模块，然后将其拖到画布上。  将该模块放在数据集模块下面。

1. 通过单击和拖动将先前添加的数据集连接到“选择数据集中的列”  模块。 从数据集的输出端口（画布上数据集底部的小圆圈）拖到“选择数据集中的列”  的输入端口（模块顶部的小圆圈）。

    > [!TIP]
    > 将一个模块的输出端口连接到另一个模块的输入端口时，即可通过管道创建数据流。
    >

    ![连接模块](./media/ui-tutorial-automobile-price-train-score/connect-modules.gif)

1. 选择“在数据集中选择列”模块。 

1. 在画布右侧的“属性”窗格中，选择“参数” > “编辑列”。   

1. 选择 **+** 以添加新规则。

1. 在下拉菜单中，选择“排除”和“列名”。  
    
1. 在文本框中输入 **normalized-losses**。

1. 在右下角，选择“保存”以关闭列选择器。 

    ![排除列](./media/ui-tutorial-automobile-price-train-score/exclude-column.png)
        
    属性窗格显示 **normalized-losses** 列已排除。

1. 选择“在数据集中选择列”模块。  

1. 在“属性”中，选择“参数” > “注释”并输入“排除规范化损失”。   

### <a name="clean-missing-data"></a>清理缺失数据

删除 **normalized-losses** 列后，数据集仍缺失值。 可以使用“清理缺失数据”模块来删除剩余的缺失数据。 

> [!TIP]
> 在可视界面中使用大多数模块时，都必须从输入数据中清除缺失值。

1. 在“搜索”框中键入“清理”，查找“清理缺失数据”模块。  

1. 将“清理缺失数据”模块拖到管道画布上，然后将其连接到“在数据集中选择列”模块。   

1. 在“属性”窗格中，选择“清理模式”下的“删除整个行”。  

1. 在“属性”窗格中，将“删除缺失值行”输入到 “注释”框中。   

    管道现在应如下所示：
    
    ![选择列](./media/ui-tutorial-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>训练机器学习模型

预处理数据后，可以构造一个预测模型。 你将使用自己的数据来训练模型。 然后将测试模型，以确定它预测价格的准确性。

### <a name="select-an-algorithm"></a>选择一个算法

**分类** 和**回归** 是两种监督式机器学习算法。 **分类**可以从一组定义的类别预测答案，例如颜色（红、蓝或绿）。 **回归**用于预测数字。

由于你要预测价格（一个数字），因此可以使用回归算法。 本示例将使用线性回归模型。

### <a name="split-the-data"></a>拆分数据

将数据拆分为两个单独的数据集，用于模型训练和测试。

1. 在搜索框中键入“拆分数据”找到“拆分数据”模块，然后将其连接到“清理缺失数据”模块的左端口。   

1. 选择“拆分数据”  模块。

1. 在“属性”窗格中，将“第一个输出集中的行部分”设置为 0.7。 

    这会使用 70% 的数据来训练模型，保留 30% 的数据用于测试。

1. 在“属性”窗格中，将“将数据集拆分为训练集(0.7)和测试集(0.3)”输入到 “注释”框中。 

### <a name="train-the-model"></a>训练模型

在模型中提供一组包含价格的数据以对其进行训练。 该模型会扫描数据，查找汽车特征与其价格之间的关联。

1. 若要选择学习算法，请清除模块控制板搜索框。

1. 展开“机器学习算法”。 
    
    此时会显示多个可用于初始化机器学习算法的模块类别。

1. 对于此管道，请选择“回归” > “线性回归”并将其拖到管道画布上。  

1. 找到“训练模型”模块并将其拖到管道画布上。  

1. 将“线性回归”模块的输出连接到“训练模型”模块的左侧输入。

1. 将“拆分数据”模块的训练数据输出（左侧端口）连接到“训练模型”模块的右侧输入。  

    ![显示“训练模型”模块的正确配置的屏幕截图。 “线性回归”模块将连接到“训练模型”模块的左端口，“拆分数据”模块将连接到“训练模型”的右端口](./media/ui-tutorial-automobile-price-train-score/pipeline-train-model.png)

1. 选择“训练模型”模块。 

1. 在“属性”窗格中，选择“编辑列”选择器。 

1. 在“标签列”对话框中展开下拉菜单，然后选择“列名”。   在文本框中输入“价格”。  价格是模型要预测的值。

    管道应如下所示：

    ![显示添加“训练模型”模块后管道的正确配置的屏幕截图。](./media/ui-tutorial-automobile-price-train-score/pipeline-train-graph.png)

## <a name="evaluate-a-machine-learning-model"></a>评估机器学习模型

使用 70% 的数据训练模型后，可以使用该模型为另外 30% 的数据评分，确定模型的运行情况。

1. 在搜索框中键入“评分模型”找到“评分模型”模块，并将该模块拖到管道画布上。   

1. 将 **训练模型** 模块的输出连接到 **评分模型** 的左侧输入端口。 将 **拆分数据** 模型的测试数据输出（右端口）连接到 **评分模型** 的右侧输入端口。

1. 在搜索框中键入“评估”找到“评估模型”，并将该模块拖到管道画布上。   

1. 将“评分模型”模块的输出连接到“评估模型”的左侧输入。   

    最终的管道应如下所示：

    ![显示管道的正确配置的屏幕截图。](./media/ui-tutorial-automobile-price-train-score/pipeline-final-graph.png)

### <a name="run-the-pipeline"></a>运行管道

[!INCLUDE [aml-ui-create-training-compute](../../../includes/aml-ui-create-training-compute.md)]

### <a name="view-results"></a>查看结果

运行完成后，可以查看管道运行的结果。 

1. 选择“评分模型”模块，查看“评分模型”模块的输出。  

1. 在“属性”窗格中，选择“输出” > “可视化”。    输出显示价格预测值，以及来自测试数据的已知值。

    ![输出可视化效果的屏幕截图，其中突出显示了“评分标签”列](./media/ui-tutorial-automobile-price-train-score/score-result.png)

1. 选择“评分模型”模块，查看“评估模型”模块的输出。  

1. 在“属性”窗格中，选择“输出” > “可视化”，然后选择“可视化”。    

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

* 已创建管道
* 已准备数据
* 已训练模型
* 已对模型进行评分和评估

第二部分介绍如何将模型部署为实时终结点。

> [!div class="nextstepaction"]
> [继续部署模型](ui-tutorial-automobile-price-deploy.md)
