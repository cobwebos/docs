---
title: 计划 Azure 机器学习管道
titleSuffix: Azure Machine Learning
description: 通过适用于 Python 的 Azure 机器学习 SDK 来计划 Azure 机器学习管道。 通过计划的管道，可以自动执行耗时的例行任务，例如数据处理、训练和监视。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: df9447160fe6a0aa2a3ae001ad8a337c3ff488f2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91275937"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>通过适用于 Python 的 Azure 机器学习 SDK 来计划机器学习管道

本文介绍如何以编程方式计划管道，使其在 Azure 上运行。 可以选择创建基于运行时间或文件系统更改的计划。 基于时间的计划可用于处理例行任务，如监视数据偏移。 基于更改的计划可用于响应非常规或无法预测的更改，例如上传新数据或编辑旧数据。 了解如何创建计划后，你将了解如何检索和停用它们。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请创建一个 [免费帐户](https://aka.ms/AMLFree)。

* 安装了适用于 Python 的 Azure 机器学习 SDK 的 Python 环境。 有关详细信息，请参阅[使用 Azure 机器学习创建和管理用于训练和部署的可重用环境。](how-to-use-environments.md)

* 包含已发布管道的机器学习工作区。 可以使用[使用 Azure 机器学习 SDK 创建和运行机器学习管道](how-to-create-your-first-pipeline.md)中提供的工作区。

## <a name="initialize-the-workspace--get-data"></a>初始化工作区并获取数据

若要计划管道，需要引用工作区、已发布管道的标识符以及要在其中创建计划的试验的名称。 可通过以下代码获取这些值：

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>创建计划

若要重复运行管道，需创建计划。 `Schedule` 与管道、试验和触发器关联。 触发器可以是 `ScheduleRecurrence`（用于描述两次运行之间的等待时间），也可以是数据存储路径（用于指定要监视其中更改的目录）。 在任一情况下，都需要管道标识符，以及要在其中创建计划的试验名称。

在 python 文件的顶部，导入 `Schedule` 和 `ScheduleRecurrence` 类：

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>创建基于时间的计划

`ScheduleRecurrence` 构造函数具有一个必需的 `frequency` 参数，它必须包含以下字符串之一：“Minute”、“Hour”、“Day”、“Week”或“Month”。 它还需要一个整型 `interval` 参数，用于指定每次启动计划前应经过的 `frequency` 单位数。 可以使用可选参数更具体地规定开始时间，如 [ScheduleRecurrence SDK 文档](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py&preserve-view=true)中所述。

创建每 15 分钟运行一次的 `Schedule`：

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>创建基于更改的计划

由文件更改触发的管道可能比基于时间的计划更有效。 例如，你可能需要在文件更改时或者在将新文件添加到数据目录时执行预处理步骤。 可以监视对数据存储的任何更改，或监视数据存储中特定目录中的更改。 如果监视特定目录，该目录的子目录中的更改将不会触发运行。

若要创建响应文件的 `Schedule`，必须在对 [Schedule.create](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py&preserve-view=true#&preserve-view=truecreate-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-) 的调用中设置 `datastore` 参数。 若要监视文件夹，请设置 `path_on_datastore` 参数。

`polling_interval` 参数可用于指定检查数据存储更改的频率（分钟）。

如果管道中构造了 [DataPath](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py&preserve-view=true) [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py&preserve-view=true)，则可以通过设置 `data_path_parameter_name` 参数将该变量设置为修改后的文件的名称。

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>创建计划时的可选参数

除了前面所述的参数，还可以将 `status` 参数设置为 `"Disabled"` 以创建非活动计划。 最后，可通过 `continue_on_step_failure` 传递布尔值，重写管道的默认失败行为。

### <a name="use-azure-logic-apps-for-more-complex-workflows"></a>将 Azure 逻辑应用用于更复杂的工作流

Azure 逻辑应用支持更复杂的工作流，其集成的广泛程度远远高于 Azure 机器学习管道。 有关详细信息，请参阅[从逻辑应用触发机器学习管道的运行](how-to-trigger-published-pipeline.md)。

## <a name="view-your-scheduled-pipelines"></a>查看计划的管道

在 Web 浏览器中，导航到 Azure 机器学习。 在导航面板的“终结点”部分中，选择“管道终结点” 。 将转到已在工作区中发布的管道的列表。

![AML 的“管道”页](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

在此页中，可以看到工作区中所有的管道摘要信息，包括名称、说明、状态等。 单击管道可深入了解其信息。 在由此打开的页面上，显示了有关该管道的更多详细信息，可以向下钻取到单次运行。

## <a name="deactivate-the-pipeline"></a>停用管道

如果拥有已发布但未计划的 `Pipeline`，可以使用以下方法禁用它：

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

如果已计划管道，必须先取消计划。 从门户或通过运行以下方法检索计划的标识符：

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

获得要禁用计划的的 `schedule_id` 后，请运行：

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

如果此后再次运行 `Schedule.list(ws)`，应会得到一个空列表。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用适用于 Python 的 Azure 机器学习 SDK 以两种不同的方式计划管道。 其中一个计划按运行时间重复。 另一个计划在指定的 `Datastore` 或该存储的目录中有文件更改时运行。 你了解了如何使用门户来检查管道和单次运行。 最后，你了解了如何禁用计划以使管道停止运行。

有关详细信息，请参阅：

> [!div class="nextstepaction"]
> [使用 Azure 机器学习管道进行批量评分](tutorial-pipeline-batch-scoring-classification.md)

* 详细了解[管道](concept-ml-pipelines.md)
* 详细了解如何[通过 Jupyter 探索 Azure 机器学习](samples-notebooks.md)

