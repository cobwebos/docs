---
title: 创建第一个自动化机器学习试验
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure 门户中使用自动化机器学习训练和部署分类模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 08/14/2019
ms.openlocfilehash: 01228dc01b8006a0a2476ddbbd6fa8ff430e280a
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982761"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>教程：使用自动化机器学习创建第一个分类模型

本教程介绍如何在不编写任何代码行的情况下，在 Azure 门户（预览版）中创建第一个自动化机器学习试验。 本示例将创建一个分类模型来预测某家金融机构的客户是否会认购定期存款产品。

利用自动机器学习，可以自动完成耗时的任务。 自动机器学习会快速循环访问算法和超参数的多个组合，以帮助你根据所选的成功指标找到最佳模型。

本教程介绍如何执行以下任务：

> [!div class="checklist"]
> * 创建 Azure 机器学习服务工作区。
> * 运行自动机器学习试验。
> * 查看试验详细信息。
> * 部署模型。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请创建一个[免费帐户](https://aka.ms/AMLFree)。

* 下载 [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) 数据文件。 **y** 列指示客户是否认购了定期存款产品，该列稍后在本教程中将标识为预测目标列。 

## <a name="create-a-workspace"></a>创建工作区

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-and-run-the-experiment"></a>创建并运行试验

这些步骤将引导你完成从选择数据到选择主要指标和模型类型的整个试验设置。 

1. 转到工作区的左窗格。 在“创作(预览版)”部分下选择“自动化机器学习”。  
此时会显示“欢迎使用自动化机器学习”屏幕，因为这是使用自动化机器学习创建的第一次试验。 

    ![Azure 门户导航窗格](media/tutorial-1st-experiment-automated-ml/nav-pane.png)

1. 选择“创建试验”。  然后输入 **my-1st-automl-experiment** 作为试验名称。

1. 选择“创建新计算”并为此试验配置计算上下文。 

    字段| 值
    ---|---
    计算名称| 输入用于标识计算上下文的唯一名称。 本示例使用 **automl-compute**。
    虚拟机大小| 指定计算资源的虚拟机大小。 我们使用了 **Standard_DS12_V2**。
    其他设置| *最小节点数*：1. 若要启用数据分析，必须有一个或多个节点。 <br> *最大节点数*：6. 

    若要创建新的计算资源，请选择“创建”  。 片刻时间即可完成此过程。 

    创建完成后，从下拉列表中选择新的计算资源，然后选择“下一步”。 

    >[!NOTE]
    >本教程使用对新计算资源创建的默认存储帐户和容器。 它们会自动在填充在窗体中。

1. 选择“上传”，并选择本地计算机中的“bankmarketing_train.csv”文件，以将其上传到默认容器。   公共预览版仅支持本地文件上传和 Azure Blob 存储帐户。 上传完成后，从列表中选择该文件。 

1. 在“预览”选项卡中可以进一步为此试验配置数据。 

    在“预览”选项卡上指定数据包含标头。  该服务默认包含用于训练的所有特征（列）。 对于本示例，请向右滚动并**忽略** **day_of_week** 特征。

    ![“预览”选项卡中的配置](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)

    >[!NOTE]
    > 数据分析不适用于最小节点数为零的计算资源。

1. 选择“分类”作为预测任务。 

1. 选择“y”作为用于执行预测的目标列。  此列指示客户是否认购了定期存款产品。

1. 展开“高级设置”并按如下所示填充字段。 

    高级设置|值
    ------|------
    主要指标| AUC_weighted 
    退出条件| 如果满足以下任意条件，训练作业将在完全完成之前结束： <br> *训练作业时间(分钟)* ：5  <br> *最大迭代次数*：10 
    预处理| 启用自动化机器学习执行的预处理。 这包括自动化数据清理、准备和转换，以生成合成特征。
    验证| 选择“K 折交叉验证”并选择“2”作为交叉验证次数。  
    并发| 选择“5”作为最大并发迭代次数。 

   >[!NOTE]
   > 对于本试验，我们不会设置指标或为每个迭代阈值的最大核心数。 我们也不会阻止算法的测试。

1. 选择“启动”以运行试验。 

   当试验启动时，你会看到一个空白的“运行详细信息”屏幕，其顶部显示了以下状态。 
      
试验准备过程需要花费几分钟时间。 该过程完成后，状态消息将更改为“运行正在运行”。 

##  <a name="view-experiment-details"></a>查看试验详细信息

当试验正在进行时，“运行详细信息”屏幕将使用运行的不同迭代（模型）更新迭代图表和列表。  迭代列表按指标评分顺序显示。 默认情况下，列表中首先显示评分最高的模型（评分根据 **AUC_weighted** 指标给出）。

>[!TIP]
> 训练作业将等待几分钟来让每个管道完成运行。

[![运行详细信息仪表板](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>部署模型

在 Azure 门户中使用自动化机器学习可将最佳模型部署为 Web 服务，以基于新数据进行预测，并识别出潜在的改进机会。 对于本试验，部署意味着金融机构现已获得一个迭代和可缩放的解决方案来识别潜在的定期存款客户。

在此试验上下文中，根据 **AUC_weighted** 指标，**VotingEnsemble** 被视为最佳模型。  我们将部署此模型，但请注意，部署需要大约 20 分钟才能完成。

1. 在“运行详细信息”页上，选择“部署最佳模型”按钮。  

1. 按如下所示填充“部署最佳模型”窗格： 

    字段| 值
    ----|----
    部署名称| my-automl-deploy
    部署说明| 我的第一个自动化机器学习试验部署
    评分脚本| 自动生成
    环境脚本| 自动生成
    
1. 选择“部署”。 

    部署成功完成后，将显示部署完成消息。
    
现在，你已获得一个正常运行的、可以生成预测结果的 Web 服务。

## <a name="clean-up-resources"></a>清理资源

部署文件比数据文件和试验文件更大，因此它们的存储成本也更大。 仅当你想要最大程度地降低帐户成本，或者想要保留工作区和试验文件时，才删除部署文件。 否则，如果你不打算使用任何文件，请删除整个资源组。  

### <a name="delete-the-deployment-instance"></a>删除部署实例

若要保留资源组和工作区以便在其他教程和探索中使用，请仅从 Azure 门户中删除部署实例。 

1. 转到左侧的“资产”窗格，选择“部署”。   

1. 选择要删除的部署，然后选择“删除”。  

1. 选择“继续”。 

### <a name="delete-the-resource-group"></a>删除资源组

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

在本自动化机器学习教程中，你已使用 Azure 门户创建并部署了一个分类模型。 有关详细信息和后续步骤，请参阅以下文章：

> [!div class="nextstepaction"]
> [使用 Web 服务](how-to-consume-web-service.md)

+ 详细了解[预处理](how-to-create-portal-experiments.md#preprocess)。
+ 详细了解[数据分析](how-to-create-portal-experiments.md#profile)。
+ 详细了解[自动化机器学习](concept-automated-ml.md)。

>[!NOTE]
> 此银行营销数据集是根据 [Creative Commons (CCO:Public Domain) 许可条款](https://creativecommons.org/publicdomain/zero/1.0/)提供的。 数据库各项内容中的任何权利是根据[数据库内容许可条款](https://creativecommons.org/publicdomain/zero/1.0/)在 [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset) 上授予的。 此数据集最初在 [UCI 机器学习数据库](https://archive.ics.uci.edu/ml/datasets/bank+marketing)中提供。<br><br>
> 使用时请引述以下内容： <br> [Moro et al., 2014] S. Moro, P. Cortez and P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, June 2014.
