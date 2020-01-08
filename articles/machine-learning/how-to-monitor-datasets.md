---
title: 分析和监视数据集上的数据偏移（预览）
titleSuffix: Azure Machine Learning
description: 创建 Azure 机器学习数据集监视器（预览）、监视数据集中的数据偏移以及设置警报。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 524a831c63e2a034766b874cc45bb039e3185b50
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75536871"
---
# <a name="detect-data-drift-preview-on-datasets"></a>检测数据集上的数据偏移（预览）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何创建 Azure 机器学习数据集监视器（预览版）、监视数据偏移和数据集中的统计更改以及设置警报。

利用 Azure 机器学习数据集监视器，可以：
* **分析数据中的偏差**，以了解它在一段时间内的变化情况。
* **监视模型数据**，以了解定型数据集和服务数据集之间的差异。
* **监视新数据**，以了解任何基线和目标数据集之间的差异。
* **数据中的分析功能**，用于跟踪统计信息属性随时间变化的方式。
* 针对**数据偏移设置警报**，以便在出现潜在问题时及早发出警告。 

可以通过与 Azure 机器学习工作区关联的[Azure 应用程序 insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)资源来获取指标和见解。

> [!Important]
> 请注意，在所有版本中都提供监视与 SDK 的数据偏移，而通过 web 上的 studio 监视数据偏移仅适用于企业版。

## <a name="prerequisites"></a>必备组件

若要创建和使用数据集监视器，需要：
* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 立即试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。
* [Azure 机器学习工作区](how-to-manage-workspace.md)。
* [安装的适用于 Python 的 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)，其中包含 azureml 数据集包。
* 结构化（表格）数据，具有在数据的文件路径、文件名或列中指定的时间戳。

## <a name="what-is-data-drift"></a>什么是数据偏移？

在机器学习的上下文中，数据偏移是模型输入数据中的更改，导致模型性能下降。 这是模型准确性在一段时间内的最主要原因之一，因此监视数据偏移可帮助检测模型性能问题。

数据偏移的原因包括： 

- 上游处理更改，如替换为从英寸到厘米的度量单位的传感器。 
- 数据质量问题，如传感器损坏始终为0。
- 数据中的自然偏移，如用季节变化的平均温度。
- 在特征或共变数移位之间进行更改。 

利用 Azure 机器学习数据集监视器，你可以设置警报，以便在一段时间内帮助数据偏移检测数据。 

### <a name="dataset-monitors"></a>数据集监视器 

您可以创建数据集监视器来检测和警报数据集中的新数据的数据偏移，分析历史数据的偏差，并分析一段时间内的新数据。 数据偏移算法提供数据更改的整体度量，并指明哪些功能负责进一步调查。 数据集监视器通过分析 `timeseries` 数据集中的新数据来产生多个其他指标。 可以通过[Azure 应用程序 Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)在监视器生成的所有指标上设置自定义警报。 数据集监视器可用于快速捕获数据问题，并通过识别可能的原因来减少调试问题的时间。  

从概念上讲，在 Azure 机器学习中设置数据集监视器有三个主要方案。

方案 | Description
---|---
监视模型的服务数据是否与模型的定型数据偏差 | 此方案中的结果可以解释为监视代理的模型准确性，因为如果服务数据偏离的是定型数据，则模型准确性会降低。
监视时序数据集的时间与以前的时间段之间的偏差。 | 此方案更常见，可用于监视正在生成的上游或下游的数据集。  目标数据集必须具有时间戳列，而基线数据集可以是具有与目标数据集共有的功能的任何表格数据集。
对过去的数据执行分析。 | 此方案可用于了解数据集监视器的设置中的历史数据并通知决策。

## <a name="how-dataset-can-monitor-data"></a>数据集如何监视数据

使用 Azure 机器学习，数据偏移通过数据集进行监视。 若要监视数据偏移，请指定基线数据集，这通常是模型的定型数据集。 目标数据集（通常为模型输入数据）会随着时间的推移与基准数据集进行比较。 这种比较意味着目标数据集必须指定时间戳列。

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>设置目标数据集中的 `timeseries` 特征

目标数据集需要在其上设置 `timeseries` 特征，方法是从数据中的列或从文件的路径模式派生的虚拟列指定时间戳列。 可以通过 Python SDK 或 Azure 机器学习 studio 完成此操作。 必须指定一个表示 "精细" 时间戳的列，才能将 `timeseries` 特征添加到数据集。 如果将数据分区到带有时间信息的文件夹结构中（如 "{yyyy/MM/dd}"），则可以通过 "路径模式" 设置创建虚拟列，并将其设置为 "粗粒度" 时间戳以提高时序功能的重要性。 

#### <a name="python-sdk"></a>Python SDK

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-)类的[`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-)方法定义数据集的时间戳列。 

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

有关使用数据集的 `timeseries` 特征的完整示例，请参阅[示例笔记本](https://aka.ms/azureml-tsd-notebook)或[数据集 SDK 文档](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-)。

#### <a name="azure-machine-learning-studio"></a>Azure 机器学习工作室
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

如果使用 Azure 机器学习 studio 创建数据集，请确保数据的路径包含时间戳信息，包括包含数据的所有子文件夹，并设置分区格式。 

在下面的示例中， *NoaaIsdFlorida/2019*子文件夹下的所有数据都是采用的，分区格式指定时间戳的年、月和日。 

[![分区格式](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

在 "**架构**设置" 中，指定指定数据集中的虚拟列或实际列中的时间戳列：

![Timestamp](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>数据集监视器设置

创建具有指定时间戳设置的数据集后，便可以配置数据集监视器了。

各种数据集监视器设置分为三组：**基本信息、监视设置**和**回填设置**。

### <a name="basic-info"></a>基本信息

此表包含用于数据集监视器的基本设置。

| 设置 | Description | 提示 | 可变 | 
| ------- | ----------- | ---- | ------- | 
| 名称 | 数据集监视器的名称。 | | 否 |
| 基线数据集 | 表格数据集，将用作一段时间内的目标数据集比较基线。 | 基线数据集必须具有与目标数据集共有的功能。 通常，应将基线设置为模型的定型数据集或目标数据集的切片。 | 否 |
| 目标数据库 | 指定了时间戳列的表格数据集将分析数据偏移。 | 目标数据集必须与基线数据集具有相同的功能，并且应为将新数据追加到的 `timeseries` 数据集。 可以分析目标数据集中的历史数据，也可以监视新数据。 | 否 | 
| 频率 | 用于计划管道作业并在运行回填时分析历史数据的频率。 选项包括每日、每周或每月。 | 调整此设置以在基线中包含可比较的数据大小。 | 否 | 
| 功能 | 一段时间内将针对数据偏差分析的特征列表。 | 设置为模型的输出功能以测量概念偏移。 不要包含在一段时间内自然变化（月、年、索引等）的功能。 调整功能列表后，可以回填和现有数据偏移监视器。 | 是 | 
| 计算目标 | Azure 机器学习计算目标运行数据集监视作业。 | | 是 | 

### <a name="monitor-settings"></a>监视设置

这些设置适用于将创建的计划数据集监视管道。 

| 设置 | Description | 提示 | 可变 | 
| ------- | ----------- | ---- | ------- |
| 启用 | 启用或禁用数据集监视管道上的计划 | 禁用计划以便通过回填设置分析历史数据。 它可以在创建数据集监视器后启用。 | 是 | 
| 延迟 | 数据到达数据集所需的时间（以小时为单位）。 例如，如果数据在数据集封装到 SQL 数据库中时需要三天时间，则将滞后时间设置为72。 | 创建数据集监视器后无法更改 | 否 | 
| 电子邮件地址 | 基于违反数据偏差百分比阈值的警报的电子邮件地址。 | 电子邮件通过 Azure Monitor 发送。 | 是 | 
| 阈值 | 电子邮件警报的数据偏移百分比阈值。 | 还可以在工作区的关联 Application Insights 资源中为其他许多指标设置警报和事件。 | 是 | 

### <a name="backfill-settings"></a>回填设置

这些设置用于针对数据偏差指标针对过去的数据运行回填。

| 设置 | Description | 提示 |
| ------- | ----------- | ---- |
| 开始日期 | 回填作业的开始日期。 | | 
| 结束日期 | 回填作业的结束日期。 | 结束日期不能超过从开始日期起的频率的频率单位。 在现有数据集监视器上，可以回填指标分析历史数据或将指标替换为更新的设置。 |

## <a name="create-dataset-monitors"></a>创建数据集监视器 

创建数据集监视器，以检测 Azure 机器学习 studio 或 Python SDK 的新数据集上的数据偏移并向其发出警报。 

### <a name="azure-machine-learning-studio"></a>Azure 机器学习工作室
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

若要在数据集监视器上设置警报，包含要为其创建监视器的数据集的工作区必须具有企业版功能。 

确认工作区功能后，导航到工作室的主页，然后选择左侧的 "数据集" 选项卡。 选择数据集监视器。

![监视器列表](./media/how-to-monitor-datasets/monitor-list.png)

单击 " **+ 创建监视器**" 按钮，然后单击 "**下一步**" 继续运行向导。

![向导](./media/how-to-monitor-datasets/wizard.png)

生成的数据集监视器将显示在列表中。 选择它以切换到该监视器的详细信息页。

### <a name="from-python-sdk"></a>从 Python SDK

有关完整详细信息，请参阅[PYTHON SDK 参考文档有关数据偏移](/python/api/azureml-datadrift/azureml.datadrift)。 

下面的示例演示如何使用 Python SDK 创建数据集监视器

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

有关设置 `timeseries` 数据集和数据偏移检测程序的完整示例，请参阅[示例笔记本](https://aka.ms/datadrift-notebook)。

## <a name="understanding-data-drift-results"></a>了解数据偏移结果

数据监视器产生两组结果：偏移概述和功能详细信息。 以下动画显示基于所选功能和指标的可用偏移监视器图表。 

![演示视频](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>偏移概述

"**偏移概述**" 部分包含有关数据偏移量的顶级信息，以及应对哪些功能进行进一步调查。 

| 度量值 | Description | 提示 | 
| ------ | ----------- | ---- | 
| 数据偏移量 | 指定为一段时间内基准数据集和目标数据集之间的百分比。 范围从0到100，其中0表示相同的数据集，100表示 Azure 机器学习数据偏移功能可以完全告诉两个数据集。 | 由于用于生成这种规模的机器学习技术，预期的精确百分比的噪音。 | 
| 按功能的偏差贡献 | 目标数据集中的每个功能对度量的偏移量的贡献。 |  由于共变数移位，功能的基础分布不一定需要更改为具有相对较高的特征重要性。 | 

下图是一个示例，该示例显示在 Azure 机器学习 studio 中的**偏差概述**中，这是由[NOAA 集成 Surface 数据](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)的回填导致的。 将数据采样为 `stationName contains 'FLORIDA'`，使用2019年1月作为基准数据集，并使用所有2019数据作为目标。
 
![偏移概述](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>功能详细信息

"**功能详细信息**" 部分包含有关所选功能的分发中的更改以及其他统计信息随时间变化的功能级别见解。 

还会在一段时间内分析目标数据集。 每个功能的基线分布之间的统计距离与目标数据集的随时间进行比较，这在概念上类似于数据偏移量，但这种统计距离适用于单个功能。 最小值、最大值和平均值也可用。 

在 Azure 机器学习 studio 中，如果单击图中的数据点，则会相应地调整所显示的功能的分布。 默认情况下，它显示了基线数据集的分布以及同一功能的最近运行的分布。 

还可以通过 `DataDriftDetector` 对象上的 `get_metrics()` 方法在 Python SDK 中检索这些指标。 

#### <a name="numeric-features"></a>数值特征 

在每个数据集监视器运行中对数值特征进行分析。 Azure 机器学习 studio 中公开了以下各项。 显示分布的概率密度。

| 度量值 | Description |  
| ------ | ----------- |  
| Wasserstein 距离 | 将基线分布转换为目标分布的最小工作量。 |
| 均值值 | 功能的平均值。 |
| 最小值 | 功能的最小值。 |
| 最大值 | 功能的最大值。 |

![功能详细信息数值](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>分类特征 

在每个数据集监视器运行中对数值特征进行分析。 Azure 机器学习 studio 中公开了以下各项。 为分布显示直方图。

| 度量值 | Description |  
| ------ | ----------- |  
| Euclidian 距离 | 基线与目标分布之间的几何距离。 |
| 唯一值 | 功能的唯一值（基数）的数目。 |


![功能详细信息分类](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>指标、警报和事件

可以在与机器学习工作区关联的[Azure 应用程序 Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)资源中查询度量值。 这将授予对 Application Insights 的所有功能的访问权限，包括为自定义警报规则和操作组设置以触发操作，如电子邮件/短信/推送/语音或 Azure 功能。 有关详细信息，请参阅完整的 Application Insights 文档。 

若要开始，请导航到 Azure 门户并选择工作区的 "**概述**" 页。  关联的 Application Insights 资源位于最右侧：

[![Azure 门户概述](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

在左侧窗格中选择 "监视" 下的 "日志（分析）"：

![Application insights 概述](./media/how-to-monitor-datasets/ai-overview.png)

数据集监视器度量值存储为 `customMetrics`。 您可以在设置数据集监视器之后编写和运行查询，以查看它们：

[![Log analytics 查询](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

确定用于设置预警规则的指标后，创建新的警报规则：

![新建警报规则](./media/how-to-monitor-datasets/alert-rule.png)

您可以使用现有的操作组，或者创建一个新的操作组来定义满足设置条件时要执行的操作：

![新建操作组](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>故障排除

限制和已知问题：

* 回填作业的时间范围限制为监视器频率设置的31个间隔。 
* 200功能的限制，除非未指定功能列表（使用了所有功能）。
* 计算大小必须足够大才能处理数据。 
* 确保你的数据集在给定的监视器运行的开始和结束日期内具有数据。

数据集中的列或功能根据下表中的条件分类为分类或数值。 如果该功能不满足这些条件，则为 string 类型的列 > 100 个唯一值，则会从数据偏移算法中删除该功能，但仍会对其进行分析。 

| 功能类型 | 数据类型 | 条件 | 限制 | 
| ------------ | --------- | --------- | ----------- |
| 分类 | string、bool、int、float | 功能中的唯一值数小于100，小于行数的5%。 | Null 被视为自己的类别。 | 
| 数值 | int、float | 功能中的值为数值数据类型，不符合分类功能的条件。 | 如果 > 15% 的值为 null，则删除功能。 | 

## <a name="next-steps"></a>后续步骤

* 转到[Azure 机器学习 studio](https://ml.azure.com)或[Python 笔记本](https://aka.ms/datadrift-notebook)，设置数据集监视器。
* 请参阅如何在[部署到 Azure Kubernetes 服务的模型](how-to-monitor-data-drift.md)上设置数据偏移。
* 设置带有[事件网格](how-to-use-event-grid.md)的数据集偏移监视器。 