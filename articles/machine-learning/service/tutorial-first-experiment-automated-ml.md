---
title: 创建第一个自动化机器学习试验
titleSuffix: Azure Machine Learning service
description: 了解如何在 Azure 门户中使用自动机器学习来训练和部署分类模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 07/23/2019
ms.openlocfilehash: 30dc731efdb6016f505b7a16860e0cb9c6480333
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716581"
---
# <a name="tutorial-use-automated-machine-learning-to-train-and-deploy-your-first-classification-model-preview"></a>教程：使用自动机器学习来训练和部署第一个分类模型 (预览)

在本教程中, 将了解如何在 Azure 门户中创建第一个自动化机器学习试验。 此示例将创建一个分类模型, 以预测客户端是否将订阅与银行一起使用的字词。 

通过使用服务的自动化机器学习功能和 Azure 门户, 你可以启动自动化机器学习过程, 并对你进行算法选择和超参数优化。 自动机器学习技术将循环访问算法和超参数的许多组合, 直到它基于您的条件找到最佳模型, 而无需编写单行代码。

本教程将介绍以下任务：

> [!div class="checklist"]
> * 配置 Azure 机器学习服务工作区。
> * 创建试验。
> * 自动为分类模型定型。
> * 查看定型运行详细信息。
> * 部署模型。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

* **Bankmarketing_train**数据文件。 可从[此处](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)下载。

## <a name="create-a-workspace"></a>创建工作区

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-an-experiment"></a>创建试验

1. 导航到工作区的左窗格。 选择 "**创作 (预览)** " 部分下的 "**自动机器学习**。

    ![Azure 门户导航窗格](media/tutorial-1st-experiment-automated-ml/nav-pane.png)

    因为这是自动机器学习的第一次试验, 你会看到 "**欢迎使用" 自动机器学习**屏幕。 

1.  选择 "**创建试验**"。 然后输入**automl-试验**作为实验名称。

1. 选择 "**创建新计算**" 并为此试验配置计算上下文。

    字段| ReplTest1
    ---|---
    计算名称| 输入标识计算上下文的唯一名称。 在此示例中, 我们使用**automl**。
    虚拟机大小| 为计算选择虚拟机大小。 我们使用**Standard_DS12_V2**。
    其他设置| *最小节点*:1. 若要启用数据事件探查, 必须有1个或多个节点。 <br> *最大节点*:6. 

    若要开始创建新计算, 请选择 "**创建**"。 这可能需要几分钟的时间。 

    创建完成后, 从下拉列表中选择新计算, 然后选择 "**下一步**"。

1. 对于本教程, 我们将使用新的计算创建的默认存储帐户和容器。 这会自动填充窗体。

1. 选择 "**上传**", 从本地计算机中选择**bankmarketing_train**文件, 并将其上传到默认容器。 公共预览版仅支持本地文件上传和 Azure Blob 存储帐户。 上传完成后, 从列表中选择文件。 

    [![选择数据文件](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. "**预览**" 选项卡允许我们为此试验进一步配置数据。

    在 "预览" 选项卡上, 指示数据包含标头。 服务默认为包含定型的所有功能 (列)。 在此示例中, 向右滚动并**忽略** **day_of_week**功能。

    ![预览选项卡配置](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)


    >[!NOTE]
    > 数据事件探查不适用于具有0个最小节点的计算。

1. 选择 "**分类**" 作为预测任务。

1. 选择**y**作为目标列, 即要对其进行预测的列。 此列指示客户端是否订阅了字词存款。

1. 展开 "**高级" 设置**, 并按如下所示填充字段。

    高级设置|ReplTest1
    ------|------
    主要指标| AUC_weighted 
    退出条件| 满足这些条件中的任何一种时, 训练作业将在完成前结束。 <br> *训练作业时间 (分钟)* :5  <br> *最大迭代次数*:10 
    预处理| 启用自动机器学习完成的预处理。 这包括自动进行数据清理、准备和转换以生成综合功能。
    验证| 选择 "K-折叠交叉验证", 并选择2表示交叉验证数。 
    并发| 对于 "最大并发迭代数", 选择5。

   >[!NOTE]
   > 对于此试验, 我们不会设置指标或最大迭代阈值, 也不会阻止测试算法。

1. 单击 "**启动**" 以运行试验。

   试验开始后, 会看到一个空白的 "**运行详细信息**" 屏幕, 其顶部显示以下状态。 试验过程只需几分钟即可完成。 准备过程完成后, 状态消息将更改为 "**正在运行**"。

      ![运行准备](media/tutorial-1st-experiment-automated-ml/run-preparing.png)

##  <a name="view-experiment-details"></a>查看试验详细信息

当试验时, "**运行详细信息**" 屏幕会将迭代图表和列表更新为运行的不同迭代 (模型)。 迭代列表按指标分数排序, 默认情况下, 根据我们的**AUC_weighted**指标为最高分数评分的模型位于列表的顶部。

>[!TIP]
> 训练作业可能需要几分钟时间才能完成运行。

[![运行详细信息仪表板](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-model"></a>部署模型

对于此试验, **VotingEnsemble**被视为基于**AUC_weighted**指标的最佳模型。 通过 Azure 门户中的自动化机器学习, 我们可以将此模型部署为 web 服务, 通过一次单击来预测新数据。 

1. 在 "**运行详细信息**" 页上, 选择 "**部署最佳模式**" 按钮。

1. 按如下所示填充 "**部署最佳模型**" 窗格:

    字段| ReplTest1
    ----|----
    部署名称| 我的 automl-部署
    部署说明| 我第一次自动机器学习试验部署
    评分脚本| 生成
    环境脚本| 生成
    
1. 选择“部署”。 部署可能需要大约20分钟才能完成。

    部署成功完成后, 将显示以下消息。

    ![部署完成](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png)
    
    就这么简单！ 你具有可用于生成预测的操作 web 服务。

## <a name="clean-up-resources"></a>清理资源

部署文件大于数据和试验文件, 因此它们的存储成本更高。 仅删除部署文件以最大程度减少帐户成本, 以及是否要保留工作区和试验文件。 否则, 如果不打算使用任何文件, 则删除整个资源组。  

### <a name="delete-deployment-instance"></a>删除部署实例

如果要保留资源组和工作区以获取其他教程和探索, 请从 Azure 门户中仅删除部署实例。 

1. 导航到左侧的 "**资产**" 窗格, 然后选择 "**部署**"。 

1. 选择要删除的部署, 然后选择 "**删除**"。 

1. 选择 "**继续**"。

### <a name="delete-resource-group"></a>删除资源组

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

在此自动机器学习教程中, 您使用了 Azure 门户创建和部署分类模型。 请参阅以下文章, 了解详细信息和后续步骤。

+ 如何[使用 web 服务](how-to-consume-web-service.md)。
+ 详细了解[预处理](how-to-create-portal-experiments.md#preprocess)。
+ 了解有关[数据分析](how-to-create-portal-experiments.md#profile)的详细信息。
+ 详细了解[自动化机器学习](concept-automated-ml.md)。

>[!NOTE]
> 此银行营销数据集可在[创造性 Commons (mosquera:公共域) 许可证](https://creativecommons.org/publicdomain/zero/1.0/)。 数据库的各个内容中的任何权限都在[数据库内容许可证](https://creativecommons.org/publicdomain/zero/1.0/)下获得授权, 并在[Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset)上提供。 此数据集最初在[UCI 机器学习数据库](https://archive.ics.uci.edu/ml/datasets/bank+marketing)中可用。<br><br>
>  请引用以下工作: <br> [Moro et al, 2014]Moro、Cortez 和 Rita。 一种数据驱动的方法, 用于预测银行电话销售是否成功。 决策支持系统, Elsevier, 62:22-31, 2014 年6月。
