---
title: 创建和浏览门户中的试验
titleSuffix: Azure Machine Learning service
description: 了解如何创建和管理自动化的机器学习试验中门户
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: a2a281fda9272fb794692becb0ca08f3cf791458
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990144"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>创建和浏览自动化的机器学习试验 （预览版） 在 Azure 门户中

 在本文中，您将学习如何创建、 运行和了解自动化的机器学习试验中而无需一行代码在 Azure 门户。 自动化的机器学习会自动选择最佳算法使用为特定数据，因此可以快速生成机器学习模型的过程。 [了解有关自动化的机器学习的详细信息](concept-automated-ml.md)。

 如果需要更多基于代码的体验，你也可以[配置自动的机器学习中 Python 的试验](how-to-configure-auto-train.md)与[Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

* Azure 机器学习服务工作区。 请参阅[创建 Azure 机器学习服务工作区](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)。

## <a name="get-started"></a>开始使用

导航到你的工作区的左窗格中。 在创作 （预览版） 部分下选择自动机器学习。

![Azure 门户中导航窗格](media/how-to-create-portal-experiments/nav-pane.png)

 如果这是你第一次执行任何使用自动化的机器学习试验，你将看到以下信息：

![Azure 门户试验登陆页面](media/how-to-create-portal-experiments/landing-page.png)

否则，你将看到自动机器学习仪表板与所有自动化机器学习试验，包括那些使用 SDK 创建的概述。 此处可以筛选和按日期浏览在运行过程中，试验名称，并运行状态。

![Azure 门户试验仪表板](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>创建试验

选择创建试验按钮以填充以下窗体。

![创建实验窗体](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. 输入试验名称。

1. 选择计算的数据事件探查和训练作业。 在下拉列表中提供了您现有的计算资源的列表。 若要创建新的计算，请执行步骤 3 中的说明。

1. 选择创建新的计算按钮以打开下方窗格中，并配置对于此试验计算上下文。

    ![创建新试验的计算](media/how-to-create-portal-experiments/create-new-compute.png)

    字段|描述
    ---|---
    计算名称| 输入用于标识您的计算上下文的唯一名称。
    虚拟机大小| 选择在计算的虚拟机大小。
    其他设置| *最小节点*:输入在计算节点的最小数目。 最小的 AML 计算节点数为 0。 若要启用数据事件探查，您必须具有一个或多个节点。 <br> *最大节点*:根据计算输入的最大节点数。 默认值为 6 个节点的 AML 计算。

      若要开始创建新计算，请选择**创建**。 这可能需要一些时间。

      >[!NOTE]
      > 计算名称将指出你选择/创建的计算是否*分析启用*。 （请参阅更多详细信息的 7b 数据事件探查）。

1. 选择你的数据的存储帐户。 公共预览版仅支持本地文件上传和 Azure Blob 存储帐户。

1. 选择一个存储容器。

1. 从存储容器，选择一个数据文件或上传到容器的文件从本地计算机。

    ![选择实验的数据文件](media/how-to-create-portal-experiments/select-file.png)

1. 使用预览和配置文件选项卡进一步配置你的数据对于此试验。

    1. 在预览选项卡上，指示是否你的数据包含标头，并选择特征 （列） 是用于定型**包含**每个功能列中切换按钮。

        ![数据预览](media/how-to-create-portal-experiments/data-preview.png)

    1. 在配置文件选项卡，你可以查看[数据配置文件](#profile)的功能，以及分发、 类型和摘要统计信息 （平均值、 中间值、 最大/最小值等） 的每个。

        ![数据配置文件选项卡](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > 如果计算上下文，将显示以下的错误消息**不**分析启用：*数据事件探查功能仅适用于已在运行的计算目标*。

1. 选择培训作业类型： 分类、 回归或预测。

1. 选择目标列。 其想要对预测的列。

1. 用于预测：
    1. 选择时间列：此列包含要使用的时间数据。
    1. 选择预测时间范围：指示多少个时间单位 （分钟/小时/天/周/月/年） 将该模型能够预测到将来的发展。 随着模型需要不太准确它将成为未来的预测。 [详细了解预测和预测即将推出](https://docs.microsoft.com/azure/machine-learning/service/how-to-auto-train-forecast#configure-experiment)。

1. （可选）高级设置： 可用于更好地控制训练作业的其他设置。

    高级设置|描述
    ------|------
    主要指标| 用于进行评分模型的主要度量。 [详细了解模型度量](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#explore-model-metrics)。
    退出条件| 当满足以下任意条件时，训练作业结束之前完全完成。 <br> *训练作业时间 （分钟）*:允许运行训练作业的时间。  <br> *最大迭代数*:管道 （迭代） 中，以便测试在训练作业的最大数目。 该作业将不会运行多个指定的迭代次数。 <br> *指标分数阈值*:所有管道的最小指标分数。 这可确保，如果您想要达到定义的目标度量值，则执行不花更多时间比实际所需的培训作业。
    预处理| 选择此选项可以启用或禁用通过自动的机器学习的预处理。 预处理包括自动数据清理、 准备，和转换以生成综合的功能。 [了解有关预处理](#preprocess)。
    验证| 选择一个要在训练作业中使用的交叉验证选项。 [详细了解交叉验证](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-auto-train#cross-validation-split-options)。
    并发| 选择你想要使用多核计算时使用的多核限制。
    已阻止的算法| 选择你想要从训练作业中排除的算法。

   ![高级的设置窗体](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> 字段的详细信息，请单击信息工具提示。

<a name="profile"></a>

### <a name="data-profiling"></a>数据事件探查

跨您的数据集以验证您的数据集是否是机器学习准备就绪，可以获取各种汇总统计信息。 对于非数字列，其中包括 min、 max 和错误计数等的仅基本统计信息。 对于数值列，您还可以查看其统计一点时间，估计分位数。 具体而言，我们的数据配置文件包括：

* **功能**： 这汇总的列的名称。

* **配置文件**： 在行中可视化效果基于推断出的类型。 例如，字符串、 布尔值和日期将具有值计数，而小数 （数字） 具有近似直方图。 这允许您快速了解数据的分布。

* **键入分发**： 类型列中的行中值计数。 Null 值是其自己的类型，因此此可视化效果可用于检测异常或缺失值。

* **类型**: 列的推断的类型。 可能的值包括： 字符串、 布尔值、 日期和小数位数。

* **最小值**: 列的最小值。 对于其类型不具有固有的排序 （例如布尔值） 的功能显示为空白条目。

* **最大**: 列的最大值。 如"最小值，"为空白条目出现在不相关类型的功能。

* **计数**： 缺少和非缺少列中的项的总数。

* **不会错过计数**: 列不是缺失的条目数。 请注意，空字符串和错误被视为值，因此它们不会构成"不缺少计数"。

* **分位数**（在 0.1、 1、 5、 25、 50、 75、 95、 99 和 99.9%的时间间隔）： 在每个分位来提供数据的分布的意义上的近似的值。 功能与不相关的类型显示为空白条目。

* **意味着**: 列的算术平均值。 功能与不相关的类型显示为空白条目。

* **标准偏差**： 列的标准偏差。 功能与不相关的类型显示为空白条目。

* **方差**： 列的方差。 功能与不相关的类型显示为空白条目。

* **不对称度**： 列的不对称度。 功能与不相关的类型显示为空白条目。

* **峰度**： 列的峰值。 功能与不相关的类型显示为空白条目。

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>高级预处理

在配置在实验时，可以启用高级的设置`Preprocess`。 因此，这样做意味着自动执行以下数据预处理和特征化步骤。

|预处理&nbsp;步骤| 描述 |
| ------------- | ------------- |
|删除较大的基数或者无差异的特征|请先删除这些从训练和验证集，包含所有缺失，相同的值之间的所有行或具有极高基数 （例如，哈希、 Id 或 Guid） 值中包括的功能。|
|估算缺失值|数字特征，输入与列中值的平均值。<br/><br/>分类特征、 输入与最常用的值。|
|生成其他特征|对于日期时间特征：年、月、日、星期、年日期、季、年周、小时、分钟、秒。<br/><br/>对于文本特征：根据获得、 双元语法和三元字符语法的字词频率。|
|转换和编码 |很少唯一值的数字功能会被转换为分类特征。<br/><br/>独热编码为较小的基数分类; 执行为高的基数，一个热的哈希编码。|
|词嵌入|文本特征化器，可将文本标记的向量转换到句子矢量使用预先训练的模型。 在文档中的每个单词嵌入向量被聚合在一起以生成文档特征向量。|
|目标编码|分类特征、 将映射与平均的目标值对于回归问题，并为每个类分类问题的类概率的每个类别。 基于频率的权重和 k-折叠交叉验证用于减少通过映射和干扰引起的稀疏数据类别中调整。|
|文本目标编码|文本输入具有包的单词的堆积线性模型用于生成每个类的概率。|
|证据 （出错） 的权重|计算为分类列与目标列的关联的度量值出错。 它的计算方式的类在 vs 扩展类的概率的比率的日志。 此步骤中输出每个类的一个数字特征列，并且无需显式用户输入缺失值和离群值处理方法。|
|分类距离|训练的 k 平均值聚类分析模型对所有数字列。  输出 k 新功能，每个群集，其中包含每个示例与每个群集的形心的距离的一个新数字功能。|

## <a name="run-experiment-and-view-results"></a>运行实验并查看结果

若要运行此试验，请单击开始。 实验准备过程需要几分钟的时间。

### <a name="view-experiment-details"></a>查看实验详细信息

实验准备阶段完成后，你将看到运行详细信息屏幕。 这样，您创建的模型的完整列表。 默认情况下，最高评分的模型基于您的参数列表的顶部。 如训练作业尝试出更多的模型，则将它们添加到迭代列表和图表中。 使用迭代图表以获取到目前为止生成的模型的快速度量值的比较。

训练作业可能需要每个管道完成运行一段时间。

![运行详细信息仪表板](media/how-to-create-portal-experiments/run-details.png)

### <a name="view-training-run-details"></a>查看培训运行详细信息

向下钻取的任何输出模型，以查看运行详细信息，如性能指标和分发图表的培训。 [了解有关图表的详细](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments#understanding-automated-ml-charts)。

![迭代详细信息](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-model"></a>部署模型

一旦您手头有最佳模型，就可以将其部署为 web 服务，以便预测新数据。

自动化机器学习可帮助您部署模型，而无需编写代码：

1. 有几个部署选项。 
    1. 如果你想要部署基于指标条件的最佳模型则设置对于试验，请选择**部署最佳模型**从**运行详细信息**页。

        ![部署模型按钮](media/how-to-create-portal-experiments/deploy-model-button.png)

    1. 如果你想要部署特定模型迭代，向下钻取模型以打开其特定的运行详细信息页，然后选择**部署模型**。

        ![部署模型按钮](media/how-to-create-portal-experiments/deploy-model-button2.png)

1. 第一步是注册到服务模型。 选择"注册模型"并等待完成注册过程。

    ![部署模型边栏选项卡](media/how-to-create-portal-experiments/deploy-model-blade.png)

1. 模型注册后，你将能够下载评分脚本 (scoring.py) 和要在部署期间使用的环境脚本 (condaEnv.yml)。

1. 评分脚本和环境脚本下载时，请转到**资产**边栏选项卡的左侧的导航窗格，然后选择**模型**。

    ![导航窗格模型](media/how-to-create-portal-experiments/nav-pane-models.png)

1. 选择注册时，该模型并选择"创建映像"。

    您可以通过其说明，将采用以下格式包括运行的 ID，迭代数来确定模型： *< Run_ID > _ < Iteration_number > _Model*

    ![模型:创建映像](media/how-to-create-portal-experiments/model-create-image.png)

1. 输入映像的名称。 
1. 选择**浏览**"评分文件"框中，若要上传之前下载的计分概要文件 (scoring.py) 旁边的按钮。

1. 选择**浏览**"Conda 文件"框中，若要上传之前下载的环境文件 (condaEnv.yml) 旁边的按钮。

    您可以使用自己的评分脚本和 conda 文件，以及上传其他文件。 [了解有关评分脚本的详细信息](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#script)。

      >[!Important]
      > 文件的名称必须在 32 个字符和必须开始和结尾的字母数字。 可能包括短划线、 下划线、 圆点和之间的字母数字。 不允许有空格。

    ![创建映像](media/how-to-create-portal-experiments/create-image.png)

1. 选择"创建"按钮以启动映像创建。 这将需要几分钟才能完成，完成后，将顶部栏上看到一条消息。
1. 转到"映像"选项卡，选中你想要部署的映像旁边的复选框并选择"创建部署"。 [了解有关部署的详细](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where)。

    有两个部署选项。
     + Azure 容器实例 (ACI)-这用于测试目的而不是在规模较大的操作部署更多使用。 请确保至少一个核心的值填充_CPU 保留容量_，并至少 1 千兆字节 (GB) 为_内存保留容量_
     + Azure Kubernetes 服务 (AKS)）-此选项适用于大规模的部署。 需要已准备好基于 AKS 计算。

     ![映像：创建部署](media/how-to-create-portal-experiments/images-create-deployment.png)

1. 完成后，选择“创建”。 部署模型可能需要几分钟时间完成运行每个管道。

1. 就这么简单！ 有一个操作的 web 服务来生成预测。

## <a name="next-steps"></a>后续步骤

* [了解有关自动化的机器学习的详细信息](concept-automated-ml.md)和 Azure 机器学习。
* [了解如何使用 web 服务](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service)。
