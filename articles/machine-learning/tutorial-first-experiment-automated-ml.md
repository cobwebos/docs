---
title: 创建第一个自动化 ML 试验
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习工作室中使用自动化机器学习训练和部署分类模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: c13b2a24147c542e36b78e8ae17af8e85414cd74
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75532470"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>教程：使用自动化机器学习创建第一个分类模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

本教程介绍如何在不编写任何代码行的情况下，通过 Azure 机器学习工作室创建第一个自动化机器学习试验。 本示例将创建一个分类模型来预测某家金融机构的客户是否会认购定期存款产品。

利用自动机器学习，可以自动完成耗时的任务。 自动机器学习会快速循环访问算法和超参数的多个组合，以帮助你根据所选的成功指标找到最佳模型。

本教程介绍如何执行以下任务：

> [!div class="checklist"]
> * 创建 Azure 机器学习工作区。
> * 运行自动机器学习试验。
> * 查看试验详细信息。
> * 部署模型。

## <a name="prerequisites"></a>必备条件

* Azure 订阅。 如果没有 Azure 订阅，请创建一个[免费帐户](https://aka.ms/AMLFree)。

* 下载 [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) 数据文件。 **y** 列指示客户是否认购了定期存款产品，该列稍后在本教程中将标识为预测目标列。 

## <a name="create-a-workspace"></a>创建工作区

Azure 机器学习工作区是云中的基础资源，用于试验、训练和部署机器学习模型。 它将 Azure 订阅和资源组关联到服务中一个易于使用的对象。 

通过 Azure 机器学习工作室创建工作区，该工作室是用于管理 Azure 资源的基于 Web 的控制台。

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

>[!IMPORTANT] 
> 记下你的工作区和订阅   。 你将需要这些项才能确保在正确的位置创建试验。 

## <a name="create-and-run-the-experiment"></a>创建并运行试验

在 Azure 机器学习工作室中完成以下试验设置和运行步骤，该工作室是包含用于为所有技能级别的数据科学实践者执行数据科学方案的机器学习工具的合并界面。 Internet Explorer 浏览器不支持此工作室。

1. 登录到 [Azure 机器学习工作室](https://ml.azure.com)。

1. 选择创建的订阅和工作区。

1. 选择“开始”。 

1. 在左窗格的“创作”部分，选择“自动化 ML”   。

   由于这是你的第一个自动化 ML 试验，因此会看到空列表和文档链接。

   ![Azure 机器学习工作室](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. 选择“新的自动化 ML 运行”  。 

1. 通过从“+ 创建数据集”  下拉菜单选择“从本地文件”  ，创建新的数据集。 

    1. 选择“浏览”  。
    
    1. 选择本地计算机上的 bankmarketing_train.csv 文件  。 这是作为[必备组件](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)下载的文件。

    1. 选择“表格”  作为数据集类型。 

    1. 为数据集指定唯一名称，并提供可选说明。 

    1. 在底部左侧选择“下一步”，将其上传到在创建工作区期间自动设置的默认容器  。  
    
       上传完成后，系统会根据文件类型预先填充“设置和预览”窗体。 
       
    1. 验证“设置和预览”窗体是否已填充如下，然后选择“下一步”。  
        
        字段|说明| 教程的值
        ---|---|---
        文件格式|定义文件中存储的数据的布局和类型。| 带分隔符
        分隔符|一个或多个字符，用于指定纯文本或其他数据流中不同的独立区域之间的边界。&nbsp; |逗号
        编码|指定字符架构表中用于读取数据集的位。| UTF-8
        列标题| 指示如何处理数据集的标头（如果有）。| 所有文件都具有相同的标题
        跳过行 | 指示要跳过数据集中的多少行（如果有）。| 无

    1. 通过“架构”窗体，可以进一步为此试验配置数据。  对于本示例，为 day_of_week 特征选择切换开关，以便在此试验中不包含在内  。 选择“**下一页**”。

        ![“预览”选项卡中的配置](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)

    1. 在“确认详细信息”  窗体上，确认信息与先前在“基本信息”  和“设置和预览”  窗体上填充的内容匹配。
    1. 选择“创建”  以完成数据集的创建。
    1. 当数据集出现在列表中时，则选择它。
    1. 查看“数据预览”  ，以确保未包括“day_of_week”  ，然后选择“确定”  。

    1. 选择“下一步”。 

1. 按如下所示填充“配置运行”  窗体：
    1. 输入以下试验名称：`my-1st-automl-experiment`

    1. 选择“y”作为用于执行预测的目标列。  此列指示客户是否认购了定期存款产品。
    1. 选择“创建新计算”并配置计算目标。  计算目标是本地的或基于云的资源环境，用于运行训练脚本或托管服务部署。 对于此试验，我们使用基于云的计算。 

        字段 | 说明 | 教程的值
        ----|---|---
        计算名称 |用于标识计算上下文的唯一名称。|automl-compute
        虚拟机大小&nbsp;&nbsp;| 指定计算资源的虚拟机大小。|Standard_DS12_V2
        最小/最大节点数（在“高级设置”中）| 若要分析数据，必须指定一个或多个节点。|最小节点数：1<br>最大节点数：6
  
        1. 选择“创建”，获取计算目标。  

            **完成此操作需要数分钟的时间。** 

        1. 创建后，从下拉列表中选择新的计算目标。

    1. 选择“**下一页**”。

1. 在“任务类型和设置”  窗体上，选择“分类”  作为机器学习任务类型。

    1. 选择“查看其他配置设置”  并按如下所示填充字段。 使用这些设置可以更好地控制训练作业。 否则，将会根据试验选择和数据应用默认设置。

        >[!NOTE]
        > 在本教程中，我们不会按迭代阈值设置指标分数或最大核心数， 也不会阻止对算法进行测试。
   
        其他配置&nbsp;|说明|教程的值&nbsp;&nbsp;
        ------|---------|---
        主要指标| 对机器学习算法进行度量时依据的评估指标。|AUC_weighted
        自动特征化| 启用预处理。 这包括自动化数据清理、准备和转换，以生成合成特征。| 启用
        阻止的算法 | 要从训练作业中排除的算法| 无
        退出条件| 如果符合某个条件，则会停止训练作业。 |训练作业时间（小时）：&nbsp;&nbsp;1 <br> 指标分数阈值：&nbsp;&nbsp;无
        验证 | 选择交叉验证类型和测试数。|验证类型：<br>k-折交叉验证&nbsp;&nbsp; <br> <br> 验证次数：2
        并发| 已执行并行迭代的最大次数和每次迭代使用的最大内核数| 最大并发迭代次数：&nbsp;&nbsp;5<br> 最大内核数/迭代：&nbsp;&nbsp;&nbsp;无
        
        选择“确定”  。

1. 选择“创建”  以运行试验。 当试验准备开始时，将打开“运行详细信息”  屏幕并显示“运行状态”  。

>[!IMPORTANT]
> 准备试验运行时，准备需要 **10-15 分钟**。
> 运行以后，**每个迭代还需要 2-3 分钟**。  
> 定期选择“刷新”  ，以查看实验过程中运行的状态。
>
> 在生产环境中，你可能会走开一段时间。 但在本教程中，建议你开始浏览“模型”选项卡上的已测试算法，因为当其他模型仍在运行的时候，这些模型已经完成。 

##  <a name="explore-models"></a>浏览模型

导航到“模型”  选项卡，以查看测试的算法（模型）。 默认情况下，这些模型在完成后按指标分数排序。 对于本教程，列表中首先显示评分最高的模型（评分根据所选 **AUC_weighted** 指标给出）。

在等待所有试验模型完成的时候，可以选择已完成模型的“算法名称”  ，以便浏览其性能详细信息。 

下面将浏览“模型详细信息”  和“可视化”  选项卡，以查看选定模型的属性、指标和性能图表。 

![运行迭代详细信息](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>部署模型

Azure 机器学习工作室中的自动化机器学习可以通过几个步骤将最佳模型部署为 Web 服务。 部署是模型的集成，因此它可以对新数据进行预测并识别潜在的机会领域。 

对于本试验，部署到 Web 服务意味着金融机构现已获得一个迭代和可缩放的 Web 解决方案，用于识别潜在的定期存款客户。 

运行完成后，导航回“运行详细信息”  页，然后选择“模型”  选项卡。选择“刷新”  。 

在此试验上下文中，根据 **AUC_weighted** 指标，**VotingEnsemble** 被视为最佳模型。  我们将部署此模型，但请注意，部署需要大约 20 分钟才能完成。 部署过程需要几个步骤，包括注册模型、生成资源和为 Web 服务配置资源。

1. 选择左下角的“部署最佳模型”按钮  。

1. 按如下所示填充“部署模型”窗格： 

    字段| 值
    ----|----
    部署名称| my-automl-deploy
    部署说明| 我的第一个自动化机器学习试验部署
    计算类型 | 选择“Azure 计算实例(ACI)”
    启用身份验证| 禁用。 
    使用自定义部署| 禁用。 允许自动生成默认驱动程序文件（评分脚本）和环境文件。 
    
    本示例使用“高级”菜单中提供的默认值。  

1. 选择“部署”。   

    “运行”屏幕的顶部会以绿色字体显示一条成功消息，“建议的模型”窗格中的“部署状态”下会显示一条状态消息。    定期选择“刷新”  以检查部署状态。
    
现在，你已获得一个正常运行的、可以生成预测结果的 Web 服务。 

转到[后续步骤  ](#next-steps)详细了解如何使用新的 Web 服务，以及如何使用 Power BI 的内置 Azure 机器学习支持来测试预测。

## <a name="clean-up-resources"></a>清理资源

部署文件比数据文件和试验文件更大，因此它们的存储成本也更大。 仅当你想要最大程度地降低帐户成本，或者想要保留工作区和试验文件时，才删除部署文件。 否则，如果你不打算使用任何文件，请删除整个资源组。  

### <a name="delete-the-deployment-instance"></a>删除部署实例

若要保留资源组和工作区以便在其他教程和探索中使用，请仅从 Azure 机器学习工作室中删除部署实例。 

1. 转到 [Azure 机器学习工作室](https://ml.azure.com/)。 导航到你的工作区，然后在“资产”  窗格的左下角选择“终结点”  。 

1. 选择要删除的部署，然后选择“删除”。  

1. 选择“继续”。 

### <a name="delete-the-resource-group"></a>删除资源组

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

在本自动化机器学习教程中，你已使用 Azure 机器学习工作室创建并部署了一个分类模型。 有关详细信息和后续步骤，请参阅以下文章：

> [!div class="nextstepaction"]
> [使用 Web 服务](how-to-consume-web-service.md#consume-the-service-from-power-bi)

+ 详细了解[预处理](how-to-create-portal-experiments.md#preprocess)。
+ 详细了解[数据分析](how-to-create-portal-experiments.md#profile)。
+ 详细了解[自动化机器学习](concept-automated-ml.md)。
+ 有关分类指标和图表的详细信息，请参阅[理解自动化机器学习结果](how-to-understand-automated-ml.md#classification)一文。

>[!NOTE]
> 此银行营销数据集是根据 [Creative Commons (CCO:Public Domain) 许可条款](https://creativecommons.org/publicdomain/zero/1.0/)提供的。 数据库各项内容中的任何权利是根据[数据库内容许可条款](https://creativecommons.org/publicdomain/zero/1.0/)在 [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset) 上授予的。 此数据集最初在 [UCI 机器学习数据库](https://archive.ics.uci.edu/ml/datasets/bank+marketing)中提供。<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez and P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, June 2014.
