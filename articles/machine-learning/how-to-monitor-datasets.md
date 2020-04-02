---
title: 分析和监视数据集中的数据偏移（预览版）
titleSuffix: Azure Machine Learning
description: 创建 Azure 机器学习数据集监视器（预览版）、监视数据集中的数据偏移并设置警报。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 0a7a89b4ff1f6deb94c545e64b4584d7959d573a
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546379"
---
# <a name="detect-data-drift-preview-on-datasets"></a>检测数据集中的数据偏移（预览版）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何创建 Azure 机器学习数据集监视器（预览版）、监视数据集中的数据偏移和统计更改，以及设置警报。

使用 Azure 机器学习数据集监视器可以：
* **分析数据的偏移**，以了解数据在一段时间内的变化。
* **监视模型数据**，以了解训练数据集与服务数据集之间的差异。
* **监视新数据**，以了解任何基线与目标数据集之间的差异。
* **分析数据中的特征**，以跟踪统计属性在一段时间内的变化。
* **针对数据偏移设置警报**，以便针对潜在问题提前发出警告。 

可以通过与 Azure 机器学习工作区关联的 [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 资源获取指标和见解。

> [!Important]
> 请注意，可以在所有版本中使用 SDK 监视数据偏移，但只能在企业版中通过 Web 上的工作室监视数据偏移。

## <a name="prerequisites"></a>先决条件

若要创建和使用数据集监视器，需要：
* Azure 订阅。 如果没有 Azure 订阅，请在开始操作前先创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。
* [Azure 机器学习工作区](how-to-manage-workspace.md)。
* [已安装适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包含 azureml-datasets 包。
* 在数据中的文件路径、文件名或列中指定了带时间戳的结构化（表格）数据。

## <a name="what-is-data-drift"></a>什么是数据偏移？

在机器学习的上下文中，数据偏移是指模型输入数据中导致模型性能下降的变化。 这是模型准确度在一段时间后下降的最主要原因之一，因此，监视数据偏移有助于检测模型性能问题。

数据偏移的原因包括： 

- 上游流程更改，例如，更换了传感器，使度量单位由英寸改为厘米。 
- 数据质量问题，例如，已损坏的传感器的读数始终为 0。
- 数据的自然偏移，例如，平均温度随着季节而变化。
- 特征之间的关系变化，也称为共变偏移。 

使用 Azure 机器学习数据集监视器可以设置警报，以帮助检测数据集在一段时间内的数据偏移。 

### <a name="dataset-monitors"></a>数据集监视器 

可以创建数据集监视器来检测数据集中新数据的数据偏移并发出警报，分析历史数据的偏移，以及分析一段时间内的新数据。 数据偏移算法提供数据变化的整体度量，并指出需要对哪些特征做进一步的调查。 数据集监视器通过分析 `timeseries` 数据集中的新数据来生成其他许多指标。 可以通过 [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 针对监视器生成的所有指标设置自定义警报。 数据集监视器可用于快速捕获数据问题，并通过识别可能的原因来减少调试问题所需的时间。  

从概念上讲，在 Azure 机器学习中设置数据集监视器有三种主要方案。

方案 | 说明
---|---
监视模型服务数据与模型训练数据之间的偏移 | 如果服务数据与训练数据之间存在偏移时模型的准确度下降，则此方案的结果可以解释为在代理中监视模型的准确度。
监视时序数据集与前一个时间段之间的偏移。 | 此方案较为常见，可用于监视涉及到模型生成操作的上游或下游节点的数据集。  目标数据集必须包含一个时间戳列，而基线数据集可以是任意表格数据集，其中包含与目标数据集共有的特征。
针对过去的数据执行分析。 | 此方案可用于了解历史数据，并在数据集监视器的设置方面做出决策。

## <a name="how-dataset-can-monitor-data"></a>数据集如何监视数据

使用 Azure 机器学习通过数据集监视数据偏移。 若要监视数据偏移，需要指定一个基线数据集 - 通常是模型的训练数据集。 将一段时间内的目标数据集（通常是模型输入数据）与基线数据集进行比较。 这种比较意味着必须为目标数据集指定一个时间戳列。

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>在目标数据集中设置 `timeseries` 特征

需要通过数据中的某个列或者派生自文件路径模式的某个虚拟列指定一个时间戳列，为目标数据集设置 `timeseries` 特征。 可以通过 Python SDK 或 Azure 机器学习工作室完成此操作。 必须指定表示“精细粒度”时间戳的列，才能向数据集添加 `timeseries` 特征。 如果数据已分区成包含时间信息的文件夹结构（例如“{yyyy/MM/dd}”），则可以通过路径模式设置来创建虚拟列，并将其设置为“粗糙粒度”时间戳，以提高时序功能的重要性。 

#### <a name="python-sdk"></a>Python SDK

类[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)的方法[`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)定义数据集的时间戳列。 

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

有关使用数据集的 `timeseries` 特征的完整示例，请参阅[示例笔记本](https://aka.ms/azureml-tsd-notebook)或[数据集 SDK 文档](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)。

#### <a name="azure-machine-learning-studio"></a>Azure 机器学习工作室
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

如果使用 Azure 机器学习工作室创建数据集，请确保数据的路径包含时间戳信息（其中包括包含数据的所有子文件夹），并设置分区格式。 

在以下示例中，采用了子文件夹 *NoaaIsdFlorida/2019* 下的所有数据，分区格式指定了时间戳的年、月和日。 

[![分区格式](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

在“架构”设置中，通过指定的数据集中的虚拟列或实际列指定时间戳列：****

![时间戳](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>数据集监视器设置

创建使用指定时间戳设置的数据集后，便可以配置数据集监视器。

各种数据集监视器设置分为三组：**基本信息、监视器设置**和**回填设置**。

### <a name="basic-info"></a>基本信息

下表包含用于数据集监视器的基本设置。

| 设置 | 说明 | 提示 | 可变 | 
| ------- | ----------- | ---- | ------- | 
| 名称 | 数据集监视器的名称。 | | 否 |
| 基线数据集 | 在比较一段时间内的目标数据集时用作基线的表格数据集。 | 基线数据集必须包含与目标数据集共有的特征。 通常，应将基线设置为模型的训练数据集或目标数据集的切片。 | 否 |
| 目标数据集 | 包含要在其中分析数据偏移的指定时间戳列的表格数据集。 | 目标数据集必须包含与基线数据集共有的特征，并且应该是要将新数据追加到的 `timeseries` 数据集。 可以分析目标数据集中的历史数据，也可以监视新数据。 | 否 | 
| 频率 | 用于计划管道作业以及分析历史数据（如果运行回填）的频率。 选项包括每日、每周或每月。 | 调整此设置可将数据的可比较大小包含到基线中。 | 否 | 
| 功能 | 要在其中分析一段时间内的数据偏移的特征列表。 | 设置为模型的输出特征，以度量概念偏移。 不要包含一段时间内会自然偏移的特征（月、年、索引等）。 调整特征列表后，可以回填现有的数据偏移监视器。 | 是 | 
| 计算目标 | 用于运行数据集监视作业的 Azure 机器学习计算目标。 | | 是 | 

### <a name="monitor-settings"></a>监视器设置

这些设置适用于要创建的计划数据集监视管道。 

| 设置 | 说明 | 提示 | 可变 | 
| ------- | ----------- | ---- | ------- |
| 启用 | 在数据集监视管道中启用或禁用计划 | 禁用计划以使用回填设置分析历史数据。 可以在创建数据集监视器后启用此设置。 | 是 | 
| 延迟 | 数据进入数据集所需的时间（以小时为单位）。 例如，如果数据花费 3 天时间进入数据集封装的 SQL 数据库，则将滞后时间设置为 72。 | 创建数据集监视器后无法更改 | 否 | 
| 电子邮件地址 | 在违反数据偏移百分比阈值时用于发出警报的电子邮件地址。 | 电子邮件将通过 Azure Monitor 发送。 | 是 | 
| 阈值 | 发出电子邮件警报之前所要达到的数据偏移百分比阈值。 | 可以在工作区关联的 Application Insights 资源中针对其他许多指标设置警报和事件。 | 是 | 

### <a name="backfill-settings"></a>回填设置

这些设置用于针对数据偏移指标的以往数据运行回填。

| 设置 | 说明 | 提示 |
| ------- | ----------- | ---- |
| 开始日期 | 回填作业的开始日期。 | | 
| 结束日期 | 回填作业的结束日期。 | 结束日期不能超过 31 * 从开始日期算起的频率时间单位。 在现有的数据集监视器中，可以回填指标以分析历史数据，或者将指标替换为更新的设置。 |

## <a name="create-dataset-monitors"></a>创建数据集监视器 

创建数据集监视器，以使用 Azure 机器学习工作室或 Python SDK 检测新数据集中的数据偏移并发出警报。 

### <a name="azure-machine-learning-studio"></a>Azure 机器学习工作室
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

若要在数据集监视器中设置警报，要为其创建监视器的数据集所在的工作区必须具有企业版功能。 

确认工作区功能后，导航到工作室的主页，然后选择左侧的“数据集”选项卡。 选择“数据集监视器”。

![监视器列表](./media/how-to-monitor-datasets/monitor-list.png)

单击“+创建监视器”按钮，然后单击“下一步”继续完成向导。********

![向导](./media/how-to-monitor-datasets/wizard.png)

生成的数据集监视器将显示在列表中。 选择该监视器转到其详细信息页。

### <a name="from-python-sdk"></a>通过 Python SDK

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

## <a name="understanding-data-drift-results"></a>了解数据偏移结果

数据监视器生成两组结果：漂移概览和功能详细信息。 以下动画基于所选特征和指标演示了可用的偏移监视器图表。 

![演示视频](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>偏移概述

“偏差概述”部分包含数据偏移幅度的顶级见解，并指出应该对哪些特征做进一步的调查。**** 

| 指标 | 说明 | 提示 | 
| ------ | ----------- | ---- | 
| 数据偏移幅度 | 以介于基线与一段时间内的目标数据集之间的百分比表示。 范围为 0 到 100，其中 0 表示数据集相同，100 表示 Azure 机器学习数据偏移功能可以完全区分两个数据集。 | 由于这种幅度是使用机器学习技术生成的，预期度量的精确百分比中存在干扰。 | 
| 按特征显示的偏移贡献 | 目标数据集中每个特征对度量的偏移幅度的贡献。 |  由于共变偏移，特征的基础分布不一定需要改变即可获得相对较高的特征重要性。 | 

下图是 Azure 机器学习工作室的“偏移概述”中显示的图表示例，它是回填 [NOAA 集成式图面数据](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)后生成的。**** 数据已按 `stationName contains 'FLORIDA'` 采样，其中“2019 年 1 月”用作基线数据集，2019 年的所有数据用作目标。
 
![偏移概述](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>特征详细信息

“特征详细信息”部分包含所选特征的分布变化的特征级见解，以及一段时间内的其他统计信息。**** 

此外，将分析一段时间内的目标数据集。 每个特征的基线分布之间的统计距离将与一段时间内的目标数据集进行比较，这在概念上类似于数据偏移幅度，只是统计距离适用于单个特征。 还可以使用最小值、最大值和平均值。 

在 Azure 机器学习工作室中，如果单击图中的数据点，显示的特征分布会相应地调整。 默认情况下，它会显示基线数据集的分布，以及同一特征的最近运行分布。 

也可以在 Python SDK 中通过对 `DataDriftDetector` 对象运行 `get_metrics()` 方法检索这些指标。 

#### <a name="numeric-features"></a>数字特征 

将在每个数据集监视器运行中分析数字特征。 Azure 机器学习工作室中显示以下内容。 显示分布的概率密度。

| 指标 | 说明 |  
| ------ | ----------- |  
| Wasserstein 距离 | 将基线分布转换为目标分布的最小工作量。 |
| 平均值 | 特征的平均值。 |
| 最小值 | 特征的最小值。 |
| 最大值 | 特征的最大值。 |

![数字特征详细信息](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>分类特征 

将在每个数据集监视器运行中分析数字特征。 Azure 机器学习工作室中显示以下内容。 显示分布直方图。

| 指标 | 说明 |  
| ------ | ----------- |  
| Euclidian 距离 | 基线与目标分布之间的几何距离。 |
| 唯一值 | 特征的唯一值（基数）数目。 |


![分类特征详细信息](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>指标、警报和事件

可以在与机器学习工作区关联的 [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 资源中查询指标。 可以访问 Application Insights 的所有功能，包括设置自定义警报规则和操作组，以触发电子邮件/短信/推送/语音或 Azure 函数等操作。 有关详细信息，请参阅完整的 Application Insights 文档。 

若要开始，请导航到 Azure 门户并选择工作区的“概述”页。****  关联的 Application Insights 资源位于最右侧：

[![Azure 门户概述](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

在左侧窗格中选择“监视”下的“日志(分析)”：

![Application Insights 概述](./media/how-to-monitor-datasets/ai-overview.png)

数据集监视器指标存储为 `customMetrics`。 可以在设置数据集监视器之后编写和运行查询来查看指标：

[![日志分析查询](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

识别要对其设置警报规则的指标后，创建新的警报规则：

![新建警报规则](./media/how-to-monitor-datasets/alert-rule.png)

可以使用现有操作组或创建一个新操作组来定义满足设置的条件时要执行的操作：

![新建操作组](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>疑难解答

限制和已知问题：

* 回填作业的时间范围限制为监视器频率设置的 31 个间隔。 
* 除非未指定特征列表（使用所有特征），否则特征限制为 200 个。
* 计算大小必须足够大才能处理数据。 
* 确保数据集包含处于给定监视器运行的开始和结束日期范围内的数据。
* 数据集监视器将仅适用于包含 50 行或更多行的数据集。 

数据集中的列或特征根据下表中的条件划分为分类值或数字值。 如果特征不满足这些条件 - 例如，某个字符串类型的列包含 100 个以上的唯一值 - 则会从数据偏移算法中删除该特征，但仍会对其进行分析。 

| 特征类型 | 数据类型 | 条件 | 限制 | 
| ------------ | --------- | --------- | ----------- |
| 分类 | string、bool、int、float | 特征中的唯一值数小于 100，并小于行数的 5%。 | Null 被视为其自身的类别。 | 
| 数值 | int、float | 特征中的值为数字数据类型，且不符合分类特征的条件。 | 如果 15% 以上的值为 null，则会删除特征。 | 

## <a name="next-steps"></a>后续步骤

* 转到 [Azure 机器学习工作室](https://ml.azure.com)或 [Python 笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb)来设置数据集监视器。
* 了解如何在[部署到 Azure Kubernetes 服务的模型](how-to-monitor-data-drift.md)中设置数据偏移。
* 使用[事件网格](how-to-use-event-grid.md)设置数据集偏移监视器。 
