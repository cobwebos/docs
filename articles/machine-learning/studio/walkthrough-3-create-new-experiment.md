---
title: "步骤 3：创建新机器学习实验 | Microsoft Docs"
description: "开发预测解决方案演练的步骤 3：在 Azure 机器学习工作室中创建新的训练实验。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 660e3c27-55ef-4c33-a4e9-dff4d1224630
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: a8f1764204740a8f5ef757e5e2ad63cfd43af150
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="walkthrough-step-3-create-a-new-azure-machine-learning-experiment"></a>演练步骤 3：创建新的 Azure 机器学习实验
这是演练的第三步，[在 Azure 机器学习中开发预测分析解决方案](walkthrough-develop-predictive-solution.md)

1. [创建机器学习工作区](walkthrough-1-create-ml-workspace.md)
2. [上传现有数据](walkthrough-2-upload-data.md)
3. **创建新试验**
4. [定型和评估模型](walkthrough-4-train-and-evaluate-models.md)
5. [部署 Web 服务](walkthrough-5-publish-web-service.md)
6. [访问 Web 服务](walkthrough-6-access-web-service.md)

- - -
本演练的下一步是在机器学习工作室中创建一个使用我们上传的数据集的实验。  

1. 在工作室中，单击窗口底部的“+新建”。
2. 选择“实验”，并选择“空白实验”。 

    ![创建新实验][0]

2. 选择画布顶部的默认实验名称，然后将它重命名为有意义的名称。

    ![重命名实验][5]
   
   > [!TIP]
   > 在“属性”窗格中填写实验的“摘要”和“说明”会是一个很好的做法。 这些属性提供了记录实验的机会，以便任何看到它的人都能理解目标和方法。
   > 
   > ![实验属性][6]
   > 
3. 在实验画布左侧的模块控制板中，展开“已保存的数据集”。
4. 找到在“我的数据集”下创建的数据集，并将其拖动到画布上。 此外还可以通过在控制板上方的“搜索”框中输入名称来查找数据集。  

    ![将数据集添加到实验][7]

## <a name="prepare-the-data"></a>准备数据
可以通过以下方法查看整个数据集的前 100 行数据和一些统计信息：单击数据集的输出端口（底部的小圆圈）并选择“可视化”。  

因为数据文件没有列标题，所以工作室提供了通用标题（Col1、Col2 等）。 好标题不是创建模型的关键，但它们使得处理实验中的数据变得更容易。 此外，当我们最终在 Web 服务中发布此模型时，标题将有助于识别服务用户的列。  

可以使用[编辑元数据][edit-metadata]模块来添加列标题。
可以使用[编辑元数据][edit-metadata]模块来更改与数据集关联的元数据。 在本例中，我们使用它来为列标题提供更友好的名称。 

若要使用[编辑元数据][edit-metadata]，请首先指定要修改的列（在本例中为所有列）。接下来，指定要对这些列执行的操作（在此情况下为更改列标题）。

1. 在模块控制板的“搜索”框中键入“元数据”。 [编辑元数据][edit-metadata]将出现在模块列表中。

2. 单击并将[编辑元数据][edit-metadata]模块拖动到画布上，并将其拖放到我们之前添加的数据集的下方。

3. 将数据集连接到[编辑元数据][edit-metadata]：单击数据集的输出端口（数据集底部的小圆圈），将其拖动到[编辑元数据][edit-metadata]的输入端口（模块顶部的小圆圈），然后释放鼠标按键。 即便是在画布上来回移动，数据集和模块仍保持连接。
   
   实验现在看起来应当与下图类似：  
   
   ![添加编辑元数据][1]
   
   红色感叹号表示尚未设置此模块的属性。 我们会在下一步完成该操作。
   
   > [!TIP]
   > 可以双击模块并输入文本，为模块添加注释。 这有助于快速查看模块在实验中的运行情况。 在本例中，请双击[编辑元数据][edit-metadata]模块，并输入注释“添加列标题”。 单击画布上的任意位置以关闭文本框。 若要显示注释，请单击模块上的向下箭头。
   > 
   > ![添加了注释的“编辑元数据”模块][8]
   > 
4. 选择[编辑元数据][edit-metadata]，并在画布右侧的“属性”窗格中，单击“启动列选择器”。

5. 在“选择列”对话框中，选择“可用列”中的所有行，并单击 > 以将其移动到“选定列”。
   此对话框应如下所示：

   ![其中选择了所有列的列选择器][2]

6. 单击“确定”复选标记。

7. 回到“属性”窗格中，查找“新列名称”参数。 在此字段中，输入数据集中 21 列的名称列表，以逗号分隔并按列排序。 可以从 UCI 网站上的数据集文档中获取列名称，或为了方便起见，也可以复制并粘贴以下列表：  
   
       Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   
   “属性”窗格将如下所示：
   
   ![编辑元数据的属性][3]

> [!TIP]
> 若要验证列标题，请运行实验（单击实验画布下方的“运行”）。 完成运行后（[编辑元数据][edit-metadata]上会出现一个绿色对号），单击[编辑元数据][edit-metadata]模块的输出端口，并选择“可视化”。 可以用同样的方式查看任何模块的输出，以通过实验查看数据的进度。
> 
> 

## <a name="create-training-and-test-datasets"></a>创建训练和测试数据集
我们需要一些用于训练模型的数据和一些用于测试模型的数据。
因此，在试验的下一步中，我们将数据集拆分为两个单独的数据集：一个用于训练模型，一个用于测试模型。

为此，我们使用[拆分数据][split]模块。  

1. 找到[拆分数据][split]模块，将其拖动到画布上，并将其连接到[编辑元数据][edit-metadata]模块。

2. 默认情况下，拆分比为 0.5，并且设置了“随机拆分”参数。 这意味着，随机的一半数据通过[拆分数据][split]模块的一个端口输出，另一半通过另一个端口输出。 可以调整这些参数，以及“随机种子”参数，以更改训练和测试数据之间的拆分。 在本例中，我们将其保持原样。
   
   > [!TIP]
   > **第一个输出数据集中行的分数**属性决定了通过*左*输出端口输出的数据量。 例如，如果将比率设置为 0.7，则 70% 的数据将通过左端口输出，30% 通过右端口输出。  
   > 
   > 

3. 双击[拆分数据][split]模块，并输入注释“训练/测试数据拆分 50%”。 

我们可以使用[拆分数据][split]模块的输出，但我们选择使用左输出作为训练数据，右输出作为测试数据。  

如[上一步骤](walkthrough-2-upload-data.md)中所述，将高信用风险错误分类为低的成本比将低信用风险错误分类为高的成本高五倍。 考虑到这一点，我们生成一个反映此成本函数的新数据集。 在新数据集中，每个高风险示例会复制五次，而每个低风险示例则不复制。   

我们可以使用 R 代码进行此复制：  

1. 找到[执行 R 脚本][execute-r-script]模块并将其拖动到实验画布上。 

2. 将[拆分数据][split]模块的左侧输出端口连接到[执行 R 脚本][execute-r-script]模块的第一个输入端口 ("Dataset1")。

3. 双击[执行 R 脚本][execute-r-script]模块，并输入注释“设置成本调整”。

4. 在“属性”窗格中，删除 **R 脚本**参数中的默认文本，并输入以下脚本：
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![“执行 R 脚本”模块中的 R 脚本][9]

我们需要对 [拆分数据][split]模块的每个输出执行相同的复制操作，以便训练和测试数据具有相同的成本调整。 执行此操作的最简单方法是：复制刚才生成的[执行 R 脚本][execute-r-script]模块，将其连接到[拆分数据][split]模块的另一个输出端口。

1. 右键单击 [执行 R 脚本][execute-r-script]模块，并选择“复制”。

2. 右键单击实验画布，并选择“粘贴”。

3. 将新模块拖放到位，然后将[拆分数据][split]模块的右侧输出端口连接到此新的[执行 R 脚本][execute-r-script]模块的第一个输入端口。 

4. 在画布底部，单击“运行”。 

> [!TIP]
> 执行 R 脚本模块的副本包含与原始模块相同的脚本。 在画布上复制和粘贴模块时，副本将保留原始文件的所有属性。  
> 
> 

我们的实验现在如下所示：

![添加拆分模块和 R 脚本][4]

有关在实验中使用 R 脚本的详细信息，请参阅[使用 R 扩展实验](extend-your-experiment-with-r.md)。

**下一步：[训练和评估模型](walkthrough-4-train-and-evaluate-models.md)**

[0]: ./media/walkthrough-3-create-new-experiment/create-new-experiment.png
[5]: ./media/walkthrough-3-create-new-experiment/rename-experiment.png
[6]: ./media/walkthrough-3-create-new-experiment/experiment-properties.png
[7]: ./media/walkthrough-3-create-new-experiment/add-dataset-to-experiment.png
[8]: ./media/walkthrough-3-create-new-experiment/edit-metadata-with-comment.png
[9]: ./media/walkthrough-3-create-new-experiment/execute-r-script.png
[1]: ./media/walkthrough-3-create-new-experiment/experiment-with-edit-metadata-module.png
[2]: ./media/walkthrough-3-create-new-experiment/select-columns.png
[3]: ./media/walkthrough-3-create-new-experiment/edit-metadata-properties.png
[4]: ./media/walkthrough-3-create-new-experiment/experiment.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

