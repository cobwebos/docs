---
title: 在门户中创建和浏览试验
titleSuffix: Azure Machine Learning service
description: 了解如何在门户中创建和管理自动化机器学习试验
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/02/2019
ms.openlocfilehash: 5eb3e94ff65e8a8b74f357a4cb8a517fd3837c5a
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871816"
---
# <a name="create-and-explore-automated-machine-learning-experiments-in-the-azure-portal-preview"></a>在 Azure 门户 (预览版) 中创建和探索自动化机器学习试验

 本文介绍如何在 Azure 门户中创建、运行和浏览自动化机器学习试验, 无需编写任何代码。 自动机器学习自动执行选择要用于特定数据的最佳算法的过程, 以便您可以快速生成机器学习模型。 [详细了解自动化机器学习](concept-automated-ml.md)。

 如果你更喜欢更多基于代码的体验, 还可以使用[AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)[在 Python 中配置自动化机器学习试验](how-to-configure-auto-train.md)。

## <a name="prerequisites"></a>系统必备

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

* Azure 机器学习服务工作区。 请参阅[创建 Azure 机器学习服务工作区](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)。

## <a name="get-started"></a>入门

导航到工作区的左窗格。 选择 "创作 (预览)" 部分下的 "自动机器学习。

![Azure 门户导航窗格](media/how-to-create-portal-experiments/nav-pane.png)

 如果这是你首次执行自动机器学习的试验, 将看到以下内容:

![Azure 门户试验登录页](media/how-to-create-portal-experiments/landing-page.png)

否则, 你将看到自动化机器学习仪表板, 其中概述了所有自动化机器学习试验, 其中包括使用 SDK 创建的实验。 在此处, 你可以按日期、实验名称和运行状态对运行进行筛选和浏览。

![Azure 门户试验面板](media/how-to-create-portal-experiments/dashboard.png)

## <a name="create-an-experiment"></a>创建试验

选择 "创建实验" 按钮, 以填充以下窗体。

![创建试验窗体](media/how-to-create-portal-experiments/create-exp-name-compute.png)

1. 输入实验名称。

1. 为数据事件探查和定型作业选择计算。 下拉列表中提供了现有计算的列表。 若要创建新计算, 请按照步骤3中的说明进行操作。

1. 选择 "创建新计算" 按钮以打开以下窗格, 并为此试验配置计算上下文。

    ![为试验创建新计算](media/how-to-create-portal-experiments/create-new-compute.png)

    字段|描述
    ---|---
    计算名称| 输入标识计算上下文的唯一名称。
    虚拟机大小| 为计算选择虚拟机大小。
    其他设置| *最小节点*:输入计算的最小节点数。 AML 计算的最小节点数为0。 若要启用数据事件探查, 必须有1个或多个节点。 <br> *最大节点*:输入计算的最大节点数。 默认值为用于 AML 计算的6个节点。

      若要开始创建新计算, 请选择 "**创建**"。 这可能需要几分钟的时间。

      >[!NOTE]
      > 你的计算名称将指示你选择/创建的计算是否已*启用分析*。 (有关数据分析的详细信息, 请参阅 7b)。

1. 为数据选择存储帐户。 公共预览版仅支持本地文件上传和 Azure Blob 存储帐户。

1. 选择存储容器。

1. 从存储容器中选择数据文件, 或将本地计算机上的文件上传到容器中。

    ![选择要试验的数据文件](media/how-to-create-portal-experiments/select-file.png)

1. 使用 "预览" 和 "配置文件" 选项卡进一步配置此试验的数据。

    1. 在 "预览" 选项卡上, 指示你的数据是否包括标题, 并使用每个功能列中的 "已**包含**" 按钮选择用于定型的功能 (列)。

        ![数据预览](media/how-to-create-portal-experiments/data-preview.png)

    1. 在 "配置文件" 选项卡上, 可以按功能查看[数据配置文件](#profile), 以及每个的分布、类型和摘要统计信息 (平均值、中间值、最大/最小值等)。

        ![数据配置文件选项卡](media/how-to-create-portal-experiments/data-profile.png)

        >[!NOTE]
        > 如果计算上下文未**启用分析,** 将显示以下错误消息:*数据事件探查仅适用于已在运行的计算目标*。

1. 选择定型作业类型: 分类、回归或预测。

1. 选择目标列。 要对其进行预测的列。

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

   ![高级设置窗体](media/how-to-create-portal-experiments/advanced-settings.png)

> [!NOTE]
> 有关字段的详细信息, 请单击信息工具提示。

<a name="profile"></a>

### <a name="data-profiling"></a>数据事件探查

你可以在数据集中获取各种摘要统计信息, 以验证你的数据集是否是 ML 就绪。 对于非数字列, 它们仅包含最小值、最大值和错误计数等基本统计信息。 对于数字列, 还可以查看其统计时间和估计分位数。 具体而言, 我们的数据配置文件包括:

* **功能**: 正在汇总的列的名称。

* **配置文件**: 基于所推断类型的行内可视化。 例如, 字符串、布尔值和日期具有值计数, 而小数 (数字) 则具有近似的直方图。 这使你可以快速了解数据的分布情况。

* **类型分配**: 列中的类型的行内值计数。 Null 是其自己的类型, 因此此可视化效果可用于检测奇值或缺失值。

* **类型**: 列的推断类型。 可能的值包括: 字符串、布尔值、日期和小数。

* **最**小值: 列的最小值。 对于类型不具有固有顺序 (例如布尔值) 的功能, 将显示空白项。

* **Max**: 列的最大值。 与 "最小值" 一样, 对于具有不相关类型的功能, 将显示空白条目。

* **Count**: 列中缺失和缺失条目的总数。

* **不缺少计数**: 列中不存在的条目数。 请注意, 空字符串和错误被视为值, 因此它们不会影响 "不缺少计数"。

* **分位数**(在0.1、1、5、25、50、75、95、99和 99.9% 时间间隔): 每个分位上的近似值都可提供数据分布。 对于具有不相关类型的功能, 将显示空白条目。

* **均值**: 列的算术平均值。 对于具有不相关类型的功能, 将显示空白条目。

* **标准偏差**: 列的标准偏差。 对于具有不相关类型的功能, 将显示空白条目。

* **方差**: 列的方差。 对于具有不相关类型的功能, 将显示空白条目。

* **偏斜度**: 列的偏斜度。 对于具有不相关类型的功能, 将显示空白条目。

* **峰值**: 列的峰值。 对于具有不相关类型的功能, 将显示空白条目。

<a name="preprocess"></a>

### <a name="advanced-preprocessing"></a>高级预处理

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

若要运行试验, 请单击 "启动"。 试验过程只需几分钟即可完成。

### <a name="view-experiment-details"></a>查看试验详细信息

试验准备阶段完成后, 你将看到 "运行详细信息" 屏幕。 这将提供创建的模型的完整列表。 默认情况下, 根据参数对最高分数进行评分的模型位于列表的顶部。 当定型作业尝试更多模型时, 它们将被添加到迭代列表和图表中。 使用迭代图可快速比较迄今为止生成的模型的指标。

训练作业可能需要一段时间才能完成每个管道的运行。

![运行详细信息仪表板](media/how-to-create-portal-experiments/run-details.png)

### <a name="view-training-run-details"></a>查看定型运行详细信息

向下钻取任何输出模型, 以查看定型运行详细信息, 如性能指标和分布图。 [了解有关图表的详细信息](how-to-track-experiments.md#understanding-automated-ml-charts)。

![迭代详细信息](media/how-to-create-portal-experiments/iteration-details.png)

## <a name="deploy-model"></a>部署模型

获得最佳模型后, 就可以将其部署为 web 服务, 以便预测新数据。

自动 ML 可帮助你在不编写代码的情况下部署模型:

1. 部署有几个选项。 
    1. 如果要根据为试验设置的指标条件部署最佳模型, 请从 "**运行详细信息**" 页中选择 "**部署最佳模型**"。

        !["部署模型" 按钮](media/how-to-create-portal-experiments/deploy-model-button.png)

    1. 如果要部署特定的模型迭代, 请向下钻取模型以打开其特定的 "运行详细信息" 页, 然后选择 "**部署模型**"。

        !["部署模型" 按钮](media/how-to-create-portal-experiments/deploy-model-button2.png)

1. 第一步是将模型注册到服务中。 选择 "注册模型" 并等待注册过程完成。

    ![部署模型边栏选项卡](media/how-to-create-portal-experiments/deploy-model-blade.png)

1. 注册模型后, 您将能够下载评分脚本 (scoring.py) 以及要在部署过程中使用的环境脚本 (condaEnv docker-compose.override.yml)。

1. 下载计分脚本和环境脚本时, 请在左侧导航窗格的 "**资产**" 边栏选项卡中, 选择 "**模型**"。

    ![导航窗格模型](media/how-to-create-portal-experiments/nav-pane-models.png)

1. 选择已注册的模型, 然后选择 "创建映像"。

    您可以通过其描述来确定模型, 其中包括运行 ID、迭代号, 格式如下: *< Run_ID > _ < Iteration_number > _Model*

    ![机型创建映像](media/how-to-create-portal-experiments/model-create-image.png)

1. 输入图像的名称。 
1. 选择 "计分文件" 框旁的 "**浏览**" 按钮, 上传先前下载的评分文件 (scoring.py)。

1. 选择 "Conda 文件" 框旁的 "**浏览**" 按钮, 上传先前下载的环境文件 (condaEnv)。

    你可以使用自己的评分脚本和 conda 文件, 还可以上传附加文件。 [了解有关评分脚本的详细信息](how-to-deploy-and-where.md#script)。

      >[!Important]
      > 文件名的长度必须为32个字符, 并且必须以字母数字开头和结尾。 可能包括短划线、下划线、点和之间的字母数字。 不允许使用空格。

    ![创建映像](media/how-to-create-portal-experiments/create-image.png)

1. 选择 "创建" 按钮, 开始创建映像。 这将需要几分钟才能完成, 完成后, 你会在顶部栏上看到一条消息。
1. 请在 "映像" 选项卡上, 选中要部署的映像旁边的复选框, 然后选择 "创建部署"。 [了解有关部署的详细信息](how-to-deploy-and-where.md)。

    有2个部署选项。
     + Azure 容器实例 (ACI)-此操作更用于测试目的, 而不是大规模的操作部署。 请确保为_CPU 预留容量_至少填写一个内核的值, 为_内存预留容量_至少填写一个千兆字节 (GB)
     + Azure Kubernetes 服务 (AKS))-此选项用于大规模部署。 你将需要准备好基于 AKS 的计算。

     ![幅创建部署](media/how-to-create-portal-experiments/images-create-deployment.png)

1. 完成后，选择“创建”。  部署模型可能需要几分钟时间才能完成运行。

1. 就这么简单！ 你具有可用于生成预测的操作 web 服务。

## <a name="next-steps"></a>后续步骤

* [了解有关自动化机器学习](concept-automated-ml.md)和 Azure 机器学习的详细信息。
* [了解如何使用 web 服务](https://docs.microsoft.com/azure/machine-learning/service/how-to-consume-web-service)。
