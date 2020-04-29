---
title: 使用 autoML 创建模型并部署
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习创建、查看和部署自动化机器学习模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/10/2020
ms.openlocfilehash: 0d6fa02578814c4c5d034be05cbc63093d70603b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81257222"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>使用 Azure 机器学习创建、查看和部署自动化机器学习模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

本文介绍如何在不编写任何代码的情况下，在 Azure 机器学习工作室界面中创建、探索和部署自动化机器学习模型。 自动化机器学习是一个为你选择要用于特定数据的最佳机器学习算法的过程。 通过此过程可以快速生成机器学习模型。 [详细了解自动化机器学习](concept-automated-ml.md)。
 
对于端到端示例，请试用[使用 Azure 机器学习的自动化 ML 界面创建分类模型教程](tutorial-first-experiment-automated-ml.md)。 

要获得 Python 基于代码的体验，请使用 Azure 机器学习 SDK [配置自动化机器学习试验](how-to-configure-auto-train.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。

* **企业版**类型的 Azure 机器学习工作区。 请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。  若要将现有的工作区升级到企业版，请参阅[升级到企业版](how-to-manage-workspace.md#upgrade)。

## <a name="get-started"></a>入门

1. 登录到 https://ml.azure.com 处的 Azure 机器学习。 

1. 选择你的订阅和工作区。 

1. 导航到左侧窗格。 在“创作”部分下，选择“自动化 ML”   。

[![Azure 机器学习工作室导航窗格](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 如果这是第一个进行试验，将会看到空列表和文档链接。 

否则，将看到最近进行的自动化机器学习试验列表，包括使用 SDK 创建的试验。 

## <a name="create-and-run-experiment"></a>创建并运行试验

1. 选择“+ 新建自动化 ML 运行”  并填充窗体。

1. 从存储容器中选择一个数据集，或创建新的数据集。 可以基于本地文件、Web URL、数据存储或 Azure 开放数据集创建数据集。 

    >[!Important]
    > 训练数据的要求：
    >* 数据必须为表格格式。
    >* 要预测的值（目标列）必须在数据中存在。

    1. 若要基于本地计算机上的文件创建新数据集，请选择“浏览”，然后选择该文件。  

    1. 为数据集指定唯一名称，并提供可选说明。 

    1. 选择“下一步”  ，以打开“数据存储和文件选择”  窗体。 在此窗体上，你需选择要将数据集上传到何处：将数据集上传到在工作区中自动创建的默认存储容器，或选择要用于试验的存储容器。 

    1. 在“设置和预览”窗体中检查准确度。  该窗体是基于文件类型智能填充的。 

        字段| 说明
        ----|----
        文件格式| 定义文件中存储的数据的布局和类型。
        分隔符| 一个或多个字符，用于指定纯文本或其他数据流中不同的独立区域之间的边界。
        编码| 指定字符架构表中用于读取数据集的位。
        列标题| 指示如何处理数据集的标头（如果有）。
        跳过行 | 指示要跳过数据集中的多少行（如果有）。
    
        选择“**下一步**”。

    1. “架构”窗体是基于“设置和预览”窗体中所做的选择智能填充的。   在此处，请配置每个列的数据类型，检查列名称，并选择“不包含”哪些列进行试验。  
            
        选择“下一步”。 

    1. “确认详细信息”窗体是前面在“基本信息”与“设置和预览”窗体中填充的信息的摘要。    你还可以使用已启用分析的计算来为数据集创建数据配置文件。 详细了解[数据分析](#profile)。

        选择“**下一步**”。
1. 新建的数据集出现后，请将其选中。 还可以查看数据集和样本统计信息的预览。 

1. 在“配置运行”窗体中，输入唯一的试验名称。 

1. 选择目标列；这是要对其进行预测的列。

1. 为数据分析和训练作业选择计算。 下拉列表中提供了现有计算的列表。 若要创建新的计算，请按照步骤 7 中的说明操作。

1. 选择“创建新计算”，以便为此试验配置计算上下文。 

    字段|说明
    ---|---
    计算名称| 输入用于标识计算上下文的唯一名称。
    虚拟机大小| 指定计算资源的虚拟机大小。
    最小/最大节点数（在“高级设置”中）| 若要分析数据，必须指定一个或多个节点。 输入计算的最大节点数。 对于 AML 计算，默认值为 6 个节点。
    
    选择“创建”  。 创建新计算可能需要花费几分钟时间。

    >[!NOTE]
    > 计算名称将会指示选择/创建的计算是否已启用分析。  （有关更多详细信息，请参阅[数据分析](#profile)部分）。

    选择“**下一步**”。

1. 在“任务类型和设置”窗体中选择任务类型：分类、回归或预测。  有关详细信息，请参阅[如何定义任务类型](how-to-define-task-type.md)。

    1. 对于分类，还可以启用用于文本特征化的深度学习。

    1. 对于预测：
        1. 选择时间列：此列包含要使用的时间数据。

        1. 选择预测边际：指示模型可以预测未来的多少个时间单位（分钟/小时/天/周/月/年）。 模型需要预测的未来时间越久远，其准确度越低。 [详细了解预测和预测边际](how-to-auto-train-forecast.md)。

1. （可选）查看附加配置设置：可用来更好地控制训练作业的其他设置。 否则，将会根据试验选择和数据应用默认设置。 

    其他配置|说明
    ------|------
    主要指标| 用于对模型进行评分的主要指标。 [详细了解模型指标](how-to-configure-auto-train.md#explore-model-metrics)。
    自动特征化| 选择此项可以启用或禁用自动化机器学习执行的预处理。 预处理包括自动化数据清理、准备和转换，以生成合成特征。 不支持时序预测任务类型。 [详细了解预处理](#featurization)。 
    解释最佳模型 | 此项用于允许或禁止显示建议最佳模型的解释
    阻止的算法| 选择要从训练作业中排除的算法。
    退出条件| 如果满足其中的任一条件，则会停止训练作业。 <br> *训练作业时间(小时)* ：允许训练作业运行多长时间。 <br> *指标评分阈值*：所有管道的最低指标评分。 这可以确保在你具有一个要实现的已定义目标指标时，无需花费不必要的时间来完成训练作业。
    验证| 选择要在训练作业中使用的交叉验证选项之一。 [详细了解交叉验证](how-to-configure-auto-train.md)。
    并发| *最大并发迭代数*：要在训练作业中测试的最大管道（迭代）数。 作业运行的迭代数不会超过指定的数目。

1. （可选）查看特征化设置：如果在“其他配置设置”窗体中选择启用“自动特征化”   ，则可以在此窗体中指定对哪些列执行那些特征化，并选择使用哪个统计值进行缺失值插补。

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>数据分析和摘要统计信息

可以获取整个数据集的各种摘要统计信息，以验证该数据集是否随时可在机器学习中使用。 对于非数字列，仅包括最小值、最大值和误差计数等基本统计信息。 对于数字列，还可以查看其统计时刻和估算的分位数。 具体而言，我们的数据配置文件包括：

>[!NOTE]
> 对于具有不相关类型的特征，将显示空白条目。

统计|说明
------|------
功能| 正在汇总的列的名称。
配置文件| 基于推理的类型显示的内联可视化效果。 例如，字符串、布尔值和日期包含值计数，而小数（数字）则包含近似的直方图。 这样，就可以快速了解数据的分布。
类型分布| 列中类型的内联值计数。 Null 是其自身的类型，因此，此可视化效果可用于检测反常值或缺失值。
类型|列的推理类型。 可能的值包括：字符串、布尔值、日期和小数。
Min| 列的最小值。 对于其类型不采用固有顺序（例如布尔值）的特征，将显示空白条目。
Max| 列的最大值。 
计数| 列中缺失和未缺失条目的总数。
非缺失计数| 列中未缺失的条目数。 空字符串和误差被视为值，因此它们不会计入“未缺少计数”。
分位数| 每个分位数中的近似值，用于提供数据分布的概观。
平均值| 列的算术中间值或平均值。
标准偏差| 此列数据的离散量或差异量的度量。
Variance| 此列数据与其平均值之间的分散程度度量。 
倾斜| 此列数据与正态分布之间的差异程度度量。
峰度| 此列数据与正态分布相比的落后程度度量。

<a name="featurization"></a>

## <a name="advanced-featurization-options"></a>高级特征化选项

自动化机器学习提供自动预处理和数据护栏，可帮助你查明和管理数据的潜在问题。 

### <a name="preprocessing"></a>预处理

> [!NOTE]
> 如果计划将自动 ML 创建的模型导出到[ONNX 模型](concept-onnx.md)，则 ONNX 格式仅支持使用 * 指示的特征化选项。 详细了解如何[将模型转换为 ONNX](concept-automated-ml.md#use-with-onnx)。 

|预处理步骤| 说明 |
| ------------- | ------------- |
|删除高基数或无差异特征 * |从训练集和验证集中删除这些特征，包括所有值都缺失的特征、在所有行中具有相同值的特征，或基数极高的特征（例如，哈希、ID 或 GUID）。|
|归结缺失值 * |对于数字特征，将在列中插补平均值。<br/><br/>对于分类特征，将插补最常用值。|
|生成附加功能 * |对于日期时间功能：年、月、日、星期几、每年的哪一天、每个季度、每周的哪一天、小时、分钟、秒。<br/><br/>对于文本功能：基于获得单元语法、bi 语法和三字符语法的术语频率。|
|转换和编码 *|唯一值较少的数字特征将转换为分类特征。<br/><br/>对于低基数分类特征，将执行独热 (one-hot) 编码；对于高基数特征，将执行独热哈希编码。|
|单词嵌入|使用预先训练的模型将文本标记的向量转换为句子向量的文本特征化器。 文档中的每个单词的嵌入向量聚合在一起，以生成文档功能矢量。|
|目标编码|对于分类特征，将每个类别映射到回归问题的平均目标值，并映射到分类问题的每个类的类概率。 应用基于频率的加权和 k 折交叉验证，以减少稀疏数据类别导致的映射过度拟合与干扰。|
|文本目标编码|对于文本输入，将使用带有词袋的堆叠线性模型来生成每个类的概率。|
|证据权重 (WoE)|将 WoE 计算为分类列与目标列的关联度量。 它将计算为类内概率与类外概率的比的对数。 此步骤为每个类输出一个数字特征列，无需显式推算缺失值和处理离群值。|
|聚类距离|基于所有数字列训练 k 平均聚类模型。  输出 k 个新特征（每个聚类各一个新数字特征），包含每个样本与每个聚类质心之间的距离。|

### <a name="data-guardrails"></a>数据 guardrails

启用 "自动特征化" 或 "验证" 设置为 "自动" 时，将应用数据 guardrails。数据 guardrails 有助于识别数据的潜在问题（例如缺少值、类不平衡），并帮助采取纠正措施来改善结果。 

用户可以在自动 ML 运行的**data guardrails**选项卡中查看工作室中的数据 guardrails，或者在```show_output=True```使用 Python SDK 提交实验时进行设置。 

#### <a name="data-guardrail-states"></a>数据 Guardrail 状态

数据 guardrails 将显示以下三种状态之一： "已**通过**"、"已**完成**" 或 "**警报**"。

状态| 说明
----|----
Passed| 未检测到任何数据问题，不需要用户执行任何操作。 
完成| 更改已应用于你的数据。 我们鼓励用户查看自动 ML ML 进行的纠正操作，以确保所做的更改与预期的结果一致。 
触发| 检测到无法纠正的数据问题。 我们鼓励用户修订和解决问题。 

>[!NOTE]
> 以前版本的自动 ML 试验显示了第四种状态：已**修复**。 较新的试验将不显示此状态，并且显示**固定**状态的所有 guardrails 现在将显示为 "已**完成**"。   

下表描述了当前支持的数据 guardrails，以及用户在提交试验时可能会遇到的关联状态。

Guardrail|状态|触发器&nbsp;的&nbsp;条件
---|---|---
缺少功能值插补法 |**Passed** <br><br><br> **完成**| 在定型数据中未检测到缺少的功能值。 详细了解[缺失值插补法。](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> 在定型数据和数据估算中检测到缺少的功能值。
高基数功能处理 |**Passed** <br><br><br> **完成**| 已分析你的输入，但未检测到高基数功能。 了解有关[高基数功能检测的](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options)详细信息。 <br><br> 在输入中检测到了高基数功能，已对其进行处理。
验证拆分处理 |**完成**| *验证配置设置为 "auto"，定型数据包含的行**小于**20000 行。* <br> 训练模型的每个迭代都通过交叉验证进行验证。 了解有关[验证数据的](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data)详细信息。 <br><br> *验证配置已设置为 "auto"，定型**数据包含超过**20000 行。* <br> 输入数据已拆分为定型数据集和验证数据集，用于验证模型。
类平衡检测 |**Passed** <br><br><br><br> **触发** | 已对输入进行了分析，并在定型数据中平衡了所有类。 如果每个类在数据集中具有良好的表示形式，则将数据集视为均衡，如样本的数量和比率而衡量。 <br><br><br> 在输入中检测到不均衡类。 若要修复模型偏移，请解决平衡问题。 了解有关[不均衡数据的](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)详细信息。
内存问题检测 |**Passed** <br><br><br><br> **完成** |<br> 已分析选定的 {地平线，延迟，滚动窗口} 个值，但未检测到任何可能的内存不足问题。 了解有关时序[预测配置的](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment)详细信息。 <br><br><br>已分析选定的 {地平线，延迟，滚动窗口} 值，并且可能会导致实验用尽内存。 延迟或滚动窗口配置已禁用。
频率检测 |**Passed** <br><br><br><br> **完成** |<br> 分析时序，并将所有数据点与检测到的频率对齐。 <br> <br> 分析了时序，检测到与检测频率不一致的数据点。 这些数据点已从数据集中删除。 了解有关[时序预测的数据准备情况的](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)详细信息。

## <a name="run-experiment-and-view-results"></a>运行试验并查看结果

选择 "**完成**" 以运行试验。 试验准备过程可能需要长达 10 分钟的时间。 训练作业可能需要额外的 2 - 3 分钟才能完成每个管道的运行。

### <a name="view-experiment-details"></a>查看试验详细信息

此时将打开 "**运行详细**信息" 屏幕到 "**详细信息**" 选项卡。此屏幕将显示实验运行的摘要，包括运行号旁边的状态栏。 

“模型”选项卡包含按指标分数排序创建的模型列表****。 默认情况下，列表中首先显示评分最高的模型（评分根据所选指标给出）。 随着训练作业尝试更多模型，它们会添加到列表中。 使用此项可以快速比较目前为止生成的模型的指标。

[![运行详细信息仪表板](media/how-to-use-automated-ml-for-ml-models/run-details.png)](media/how-to-use-automated-ml-for-ml-models/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>查看训练运行详细信息

向下钻取任何已完成的模型，以查看定型运行详细信息，例如，在 "**模型详细信息**" 选项卡上运行度量值，或在 "**可视化效果**" 选项卡上查看性能图表。[详细了解图表](how-to-understand-automated-ml.md)

[![迭代详细信息](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>部署模型

获得最佳模型后，可将其部署为 Web 服务以根据新数据进行预测。

自动化 ML 可帮助你在不编写任何代码的情况下部署模型：

1. 可以使用多个部署选项。 

    + 选项1：若要部署最佳模型（根据定义的指标条件），请选择 "**详细信息**" 选项卡上的 "**部署最佳模型**" 按钮。

    + 选项2：若要从此试验部署特定模型迭代，请向下钻取模型以打开其 "**模型详细信息**" 选项卡，然后选择 "**部署模型**"。

1. 填充 "**部署模型**" 窗格。

    字段| 值
    ----|----
    名称| 输入部署的唯一名称。
    说明| 输入说明，以更清楚地指出此部署的用途。
    计算类型| 选择要部署的终结点类型： *Azure Kubernetes Service （AKS）* 或*azure 容器实例（ACI）*。
    计算名称| *仅适用于 AKS：* 选择要部署到的 AKS 群集的名称。
    启用身份验证 | 选择此项将允许基于令牌或基于密钥的身份验证。
    使用自定义部署资产| 若要上传自己的评分脚本和环境文件，请启用此功能。 [详细了解评分脚本](how-to-deploy-and-where.md#script)。

    >[!Important]
    > 文件名不能超过 32 个字符，并且必须以字母数字开头和结尾。 开头和结尾之间可以包含短划线、下划线、句点和字母数字。 不允许空格。

    “高级”菜单提供默认部署功能，例如[数据收集](how-to-enable-app-insights.md)和资源利用率设置。** 若要替代这些默认设置，请在此菜单中替代。

1. 选择“部署”。  完成部署可能需要大约 20 分钟。

现在，你已获得一个正常运行的、可以生成预测结果的 Web 服务！ 您可以通过从[Power BI 的内置 Azure 机器学习支持](how-to-consume-web-service.md#consume-the-service-from-power-bi)查询服务来测试预测。

## <a name="next-steps"></a>后续步骤

* [了解如何使用 Web 服务](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service)。
* [了解自动化机器学习结果](how-to-understand-automated-ml.md)。
* [详细了解自动化机器学习](concept-automated-ml.md)和 Azure 机器学习。
