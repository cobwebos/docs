---
title: 使用自动 ML 来构建和部署机器学习模型
titleSuffix: Azure Machine Learning service
description: 在 Azure 门户中创建、管理和部署自动化机器学习试验
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 08/02/2019
ms.openlocfilehash: eb6ae11bb4ffb39d9e9bcc692f17559fa2cde674
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720239"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>在 Azure 门户中创建、浏览和部署自动化机器学习试验 (预览版)

 本文介绍如何在 Azure 门户中创建、浏览和部署自动化机器学习试验, 无需编写任何代码。 自动机器学习自动执行选择要用于特定数据的最佳算法的过程, 以便您可以快速生成机器学习模型。 [详细了解自动化机器学习](concept-automated-ml.md)。

 如果你更喜欢更多基于代码的体验, 还可以使用[AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)[在 Python 中配置自动化机器学习试验](how-to-configure-auto-train.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

* Azure 机器学习服务工作区。 请参阅[创建 Azure 机器学习服务工作区](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)。

## <a name="get-started"></a>开始使用

导航到工作区的左窗格。 选择 "创作 (预览)" 部分下的 "自动机器学习。

![Azure 门户导航窗格](media/how-to-create-portal-experiments/nav-pane.png)

 如果这是你第一次执行任何试验, 将看到 "**欢迎使用自动机器学习**屏幕"。 

否则, 你将看到**自动化机器学习**仪表板, 其中概述了所有自动化机器学习试验, 其中包括使用 SDK 创建的实验。 在此处, 你可以按日期、实验名称和运行状态对运行进行筛选和浏览。

## <a name="create-an-experiment"></a>创建试验

选择 "**创建实验**", 并填充 "**创建新的自动机器学习试验**" 窗体。

1. 输入一个唯一的实验名称。

1. 为数据事件探查和定型作业选择计算。 下拉列表中提供了现有计算的列表。 若要创建新计算, 请按照步骤3中的说明进行操作。

1. 选择 "**创建新计算**", 为此试验配置计算上下文。

    字段|描述
    ---|---
    计算名称| 输入标识计算上下文的唯一名称。
    虚拟机大小| 为计算选择虚拟机大小。
    其他设置| *最小节点*:输入计算的最小节点数。 AML 计算的最小节点数为0。 若要启用数据事件探查, 必须有1个或多个节点。 <br> *最大节点*:输入计算的最大节点数。 默认值为用于 AML 计算的6个节点。

      选择“创建”。 创建新计算可能需要几分钟时间。

      >[!NOTE]
      > 你的计算名称将指示你选择/创建的计算是否已*启用分析*。 (有关数据分析的详细信息, 请参阅 7b)。

1. 为数据选择存储帐户。 

1. 选择存储容器。

1. 从存储容器中选择数据文件, 或将本地计算机上的文件上传到容器中。 公共预览版仅支持本地文件上传和 Azure Blob 存储帐户。

    [![选择数据文件](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. 使用 "预览" 和 "配置文件" 选项卡进一步配置此试验的数据。

    1. 在 "**预览**" 选项卡上, 指示你的数据是否包括标题, 并使用每个功能列中的 "已**包含**" 按钮选择用于定型的功能 (列)。

    1. 在 "**配置文件**" 选项卡上, 可以按功能查看[数据配置文件](#profile), 以及每个的分布、类型和摘要统计信息 (平均值、中间值、最大/最小值等)。

        >[!NOTE]
        > 如果计算上下文未启用分析, 将显示以下错误消息:*数据事件探查仅适用于已在运行的计算目标*。

1. 选择定型作业类型: 分类、回归或预测。

1. 选择目标列;这是要对其进行预测的列。

1. 对于预测:
    1. 选择时间列:此列包含要使用的时间数据。

    1. 选择预测范围:指示模型能够预测到未来的时间单位 (分钟/小时/天/周/月/年)。 需要进一步预测模型以预测未来, 它的准确性越低。 [了解有关预测和预测范围的详细信息](how-to-auto-train-forecast.md)。

1. 可有可无高级设置: 可用于更好地控制训练作业的其他设置。

    高级设置|描述
    ------|------
    主要指标| 用于对模型进行评分的主要指标。 [详细了解模型指标](how-to-configure-auto-train.md#explore-model-metrics)。
    退出条件| 满足这些条件中的任何一种时, 训练作业将在完成前结束。 <br> *训练作业时间 (分钟)* :允许定型作业运行多长时间。  <br> *最大迭代次数*:训练作业中要测试的管道的最大数目 (迭代)。 作业运行的次数不能超过指定的迭代次数。 <br> *指标分数阈值*:所有管道的最小指标分数。 这可以确保如果你有想要达到的已定义目标指标, 则不需要花费更多时间来完成培训作业。
    预处理| 选择此以启用或禁用自动机器学习完成的预处理。 预处理包括用于生成综合功能的自动数据清理、准备和转换。 [详细了解预处理](#preprocess)。
    验证| 选择要在训练作业中使用的交叉验证选项之一。 [了解有关交叉验证的详细信息](how-to-configure-auto-train.md)。
    并发| 选择要在使用多核计算时使用的多核限制。
    阻塞算法| 选择要从定型作业中排除的算法。

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>数据事件探查 & 摘要统计信息

你可以在数据集中获取各种摘要统计信息, 以验证你的数据集是否是 ML 就绪。 对于非数字列, 它们仅包含最小值、最大值和错误计数等基本统计信息。 对于数字列, 还可以查看其统计时间和估计分位数。 具体而言, 我们的数据配置文件包括:

>[!NOTE]
> 对于具有不相关类型的功能, 将显示空白条目。

统计信息|描述
------|------
功能| 正在汇总的列的名称。
配置文件| 基于推断类型的行内可视化。 例如, 字符串、布尔值和日期具有值计数, 而小数 (数字) 则具有近似的直方图。 这使你可以快速了解数据的分布情况。
类型分发| 列中类型的行内值计数。 Null 是其自己的类型, 因此此可视化效果可用于检测奇值或缺失值。
类型|推断列的类型。 可能的值包括: 字符串、布尔值、日期和小数。
最小值| 列的最小值。 对于类型不具有固有顺序 (例如布尔值) 的功能, 将显示空白项。
最大| 列的最大值。 
Count| 列中缺失和缺失条目的总数。
非缺失计数| 列中不存在的条目数。 空字符串和错误被视为值, 因此它们不会影响 "不缺少计数"。
分位数| 每个分位上的近似值用于提供数据的分布。
平均值| 列的算术平均值或平均值。
标准偏差| 度量此列数据的散射量或变体量。
Variance| 此列的数据超出其平均值的度量值。 
倾斜| 衡量此列的数据与正态分布的不同之处。
峰度| 对此列的数据与正态分布进行比较的尾量的度量值。

<a name="preprocess"></a>

## <a name="advanced-preprocessing-options"></a>高级预处理选项

配置试验时, 可以启用 "高级" 设置`Preprocess`。 这样做意味着会自动执行以下数据预处理和特征化步骤。

|预处理&nbsp;步骤| 描述 |
| ------------- | ------------- |
|删除较大的基数或者无差异的特征|从定型集和验证集删除这些值, 包括所有缺少值的特征、所有行中的相同值或基数极高的基数 (例如, 哈希、Id 或 Guid)。|
|估算缺失值|对于数值特征, 归结列中的值的平均值。<br/><br/>对于分类特征, 归结是最常用的值。|
|生成其他特征|对于日期时间特征：年、月、日、星期、年日期、季、年周、小时、分钟、秒。<br/><br/>对于文本特征：基于获得单元语法、bi 语法和三字符语法的术语频率。|
|转换和编码 |具有几个唯一值的数字特征会转换为分类特征。<br/><br/>对低基数分类执行一次热编码;用于高基数、一次热哈希编码。|
|Word 嵌入|文本特征化器, 它使用预先训练的模型将文本标记的矢量转换为句型向量。 文档中的每个单词的嵌入向量聚合在一起, 以生成文档功能矢量。|
|目标编码|对于分类功能, 将每个类别与回归问题的平均目标值一起映射, 并将每个类别的类概率映射到分类问题。 应用基于频率的权重和 k 折交叉验证, 以减少稀疏数据类别导致的映射和干扰。|
|文本目标编码|对于文本输入, 将使用带有词袋的堆积线性模型来生成每个类的概率。|
|证据的权重 (WoE)|计算 WoE 作为分类列与目标列的相关性。 它计算为类与类外概率的比率的对数。 此步骤将为每个类输出一个数值特征列, 无需显式归结缺失值和离群值处理。|
|群集距离|在所有数字列上训练 k 平均值聚类分析模型。  输出 k 个新功能, 每个群集一个新的数字功能, 其中包含每个样本与每个分类的质心的距离。|

## <a name="run-experiment-and-view-results"></a>运行试验并查看结果

选择 "**启动**" 以运行试验。 试验过程只需几分钟即可完成。

### <a name="view-experiment-details"></a>查看试验详细信息

试验准备阶段完成后, 你将看到 "运行详细信息" 屏幕开始填充。 此屏幕提供了创建的模型的完整列表。 默认情况下, 根据所选指标为最高评分的模型位于列表的顶部。 当定型作业尝试更多模型时, 它们将被添加到迭代列表和图表中。 使用迭代图可快速比较迄今为止生成的模型的指标。

训练作业可能需要一段时间才能完成每个管道的运行。

[![运行详细信息仪表板](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>查看定型运行详细信息

向下钻取任何输出模型, 以查看定型运行详细信息, 如性能指标和分布图。 [了解有关图表的详细信息](how-to-understand-automated-ml.md)。

![迭代详细信息](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-your-model"></a>部署模型

获得最佳模型后, 就可以将其部署为 web 服务, 以便预测新数据。

自动 ML 可帮助你在不编写代码的情况下部署模型:

1. 部署有几个选项。 

    + 选项 1：若要部署最佳模型 (根据定义的指标条件), 请从 "运行详细信息" 页中选择 "部署最佳模型"。

    + 选项 2：若要从此试验部署特定的模型迭代, 请向下钻取到模型以打开其运行详细信息页, 然后选择 "部署模型"。
1. 填充 "**部署模型**" 窗格,

    字段| 值
    ----|----
    部署名称| 输入部署的唯一名称。
    部署说明| 输入说明以更好地识别此部署的用途。
    评分脚本| 自动生成或上传自己的评分文件。 [详细了解计分脚本](how-to-deploy-and-where.md#script)
    环境脚本| 自动生成或上传自己的环境文件。
    >[!Important]
    > 文件名的长度必须为32个字符, 并且必须以字母数字开头和结尾。 可能包括短划线、下划线、点和之间的字母数字。 不允许使用空格。

1. 选择“部署”。 部署可能需要大约20分钟才能完成。

    部署成功完成后, 将显示以下消息。

    ![部署完成](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png) 

现在, 你已拥有一个可用于生成预测的操作 web 服务!

## <a name="next-steps"></a>后续步骤

* 尝试[通过 Azure 机器学习创建首次自动 ML 试验](tutorial-first-experiment-automated-ml.md)的端到端教程。 
* [了解有关自动化机器学习](concept-automated-ml.md)和 Azure 机器学习的详细信息。
* [了解自动化机器学习结果](how-to-understand-automated-ml.md)。
* [了解如何使用 web 服务](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service)。
