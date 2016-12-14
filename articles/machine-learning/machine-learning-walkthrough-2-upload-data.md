---
title: "步骤 2：将数据上传到机器学习实验 | Microsoft Docs"
description: "开发预测解决方案演练步骤 2：将存储的公共数据上传到 Azure 机器学习工作室。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 9f4bc52e-9919-4dea-90ea-5cf7cc506d85
ms.service: machine-learning
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e1911ff65f25d3220d057b6330eb1a2a69373f1d


---
# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>演练步骤 2：将现有数据载入 Azure 机器学习试验
这是演练的第二步，[在 Azure 机器学习中开发预测分析解决方案](machine-learning-walkthrough-develop-predictive-solution.md)

1. [创建机器学习工作区](machine-learning-walkthrough-1-create-ml-workspace.md)
2. **上载现有数据**
3. [创建新试验](machine-learning-walkthrough-3-create-new-experiment.md)
4. [定型和评估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [部署 Web 服务](machine-learning-walkthrough-5-publish-web-service.md)
6. [访问 Web 服务](machine-learning-walkthrough-6-access-web-service.md)

- - -
若要开发用于信贷风险的预测模型，我们需要用于训练和测试模型的数据。 对于本演练，我们使用 UCI 机器学习存储库的“UCI Statlog(德国信贷数据)数据集”。 可在此处找到以下内容：  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

我们使用名为“german.data”的文件。 将此文件下载到本地硬盘。  

此数据集包含 1000 个以前的信贷申请人的 20 个变量行。 这 20 个变量代表数据集的功能向量，此向量提供每个信贷申请人的标识特征。 每行中的额外列表示申请人经计算的信贷风险，700 个申请人标识为低信贷风险，300 个申请人标识为高风险。

UCI 网站介绍了功能向量的特征，包括金融信息、信贷历史记录、就业状态和个人信息。 每个申请人都将提供二进制分级，指示他们的信贷风险是高还是低。  

我们将使用此数据训练预测分析模型。 操作完成后，模型应能够接收新个人的信息，并预测他们的信贷风险是低还是高。  

下面是一个有趣的转折。 数据集描述介绍说，将实际上是高信贷风险的用户错误归类为低信贷风险，给金融机构造成的损失要比将低信贷风险的用户错误归类为高信贷风险用户高 5 倍。 在实验中顾及到这种情况的一个简单方法是重复表示高信贷风险用户的条目（5 次）。 如此一来，如果模型将高信贷风险错误归类为低信贷风险，它将错误归类 5 次，一次重复归类一次。 这将增加此错误在训练结果中的成本。  

## <a name="convert-the-dataset-format"></a>转换数据集格式
原始数据集使用空白分隔的格式。 机器学习工作室使用逗号分隔值 (CSV) 文件效果更好，所以我们通过将空格替换为逗号来转换数据集。  

转换此数据的方法有很多。 一种方法是使用以下 Windows PowerShell 命令：   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

另一种方法是使用 Unix sed 命令：  

    sed 's/ /,/g' german.data > german.csv  

在任一情况下，我们已在名为“german.csv”并且要在实验中使用的文件中创建了逗号分隔版的数据。

## <a name="upload-the-dataset-to-machine-learning-studio"></a>将数据集上传到机器学习工作室
数据转换为 CSV 格式后，我们需要将其上传到机器学习工作室。 有关机器学习工作室入门的详细信息，请参阅 [Microsoft Azure 机器学习工作室主页](https://studio.azureml.net/)。

1. 打开机器学习工作室 ([https://studio.azureml.net](https://studio.azureml.net))。 要求登录时，请使用以工作区所有者身份指定的帐户。
2. 单击窗口顶部的“工作室”选项卡。
3. 单击窗口底部的“+ 新建”。
4. 选择“数据集”。
5. 选择“从本地文件”。
6. 在“上传新数据集”对话框中，单击“浏览”，查找创建的“german.csv”文件。
7. 输入数据集名称。 对于本演练，我们称之为“UCI 德国信用卡数据”。
8. 对于数据类型，请选择“没有标题的一般 CSV 文件(.nh.csv)”。
9. 如果需要，可添加描述。
10. 单击“确定”。  

![上传数据集][1]  

这将数据上传到可在实验中使用的数据集模块。

> [!TIP]
> 若要管理已上传到工作室的数据集，请单击工作室窗口左侧的“数据集”选项卡。
> 
> 

有关将各种类型的数据导入实验的详细信息，请参阅[将训练数据导入 Azure 机器学习工作室](machine-learning-data-science-import-data.md)。

**下一步：[创建新实验](machine-learning-walkthrough-3-create-new-experiment.md)**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png



<!--HONumber=Nov16_HO3-->


