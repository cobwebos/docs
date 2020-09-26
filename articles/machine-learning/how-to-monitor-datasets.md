---
title: 分析和监视数据集中的数据偏移（预览版）
titleSuffix: Azure Machine Learning
description: 创建 Azure 机器学习数据集监视器（预览版）、监视数据集中的数据偏移并设置警报。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 8f54ece9a932ed4cc0adc29747e1c58ee22646c8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333862"
---
# <a name="detect-data-drift-preview-on-datasets"></a>检测数据集中的数据偏移（预览版）


> [!IMPORTANT]
> “检测数据集中的数据偏移”功能目前为公共预览版。
> 该预览版在提供时没有附带服务级别协议，建议不要将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

了解如何监视数据偏移并设置偏移幅度很大时的警报。  

Azure 机器学习数据集监视器（预览版）具有以下功能：
* **分析数据的偏移**，以了解数据在一段时间内的变化。
* **监视模型数据**，以了解训练数据集与服务数据集之间的差异。  首先[从部署的模型收集模型数据](how-to-enable-data-collection.md)。
* **监视新数据**，以了解任何基线与目标数据集之间的差异。
* **分析数据中的特征**，以跟踪统计属性在一段时间内的变化。
* **针对数据偏移设置警报**，以便针对潜在问题提前发出警告。 

使用 [Azure 机器学习数据集](how-to-create-register-datasets.md)来创建监视器。 此数据集必须包含一个时间戳列。

可以在 Python SDK 或 Azure 机器学习工作室中查看数据偏移指标。  可以通过与 Azure 机器学习工作区关联的 [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 资源获取其他指标和见解。

## <a name="prerequisites"></a>先决条件

若要创建和使用数据集监视器，需要：
* Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。
* [Azure 机器学习工作区](how-to-manage-workspace.md)。
* [已安装适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)，其中包含 azureml-datasets 包。
* 在数据中的文件路径、文件名或列中指定了带时间戳的结构化（表格）数据。

## <a name="what-is-data-drift"></a>什么是数据偏移？

数据偏移是模型准确度不断下降的主要原因之一。  对于机器学习模型，数据偏移是指模型输入数据的变化，这会导致模型性能下降。  监视数据偏移有助于检测这些模型性能问题。

数据偏移的原因包括：

- 上游流程更改，例如，更换了传感器，使度量单位由英寸改为厘米。 
- 数据质量问题，例如，已损坏的传感器的读数始终为 0。
- 数据的自然偏移，例如，平均温度随着季节而变化。
- 特征之间的关系变化，也称为共变偏移。 

Azure 机器学习通过计算单个指标来简化偏移检测，该指标将所比较数据集的复杂性抽象化。  这些数据集可能有数百个特征和数万个行。 一旦检测到偏移，就可以通过向下钻取来了解哪些特征导致了偏移。  然后你可以检查特征级别指标，以调试和厘清偏移的根本原因。

这种自上而下的方法可以轻松监视数据，不必使用传统的基于规则的方法。 基于规则的方法（例如允许的数据范围或允许的唯一值）可能非常耗时且容易出错。

在 Azure 机器学习中，我们使用数据集监视器进行数据偏移检测和报警。
  
### <a name="dataset-monitors"></a>数据集监视器 

数据集监视器的功能：

* 检测数据集中新数据的数据偏移并发出警报。
* 分析历史数据的偏移情况。
* 分析一段时间内的新数据。

数据偏移算法提供数据变化的整体度量，并指出需要对哪些特征做进一步的调查。 数据集监视器通过分析 `timeseries` 数据集中的新数据来生成其他许多指标。 

可以通过 [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 针对监视器生成的所有指标设置自定义警报。 数据集监视器可用于快速捕获数据问题，并通过识别可能的原因来减少调试问题所需的时间。  

从概念上讲，在 Azure 机器学习中设置数据集监视器有三种主要方案。

方案 | 描述
---|---
监视模型的服务数据与训练数据之间的偏移 | 由于服务数据与训练数据之间存在偏移时模型准确度下降，因此可以将此方案的结果解释为在代理中监视模型的准确度。
监视时序数据集与前一个时间段之间的偏移。 | 此方案较为常见，可用于监视涉及到模型生成操作的上游或下游节点的数据集。  目标数据集必须有一个时间戳列。 基线数据集可以是任意表格数据集，其中包含与目标数据集共有的特征。
对过去的数据进行分析。 | 此方案可用于了解历史数据，并在数据集监视器的设置方面做出决策。

数据集监视器依赖于以下 Azure 服务。

|Azure 服务  |描述  |
|---------|---------|
| 数据集 | 偏移使用机器学习数据集检索训练数据，并比较用于模型训练的数据。  生成数据概要文件是为了生成一些报告指标，例如最小值、最大值、非重复值、非重复值计数。 |
| Azureml 管道和计算 | 偏移计算作业托管在 azureml 管道中。  该作业按需或按计划触发，可以针对在创建偏移监视器时配置的计算运行。
| *Application insights*| 偏移会向属于机器学习工作区的 Application Insights 发出指标。
| *Azure blob 存储*| 偏移会向 Azure Blob 存储发出 JSON 格式的指标。

## <a name="how-dataset-monitors-data"></a>数据集如何监视数据

可以使用机器学习数据集监视数据偏移。 请指定一个基线数据集（通常为模型的训练数据集）。 将一段时间内的目标数据集（通常是模型输入数据）与基线数据集进行比较。 这种比较意味着必须为目标数据集指定一个时间戳列。

## <a name="create-target-dataset"></a>创建目标数据集

需要通过数据中的某个列或者派生自文件路径模式的某个虚拟列指定一个时间戳列，为目标数据集设置 `timeseries` 特征。 可通过 [Python SDK](#sdk-dataset) 或 [Azure 机器学习工作室](#studio-dataset)创建带时间戳的数据集。 必须指定表示“时间戳”的列，才能向数据集添加 `timeseries` 特征。 如果数据已分区成包含时间信息的文件夹结构（例如“{yyyy/MM/dd}”），请通过路径模式设置来创建虚拟列，并将其设置为“分区时间戳”，以提高时序功能的重要性。

### <a name="python-sdk"></a><a name="sdk-dataset"></a>Python SDK

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) 类的 [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) 方法定义数据集的时间戳列。

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

有关使用数据集的 `timeseries` 特征的完整示例，请参阅[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)或[数据集 SDK 文档](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true#&preserve-view=truewith-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)。

### <a name="azure-machine-learning-studio"></a><a name="studio-dataset"></a>Azure 机器学习工作室

如果使用 Azure 机器学习工作室创建数据集，请确保数据的路径包含时间戳信息（其中包括包含数据的所有子文件夹），并设置分区格式。

在以下示例中，采用了子文件夹 *NoaaIsdFlorida/2019* 下的所有数据，分区格式指定了时间戳的年、月和日。

[![分区格式](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

在“架构”设置中，通过指定的数据集中的虚拟列或实际列指定时间戳列：

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="设置时间戳":::

如果按日期对数据分区（此处的示例就是如此），还可指定 partition_timestamp。  这样可以更高效地处理日期。

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="分区时间戳":::


## <a name="create-dataset-monitors"></a>创建数据集监视器

创建数据集监视器，以检测新数据集中的数据偏移并发出警报。  使用 [Python SDK](#sdk-monitor) 或 [Azure 机器学习工作室](#studio-monitor)。

### <a name="python-sdk"></a><a name="sdk-monitor"></a>Python SDK

有关完整详细信息，请参阅[有关数据偏移的 Python SDK 参考文档](/python/api/azureml-datadrift/azureml.datadrift)。 

以下示例演示如何使用 Python SDK 创建数据集监视器

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

有关设置 `timeseries` 数据集和数据偏移检测器的完整示例，请参阅我们的[示例笔记本](https://aka.ms/datadrift-notebook)。

### <a name="azure-machine-learning-studio"></a><a name="studio-monitor"></a> Azure 机器学习工作室

1. 导航到 [工作室的主页](https://ml.azure.com)。
1. 选择左侧的 " **数据集** " 选项卡。 
1. 选择“数据集监视器”。
   ![监视器列表](./media/how-to-monitor-datasets/monitor-list.png)

1. 单击“+创建监视器”按钮，然后单击“下一步”继续完成向导。   

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="创建监视器向导":::

* **选择目标数据集**。  目标数据集是一个表格数据集，在其中指定的时间戳列将用于分析数据偏移。 目标数据集必须包含与基线数据集共有的特征，并且应该是要将新数据追加到的 `timeseries` 数据集。 可以分析目标数据集中的历史数据，也可以监视新数据。

* **选择基线数据集。**  选择在比较一段时间内的目标数据集时用作基线的表格数据集。  基线数据集必须包含与目标数据集共有的特征。  选择一个使用目标数据集切片的时间范围，或指定一个可用作基线的单独数据集。

* **监视器设置**。  这些设置适用于要创建的计划数据集监视管道。 

    | 设置 | 说明 | 提示 | 可变 | 
    | ------- | ----------- | ---- | ------- |
    | 名称 | 数据集监视器的名称。 | | 否 |
    | 功能 | 要在其中分析一段时间内的数据偏移的特征列表。 | 设置为模型的输出特征，以度量概念偏移。 不要包含一段时间内会自然偏移的特征（月、年、索引等）。 调整特征列表后，可以回填现有的数据偏移监视器。 | 是 | 
    | 计算目标 | 用于运行数据集监视作业的 Azure 机器学习计算目标。 | | 是 | 
    | 启用 | 在数据集监视管道中启用或禁用计划 | 禁用计划以使用回填设置分析历史数据。 可以在创建数据集监视器后启用此设置。 | 是 | 
    | 频率 | 用于计划管道作业以及分析历史数据（如果运行回填）的频率。 选项包括每日、每周或每月。 | 每次运行都会根据频率比较目标数据集中的数据： <li>每日：将目标数据集中最新的完整日与基线进行比较 <li>每周：将目标数据集中最新的完整周（星期一 - 星期日）与基线进行比较 <li>每月：将目标数据集中最新的完整月与基线进行比较 | 否 | 
    | 延迟 | 数据进入数据集所需的时间（以小时为单位）。 例如，如果数据花费 3 天时间进入数据集封装的 SQL 数据库，则将滞后时间设置为 72。 | 创建数据集监视器后无法更改 | 否 | 
    | 电子邮件地址 | 在违反数据偏移百分比阈值时用于发出警报的电子邮件地址。 | 电子邮件将通过 Azure Monitor 发送。 | 是 | 
    | 阈值 | 发出电子邮件警报之前所要达到的数据偏移百分比阈值。 | 可以在工作区关联的 Application Insights 资源中针对其他许多指标设置警报和事件。 | 是 |

完成向导后，生成的数据集监视器会显示在列表中。 选择该监视器转到其详细信息页。

## <a name="understand-data-drift-results"></a>了解数据偏移结果

本部分说明了数据集监视结果，这些结果可在 Azure 工作室中的“数据集 / 数据集监视器”页中找到。   你可以在此页上更新设置以及分析特定时间段内的现有数据。  

首先大致了解数据偏移幅度，并突出显示要进一步调查的特征。

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="偏移概述":::


| 指标 | 描述 | 
| ------ | ----------- | 
| 数据偏移幅度 | 一段时间内基线与目标数据集之间的偏移百分比。 范围为 0 到 100，0 表示数据集相同，100 表示 Azure 机器学习数据偏移模型可以完全区分两个数据集。 由于这种幅度是使用机器学习技术生成的，预期度量的精确百分比中存在干扰。 | 
| 常见偏移特征 | 显示数据集中因偏移最大而对“偏移幅度”指标造成最大影响的特征。 由于共变偏移，特征的基础分布不一定需要改变即可获得相对较高的特征重要性。 |
| 阈值 | 数据偏移幅度超出设定阈值就会触发警报。 可在监视器设置中对其进行配置。 | 

### <a name="drift-magnitude-trend"></a>偏移幅度趋势

查看数据集与目标数据集在指定时段内的差异。  越接近 100%，两个数据集的差异越大。

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="偏移幅度趋势":::

### <a name="drift-magnitude-by-features"></a>偏移幅度（按特征）

此部分包含对所选特征的分布变化的特征级见解，以及一段时间内的其他统计信息。

此外，将分析一段时间内的目标数据集。 将把一段时间内每个特征的基线分布之间的统计距离与目标数据集的相应距离进行比较。  这在概念上类似于数据偏移幅度。  但是，此统计距离适用于单个特征而非所有特征。 还可以使用最小值、最大值和平均值。

在 Azure 机器学习工作室中，单击图中的某个条形可查看该日期的特征级详细信息。 默认情况下，可以看到基线数据集的分布，以及同一特征的最近运行的分布。

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="偏移幅度（按特征）":::

也可以在 Python SDK 中通过对 `DataDriftDetector` 对象运行 `get_metrics()` 方法检索这些指标。

### <a name="feature-details"></a>特征详细信息

最后，可通过向下滚动来查看每个单独特征的详细信息。  可使用图表上方的下拉列表选择特征，并另外选择要查看的指标。

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="数值特征图和比较":::

图表中的指标取决于特征的类型。

* 数字特征

    | 指标 | 说明 |  
    | ------ | ----------- |  
    | Wasserstein 距离 | 将基线分布转换为目标分布的最小工作量。 |
    | 平均值 | 特征的平均值。 |
    | 最小值 | 特征的最小值。 |
    | 最大值 | 特征的最大值。 |

* 分类特征
    
    | 指标 | 说明 |  
    | ------ | ----------- |  
    | Euclidian 距离     |  为分类列计算。欧氏距离计算在两个向量上，它们是从两个数据集中相同分类列的实践分布生成的。0指示不会有任何变化。与0相比，此列中的偏移越多。可从该指标的时序绘图中观察趋势，并有助于发现偏移的功能。  |
    | 唯一值 | 特征的唯一值（基数）数目。 |

在此图表中，可以选择单个日期来比较目标与所显示特征的此日期之间的特征分布。 对于数值特征，这会显示两个概率分布。  如果特征为数值，则显示条形图。

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="选择一个与目标比较的日期":::

## <a name="metrics-alerts-and-events"></a>指标、警报和事件

可以在与机器学习工作区关联的 [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 资源中查询指标。 你有权访问 Application Insights 的所有功能，包括为自定义警报规则和操作组设置以触发操作，如电子邮件/短信/推送/语音或 Azure 功能。 有关详细信息，请参阅完整的 Application Insights 文档。 

若要开始，请导航到 [Azure 门户](https://portal.azure.com)并选择工作区的“概览”页。  关联的 Application Insights 资源位于最右侧：

[![Azure 门户概述](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

在左侧窗格中选择“监视”下的“日志(分析)”：

![Application Insights 概述](./media/how-to-monitor-datasets/ai-overview.png)

数据集监视器指标存储为 `customMetrics`。 可以在设置数据集监视器之后编写和运行查询来查看指标：

[![Log Analytics 查询](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

识别要对其设置警报规则的指标后，创建新的警报规则：

![新建警报规则](./media/how-to-monitor-datasets/alert-rule.png)

可以使用现有操作组或创建一个新操作组来定义满足设置的条件时要执行的操作：

![新建操作组](./media/how-to-monitor-datasets/action-group.png)

## <a name="next-steps"></a>后续步骤

* 转到 [Azure 机器学习工作室](https://ml.azure.com)或 [Python 笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb)来设置数据集监视器。
* 了解如何在[部署到 Azure Kubernetes 服务的模型](how-to-monitor-data-drift.md)中设置数据偏移。
* 使用[事件网格](how-to-use-event-grid.md)设置数据集偏移监视器。 
* 如果遇到问题，请参阅这些常见的[故障排除提示](resource-known-issues.md#data-drift)。
