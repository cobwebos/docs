---
title: 计划 Azure 机器学习管道
titleSuffix: Azure Machine Learning
description: 使用用于 Python 的 Azure 机器学习 SDK 来计划 Azure 机器学习管道。 计划的管道使你能够自动执行日常的日常任务，如数据处理、定型和监视。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 10/15/2019
ms.openlocfilehash: 31c3cd944651b9ba4ca4fcaa275e5b0ccedd947c
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72559432"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>通过用于 Python 的 Azure 机器学习 SDK 来计划机器学习管道

本文介绍如何以编程方式安排要在 Azure 上运行的管道。 你可以选择基于运行时间或文件系统更改创建计划。 基于时间的计划可用于处理日常任务，如监视数据偏移。 基于更改的计划可用于对异常或不可预测的更改（例如上传的新数据或正在编辑的旧数据）做出反应。 在了解如何创建计划后，你将了解如何检索和停用它们。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请创建一个[免费帐户](https://aka.ms/AMLFree)。

* 用于安装 Python Azure 机器学习 SDK 的 Python 环境。 有关详细信息，请参阅[使用 Azure 机器学习创建和管理用于定型和部署的可重复使用的环境。](how-to-use-environments.md)

* 带有已发布管道的机器学习工作区。 可以使用 Azure 机器学习 SDK 中内置的[创建和运行机器学习管道](how-to-create-your-first-pipeline.md)。

## <a name="initialize-the-workspace--get-data"></a>& 获取数据初始化工作区

若要计划管道，需要对工作区、已发布管道的标识符以及要在其中创建计划的实验的名称进行引用。 可以通过以下代码获取这些值：

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

若要定期运行管道，你将创建一个计划。 @No__t_0 将管道、试验和触发器关联起来。 触发器可以是描述等待的时间间隔的 `ScheduleRecurrence`，或用于指定监视更改的目录的数据存储路径。 在任一情况下，都需要管道标识符和要在其中创建计划的实验的名称。

### <a name="create-a-time-based-schedule"></a>创建基于时间的计划

@No__t_0 构造函数具有必需的 `frequency` 参数，该参数必须为以下字符串之一： "Minute"、"Hour"、"Day"、"Week" 或 "Month"。 它还需要一个整数 `interval` 参数，该参数指定在计划开始之前应经过的 `frequency` 单位数。 可选参数使你可以更具体地了解开始时间，如[按 SDK 文档](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py)中所述。

创建一个每15分钟开始一次运行的 `Schedule`：

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>创建基于更改的计划

文件更改触发的管道可能比基于时间的计划更有效。 例如，你可能想要在文件更改时或者在将新文件添加到数据目录时执行预处理步骤。 可以监视数据存储的任何更改，或监视数据存储中特定目录中的更改。 如果监视特定目录，则该目录的子目录中的更改_不_会触发运行。

若要创建文件反应性的 `Schedule`，必须在 "[计划](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-)" 的调用中设置 `datastore` 参数。 若要监视文件夹，请设置 `path_on_datastore` 参数。

@No__t_0 参数允许你指定检查数据存储更改的频率（以分钟为单位）。

如果管道是使用[数据路径](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)构造的，则可以通过设置 `data_path_parameter_name` 参数将该变量设置为更改后的文件的名称。

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on time",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>创建计划时的可选参数

除了前面所述的参数，您还可以将 `status` 参数设置为 `"Disabled"` 以创建非活动计划。 最后，`continue_on_step_failure` 允许传递一个布尔值，该布尔值将重写管道的默认失败行为。

## <a name="view-your-scheduled-pipelines"></a>查看预定管道

在 Web 浏览器中，导航到机器学习服务工作区。 从导航面板的 "**资产**" 部分，选择 "**管道**"。 此链接会将你带到工作区中发布的管道的列表。

![工作区的管道页](media/how-to-schedule-a-pipeline/pipelines-list.png)

在此页中，可以查看工作区中所有管道的摘要信息：名称、说明、状态等。 单击管道中的 "钻取"。 在生成的页面上，提供有关管道的更多详细信息，并且可以向下钻取到各个运行中。

## <a name="deactivate-the-pipeline"></a>停用管道

如果已发布但未计划 `Pipeline`，可以使用以下方法禁用它：

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

如果安排了管道，则必须先取消计划。 从门户或通过运行以下方法检索计划的标识符：

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

获得要禁用的 `schedule_id` 后，请运行：

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule(ws, schedule_id)
```

如果随后再次运行 `Schedule.list(ws)`，则应该会获得一个空列表。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用适用于 Python 的 Azure 机器学习 SDK 以两种不同的方式安排管道。 基于已用的时钟时间重复一个计划。 如果在指定的 `Datastore` 上或在该存储区的某个目录内修改了某个文件，则其他计划将运行。 你了解了如何使用门户来检查管道和单个运行。 最后，您学习了如何禁用计划以便管道停止运行。

有关详细信息，请参阅：

> [!div class="nextstepaction"]
> [使用 Azure 机器学习管道进行批处理评分](tutorial-pipeline-batch-scoring-classification.md)

* 了解有关[管道](concept-ml-pipelines.md)的详细信息
* 了解有关[通过 Jupyter 浏览 Azure 机器学习的](samples-notebooks.md)详细信息
