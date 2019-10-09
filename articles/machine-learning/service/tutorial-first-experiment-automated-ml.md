---
title: 创建第一个自动化机器学习试验
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习的工作区登陆页面（预览版）中使用自动化机器学习训练和部署分类模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/26/2019
ms.openlocfilehash: 3ddd228488d8ba4adc6780db1f65fdb634291d3b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350504"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>教程：使用自动化机器学习创建第一个分类模型

本教程介绍如何在不编写任何代码行的情况下，通过工作区登陆页面（预览版）创建第一个自动化机器学习试验。 本示例将创建一个分类模型来预测某家金融机构的客户是否会认购定期存款产品。

利用自动机器学习，可以自动完成耗时的任务。 自动机器学习会快速循环访问算法和超参数的多个组合，以帮助你根据所选的成功指标找到最佳模型。

本教程介绍如何执行以下任务：

> [!div class="checklist"]
> * 创建 Azure 机器学习工作区。
> * 运行自动机器学习试验。
> * 查看试验详细信息。
> * 部署模型。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请创建一个[免费帐户](https://aka.ms/AMLFree)。

* 下载 [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) 数据文件。 **y** 列指示客户是否认购了定期存款产品，该列稍后在本教程中将标识为预测目标列。 

## <a name="create-a-workspace"></a>创建工作区

Azure 机器学习工作区是云中的基础资源，用于试验、训练和部署机器学习模型。 它将 Azure 订阅和资源组关联到服务中一个易于使用的对象。 

通过 Azure 门户创建工作区，该门户是用于管理 Azure 资源的基于 Web 的控制台。 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> 记下你的工作区和订阅   。 你将需要这些项才能确保在正确的位置创建试验。 

## <a name="create-and-run-the-experiment"></a>创建并运行试验

在工作区登陆页面中完成以下试验设置和运行步骤，该页面是包含用于为所有技能级别的数据科学实践者执行数据科学方案的机器学习工具的合并界面。 Internet Explorer 浏览器不支持工作区登陆页。

1. 登录到[工作区登陆页面](https://ml.azure.com/workspaceportal/)。

1. 选择创建的订阅和工作区。

1. 选择“开始”。 

1. 在左窗格的“创作”部分，选择“自动化 ML”   。

   由于这是你的第一次自动化 ML 试验，因此会看到“入门”屏幕。

   ![Azure 机器学习工作室](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. 选择“创建试验”。  

1. 输入以下试验名称：`my-1st-automl-experiment`

1. 选择“创建新计算”并配置计算目标。  计算目标是本地的或基于云的资源环境，用于运行训练脚本或托管服务部署。 对于此试验，我们使用基于云的计算。 

   字段 | 说明 | 教程的值
   ----|---|---
   计算名称 |用于标识计算上下文的唯一名称。|automl-compute
   虚拟机大小&nbsp;&nbsp;| 指定计算资源的虚拟机大小。|Standard_DS12_V2
   最小/最大节点数（在“高级设置”中）| 若要分析数据，必须指定一个或多个节点。|最小节点数：1<br>最大节点数：6

   >[!NOTE]
   >本教程使用对新计算资源创建的默认存储帐户和容器。 它们会自动在填充在窗体中。
    
1. 选择“创建”，获取计算目标。  

   **完成此操作需要数分钟的时间。** 

1. 创建完以后，从下拉列表中选择新的计算目标，然后选择“下一步”。 

1. 选择“从本地文件上传”  ，开始创建新数据集。 

    1. 选择“浏览”  。
    
    1. 选择本地计算机上的 bankmarketing_train.csv 文件  。 这是作为[必备组件](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)下载的文件。

    1. 为数据集指定唯一名称，并提供可选说明。 

    1. 在底部左侧选择“下一步”，将其上传到在创建工作区期间自动设置的默认容器  。 公共预览版仅支持本地文件上传。 
    
       上传完成后，系统会根据文件类型预先填充“设置和预览”窗体。 
       
    1. 验证“设置和预览”窗体是否已填充如下，然后选择“下一步”。  
        
        字段|说明| 教程的值
        ---|---|---
        文件格式|定义文件中存储的数据的布局和类型。| 带分隔符
        分隔符|一个或多个字符，用于指定纯文本或其他数据流中不同的独立区域之间的边界。&nbsp; |逗号
        编码|指定字符架构表中用于读取数据集的位。| UTF-8
        列标题| 指示如何处理数据集的标头（如果有）。| 所有文件都具有相同的标题
        跳过行 | 指示要跳过数据集中的多少行（如果有）。| 无
    
        ![“预览”选项卡中的配置](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

1. 选择“分类”作为预测任务。 

1. 选择“y”作为用于执行预测的目标列。  此列指示客户是否认购了定期存款产品。

1. 展开“高级设置”并按如下所示填充字段。  使用这些设置可以更好地控制训练作业。 否则，将会根据试验选择和数据应用默认设置。

   >[!NOTE]
   > 在本教程中，我们不会按迭代阈值设置指标分数或最大核心数， 也不会阻止对算法进行测试。
   
   高级设置&nbsp;|说明|教程的值&nbsp;&nbsp;
   ------|---------|---
   主要指标| 对机器学习算法进行度量时依据的评估指标。|AUC_weighted
   退出条件| 如果符合某个条件，则会停止训练作业。 |训练作业次数：&nbsp;&nbsp;5 <br> <br> 最大迭代次数：10
   预处理| 启用自动化机器学习执行的预处理。 这包括自动化数据清理、准备和转换，以生成合成特征。| 启用
   验证类型 | 选择交叉验证类型。|K 折交叉验证
   验证次数 | 测试次数。 | 2 个交叉验证 
   并发| 最大并发迭代次数。|5
   
1. 选择“启动”以运行试验。  在试验准备开始时，会显示一个包含状态消息的屏幕。

>[!IMPORTANT]
> 准备试验运行时，准备需要 **10-15 分钟**。 运行以后，**每个迭代还需要 2-3 分钟**。  
>
> 在生产环境中，你可能会走开一段时间。 但在本教程中，建议你开始浏览迭代结果，因为当其他迭代仍在运行的时候，这些迭代已经完成。 

##  <a name="explore-iteration-results"></a>浏览迭代结果

当试验正在进行时，该屏幕将使用完成时创建的不同迭代（模型）更新**迭代图表**和**迭代列表**。 默认情况下，迭代按指标评分排序。 对于本教程，列表中首先显示评分最高的模型（评分根据所选 **AUC_weighted** 指标给出）。

在等待所有试验迭代完成的时候，可以选择已完成迭代的“名称”，以便浏览其性能详细信息。  

下面显示了为每个迭代生成的图表和运行指标，例如精确度-召回率曲线、混淆矩阵、加权准确度分数，等等。 

![运行迭代详细信息](media/tutorial-1st-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>部署模型

在工作区登陆页中使用自动化机器学习可以通过几个步骤将最佳模型部署为 Web 服务。 部署是模型的集成，因此它可以对新数据进行预测并识别潜在的机会领域。 对于本试验，部署到 Web 服务意味着金融机构现已获得一个迭代和可缩放的 Web 解决方案，用于识别潜在的定期存款客户。 

运行完成以后，请导航回“迭代图表”和“迭代列表”详细信息页。   

在此试验上下文中，根据 **AUC_weighted** 指标，**VotingEnsemble** 被视为最佳模型。  我们将部署此模型，但请注意，部署需要大约 20 分钟才能完成。 部署过程需要几个步骤，包括注册模型、生成资源和为 Web 服务配置资源。

1. 选择右上角的“部署最佳模型”按钮。 

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

1. 转到 [Azure 门户](https://portal.azure.com//)。 导航到你的工作区，然后在“资产”窗格的左下角选择“部署”   。 

1. 选择要删除的部署，然后选择“删除”。  

1. 选择“继续”。 

### <a name="delete-the-resource-group"></a>删除资源组

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

在本自动化机器学习教程中，你已使用工作区登陆页面创建并部署了一个分类模型。 有关详细信息和后续步骤，请参阅以下文章：

> [!div class="nextstepaction"]
> [使用 Web 服务](how-to-consume-web-service.md)

+ 详细了解[预处理](how-to-create-portal-experiments.md#preprocess)。
+ 详细了解[数据分析](how-to-create-portal-experiments.md#profile)。
+ 详细了解[自动化机器学习](concept-automated-ml.md)。
+ 有关分类指标和图表的详细信息，请参阅[理解自动化机器学习结果](how-to-understand-automated-ml.md#classification)一文。

>[!NOTE]
> 此银行营销数据集是根据 [Creative Commons (CCO:Public Domain) 许可条款](https://creativecommons.org/publicdomain/zero/1.0/)提供的。 数据库各项内容中的任何权利是根据[数据库内容许可条款](https://creativecommons.org/publicdomain/zero/1.0/)在 [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset) 上授予的。 此数据集最初在 [UCI 机器学习数据库](https://archive.ics.uci.edu/ml/datasets/bank+marketing)中提供。<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez and P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, June 2014.
