---
title: 机器学习管道 YAML
titleSuffix: Azure Machine Learning
description: 了解如何使用 YAML 文件定义机器学习管道。 YAML 管道定义用于 Azure CLI 的机器学习扩展。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.openlocfilehash: c4fe7a696461b617307c2cd87276a91d6a1edaeb
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75541460"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>在 YAML 中定义机器学习管道

了解如何在[YAML](https://yaml.org/)中定义机器学习管道。 使用机器学习扩展进行 Azure CLI 时，许多与管道相关的命令都需要 YAML 文件来定义管道。

下表列出了在 YAML 中定义管道时当前不支持的内容：

| 步骤类型 | 是否支持？ |
| ----- | :-----: |
| PythonScriptStep | 是 |
| AdlaStep | 是 |
| AzureBatchStep | 是 |
| DatabricksStep | 是 |
| DataTransferStep | 是 |
| AutoMLStep | 否 |
| HyperDriveStep | 否 |
| ModuleStep | 否 |
| MPIStep | 否 |
| EstimatorStep | 否 |

## <a name="pipeline-definition"></a>管道定义

管道定义使用以下键，它们对应于[管道](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py)类：

| YAML 键 | Description |
| ----- | ----- |
| `name` | 管道的说明。 |
| `parameters` | 管道的参数。 |
| `data_reference` | 定义应在运行中提供数据的方式和位置。 |
| `default_compute` | 管道中的所有步骤运行时的默认计算目标。 |
| `steps` | 管道中使用的步骤。 |

## <a name="parameters"></a>参数

`parameters` 部分使用以下键，它们对应于[PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)类：

| YAML 键 | Description |
| ---- | ---- |
| `type` | 参数的值类型。 有效的类型为 `string`、`int`、`float`、`bool`或 `datapath`。 |
| `default` | 默认值。 |

每个参数都命名为。 例如，以下 YAML 代码片段定义了三个名为 `NumIterationsParameter`、`DataPathParameter`和 `NodeCountParameter`的参数：

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>数据引用

`data_references` 部分使用与[DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)相对应的以下项：

| YAML 键 | Description |
| ----- | ----- |
| `datastore` | 要引用的数据存储区。 |
| `path_on_datastore` | 数据引用的备份存储中的相对路径。 |

每个数据引用都包含在一个项中。 例如，以下 YAML 代码片段定义了存储在名为 `employee_data`的项中的数据引用：

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>步骤

步骤定义计算环境，以及要在环境中运行的文件。 若要定义步骤的类型，请使用 `type` 项：

| 步骤类型 | Description |
| ----- | ----- |
| `AdlaStep` | 使用 Azure Data Lake Analytics 运行 U SQL 脚本。 对应于[AdlaStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py)类。 |
| `AzureBatchStep` | 使用 Azure Batch 运行作业。 对应于[AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py)类。 |
| `DatabricsStep` | 添加 Databricks 笔记本、Python 脚本或 JAR。 对应于[DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py)类。 |
| `DataTransferStep` | 在存储选项之间传输数据。 对应于[DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)类。 |
| `PythonScriptStep` | 运行 Python 脚本。 对应于[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)类。 |

### <a name="adla-step"></a>ADLA 步骤

| YAML 键 | Description |
| ----- | ----- |
| `script_name` | 与 `source_directory`相对应的 U SQL 脚本的名称。 |
| `compute_target` | 要用于此步骤的 Azure Data Lake 计算目标。 |
| `parameters` | 管道的[参数](#parameters)。 |
| `inputs` | 输入可以是[InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、 [DataReference](#data-reference)、 [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、 [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 输出可以是[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)或[OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `source_directory` | 包含脚本、程序集等的目录。 |
| `priority` | 要用于当前作业的优先级值。 |
| `params` | 名称/值对的字典。 |
| `degree_of_parallelism` | 要用于此作业的并行度。 |
| `runtime_version` | Data Lake Analytics 引擎的运行时版本。 |
| `allow_reuse` | 确定在使用相同设置再次运行时该步骤是否应重新使用以前的结果。 |

以下示例包含一个 ADLA 步骤定义：

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Azure Batch 步骤

| YAML 键 | Description |
| ----- | ----- |
| `compute_target` | 要用于此步骤的 Azure Batch 计算目标。 |
| `inputs` | 输入可以是[InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、 [DataReference](#data-reference)、 [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、 [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 输出可以是[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)或[OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `source_directory` | 包含模块二进制文件、可执行文件、程序集等的目录。 |
| `executable` | 将作为此作业的一部分运行的命令/可执行文件的名称。 |
| `create_pool` | 布尔标志，用于指示在运行作业之前是否创建池。 |
| `delete_batch_job_after_finish` | 布尔标志，用于指示是否在完成后从批处理帐户中删除该作业。 |
| `delete_batch_pool_after_finish` | 布尔标志，指示是否在作业完成后删除池。 |
| `is_positive_exit_code_failure` | 布尔标志，用于指示在任务以正则代码退出时作业是否失败。 |
| `vm_image_urn` | 如果 `True``create_pool`，则 VM 将使用 `VirtualMachineConfiguration`。 |
| `pool_id` | 要在其中运行作业的池的 ID。 |
| `allow_reuse` | 确定在使用相同设置再次运行时该步骤是否应重新使用以前的结果。 |

下面的示例包含一个 Azure Batch 步骤定义：

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Databricks 步骤

| YAML 键 | Description |
| ----- | ----- |
| `compute_target` | 要用于此步骤的 Azure Databricks 计算目标。 |
| `inputs` | 输入可以是[InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、 [DataReference](#data-reference)、 [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、 [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 输出可以是[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)或[OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `run_name` | 此运行的 Databricks 中的名称。 |
| `source_directory` | 包含脚本和其他文件的目录。 |
| `num_workers` | Databricks 运行群集的静态辅助进程数。 |
| `runconfig` | `.runconfig` 文件的路径。 此文件是[RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)类的 YAML 表示形式。 有关此文件结构的详细信息，请参阅[runconfigschema](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)。 |
| `allow_reuse` | 确定在使用相同设置再次运行时该步骤是否应重新使用以前的结果。 |

以下示例包含一个 Databricks 步骤：

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>数据传输步骤

| YAML 键 | Description |
| ----- | ----- |
| `compute_target` | 要用于此步骤的 Azure 数据工厂计算目标。 |
| `source_data_reference` | 输入连接，用作数据传输操作的源。 支持的值为[InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、 [DataReference](#data-reference)、 [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、 [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `destination_data_reference` | 输入连接，用作数据传输操作的目标。 支持的值为[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)和[OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `allow_reuse` | 确定在使用相同设置再次运行时该步骤是否应重新使用以前的结果。 |

以下示例包含一个数据传输步骤：

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Python 脚本步骤

| YAML 键 | Description |
| ----- | ----- |
| `compute_target` | 要用于此步骤的计算目标。 计算目标可以是 Azure 机器学习计算、虚拟机（如 Data Science VM）或 HDInsight。 |
| `inputs` | 输入可以是[InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、 [DataReference](#data-reference)、 [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、 [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)或[PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 输出可以是[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)或[OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `script_name` | Python 脚本的名称（相对于 `source_directory`）。 |
| `source_directory` | 包含脚本、Conda 环境等的目录。 |
| `runconfig` | `.runconfig` 文件的路径。 此文件是[RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)类的 YAML 表示形式。 有关此文件结构的详细信息，请参阅[.runconfig](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)。 |
| `allow_reuse` | 确定在使用相同设置再次运行时该步骤是否应重新使用以前的结果。 |

下面的示例包含 Python 脚本步骤：

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

## <a name="schedules"></a>计划

为管道定义计划时，可以根据时间间隔，对其进行数据存储-触发或重复。 下面是用于定义计划的密钥：

| YAML 键 | Description |
| ----- | ----- |
| `description` | 计划的说明。 |
| `recurrence` | 包含重复设置（如果计划定期发生）。 |
| `pipeline_parameters` | 管道所需的任何参数。 |
| `wait_for_provisioning` | 是否等待计划的预配完成。 |
| `wait_timeout` | 超时前等待的秒数。 |
| `datastore_name` | 用于监视已修改/已添加 blob 的数据存储。 |
| `polling_interval` | 轮询已修改/添加的 blob 的时间长度（分钟）。 默认值：5分钟。 仅数据存储计划支持。 |
| `data_path_parameter_name` | 要与更改的 blob 路径一起设置的数据路径管道参数的名称。 仅数据存储计划支持。 |
| `continue_on_step_failure` | 如果步骤失败，是否继续在提交的 PipelineRun 中执行其他步骤。 如果提供，将重写管道的 `continue_on_step_failure` 设置。
| `path_on_datastore` | 可选。 用于监视已修改/已添加 blob 的数据存储上的路径。 路径位于数据存储的容器下，因此，计划监视器的实际路径为 container/`path_on_datastore`。 如果没有，则监视数据存储容器。 不会监视 `path_on_datastore` 的子文件夹中进行的添加/修改。 仅数据存储计划支持。 |

下面的示例包含用于数据存储触发的计划的定义：

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

定义**定期计划**时，请在 `recurrence`下使用以下项：

| YAML 键 | Description |
| ----- | ----- |
| `frequency` | 计划重复的频率。 有效值为 `"Minute"`、`"Hour"`、`"Day"`、`"Week"`或 `"Month"`。 |
| `interval` | 计划触发的频率。 整数值是在重新触发计划之前要等待的时间单位数。 |
| `start_time` | 计划的开始时间。 值的字符串格式是 `YYYY-MM-DDThh:mm:ss`。 如果未提供启动时间，则会立即运行第一个工作负荷，并基于计划运行未来的工作负荷。 如果开始时间是过去的时间，则在下一个计算的运行时运行第一个工作负荷。 |
| `time_zone` | 开始时间所在的时区。 如果未提供时区，则使用 UTC。 |
| `hours` | 如果 `frequency` `"Day"` 或 `"Week"`，则可以指定从0到23之间的一个或多个整数（用逗号分隔），作为管道应运行的时间。 只能使用 `time_of_day` 或 `hours` 和 `minutes`。 |
| `minutes` | 如果 `frequency` `"Day"` 或 `"Week"`，则可以指定一个或多个介于0到59之间的整数（以逗号分隔），作为管道应运行的小时数。 只能使用 `time_of_day` 或 `hours` 和 `minutes`。 |
| `time_of_day` | 如果 `frequency` `"Day"` 或 `"Week"`，则可以指定运行计划的时间。 值的字符串格式是 `hh:mm`。 只能使用 `time_of_day` 或 `hours` 和 `minutes`。 |
| `week_days` | 如果 `"Week"``frequency`，则可以指定一天或多天（用逗号分隔），以供计划运行。 有效值为 `"Monday"`、`"Tuesday"`、`"Wednesday"`、`"Thursday"`、`"Friday"`、`"Saturday"`和 `"Sunday"`。 |

以下示例包含定期计划的定义：

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>后续步骤

了解如何[使用适用于 Azure 机器学习的 CLI 扩展](service/reference-azure-machine-learning-cli.md)。