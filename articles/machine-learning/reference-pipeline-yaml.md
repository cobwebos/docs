---
title: 机器学习管道 YAML
titleSuffix: Azure Machine Learning
description: 了解如何使用 YAML 文件定义机器学习管道。 YAML 管道定义与适用于 Azure CLI 的机器学习扩展配合使用。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.custom: tracking-python
ms.openlocfilehash: a519519d5728307847b5d92f9ae5ce3e739e3ba6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84560951"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>在 YAML 中定义机器学习管道

了解如何在 [YAML](https://yaml.org/) 中定义机器学习管道。 使用适用于 Azure CLI 的机器学习扩展时，许多管道相关命令都要求提供一个用于定义管道的 YAML 文件。

下表列出了在 YAML 中定义管道时目前支持和不支持的内容：

| 步骤类型 | 支持？ |
| ----- | :-----: |
| PythonScriptStep | 是 |
| ParallelRunStep | 是 |
| AdlaStep | 是 |
| AzureBatchStep | 是 |
| DatabricksStep | 是 |
| DataTransferStep | 是 |
| AutoMLStep | 否 |
| HyperDriveStep | 否 |
| ModuleStep | 是 |
| MPIStep | 否 |
| EstimatorStep | 否 |

## <a name="pipeline-definition"></a>管道定义

管道定义使用以下对应于 [Pipelines](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py) 类的键：

| YAML 键 | 说明 |
| ----- | ----- |
| `name` | 管道的说明。 |
| `parameters` | 管道的参数。 |
| `data_reference` | 定义在运行中提供数据的方式和位置。 |
| `default_compute` | 默认的计算目标，管道中的所有步骤将在其上运行。 |
| `steps` | 管道中使用的步骤。 |

## <a name="parameters"></a>parameters

`parameters` 节使用以下对应于 [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py) 类的键：

| YAML 键 | 说明 |
| ---- | ---- |
| `type` | 参数的值类型。 有效类型为 `string`、`int`、`float`、`bool` 或 `datapath`。 |
| `default` | 默认值。 |

每个参数已命名。 例如，以下 YAML 代码片段定义名为 `NumIterationsParameter`、`DataPathParameter` 和 `NodeCountParameter` 的三个参数：

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

`data_references` 节使用以下对应于 [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py) 的键：

| YAML 键 | 说明 |
| ----- | ----- |
| `datastore` | 要引用的数据存储。 |
| `path_on_datastore` | 数据引用在后备存储中的相对路径。 |

每个数据引用包含在一个键中。 例如，以下 YAML 代码片段定义存储在键中的名为 `employee_data` 的数据引用：

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

步骤定义计算环境，以及要在环境中运行的文件。 若要定义步骤的类型，请使用 `type` 键：

| 步骤类型 | 说明 |
| ----- | ----- |
| `AdlaStep` | 使用 Azure Data Lake Analytics 运行 U-SQL 脚本。 对应于 [AdlaStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) 类。 |
| `AzureBatchStep` | 使用 Azure Batch 运行作业。 对应于 [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) 类。 |
| `DatabricsStep` | 添加 Databricks 笔记本、Python 脚本或 JAR。 对应于 [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) 类。 |
| `DataTransferStep` | 在存储选项之间传输数据。 对应于 [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) 类。 |
| `PythonScriptStep` | 运行 Python 脚本。 对应于 [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) 类。 |
| `ParallelRunStep` | 运行 Python 脚本，以异步方式并行处理大量数据。 对应于 [ParallelRunStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?view=azure-ml-py) 类。 |

### <a name="adla-step"></a>ADLA 步骤

| YAML 键 | 说明 |
| ----- | ----- |
| `script_name` | U-SQL 脚本的名称（相对于 `source_directory`）。 |
| `compute_target` | 用于此步骤的 Azure Data Lake 计算目标。 |
| `parameters` | 管道的[参数](#parameters)。 |
| `inputs` | 输入可以是 [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、[DataReference](#data-reference)、[PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、[Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、[DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py) 或 [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 输出可以是 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 或 [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `source_directory` | 包含脚本、程序集等的目录。 |
| `priority` | 用于当前作业的优先级值。 |
| `params` | 名称/值对的字典。 |
| `degree_of_parallelism` | 用于此作业的并行度。 |
| `runtime_version` | Data Lake Analytics 引擎的运行时版本。 |
| `allow_reuse` | 确定当使用相同的设置再次运行时，该步骤是否应重用以前的结果。 |

以下示例包含 ADLA 步骤定义：

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

| YAML 键 | 说明 |
| ----- | ----- |
| `compute_target` | 用于此步骤的 Azure Batch 计算目标。 |
| `inputs` | 输入可以是 [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、[DataReference](#data-reference)、[PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、[Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、[DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py) 或 [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 输出可以是 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 或 [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `source_directory` | 包含模块二进制文件、可执行文件、程序集等的目录。 |
| `executable` | 要作为此作业的一部分运行的命令/可执行文件的名称。 |
| `create_pool` | 布尔标志，指示在运行作业之前是否创建池。 |
| `delete_batch_job_after_finish` | 布尔标志，指示在完成作业后是否从 Batch 帐户中删除该作业。 |
| `delete_batch_pool_after_finish` | 布尔标志，指示在完成作业后是否删除池。 |
| `is_positive_exit_code_failure` | 布尔标志，指示在任务退出并返回正代码时作业是否失败。 |
| `vm_image_urn` | 如果 `create_pool` 为 `True`，则 VM 将使用 `VirtualMachineConfiguration`。 |
| `pool_id` | 要在其中运行作业的池的 ID。 |
| `allow_reuse` | 确定当使用相同的设置再次运行时，该步骤是否应重用以前的结果。 |

以下示例包含 Azure Batch 步骤定义：

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

| YAML 键 | 说明 |
| ----- | ----- |
| `compute_target` | 用于此步骤的 Azure Databricks 计算目标。 |
| `inputs` | 输入可以是 [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、[DataReference](#data-reference)、[PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、[Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、[DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py) 或 [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 输出可以是 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 或 [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `run_name` | 此运行在 Databricks 中的名称。 |
| `source_directory` | 包含脚本和其他文件的目录。 |
| `num_workers` | Databricks 运行群集的辅助角色的静态编号。 |
| `runconfig` | `.runconfig` 文件的路径。 此文件是 [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) 类的 YAML 表示形式。 有关此文件的结构的详细信息，请参阅 [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)。 |
| `allow_reuse` | 确定当使用相同的设置再次运行时，该步骤是否应重用以前的结果。 |

以下示例包含 Databricks 步骤：

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

| YAML 键 | 说明 |
| ----- | ----- |
| `compute_target` | 用于此步骤的 Azure 数据工厂计算目标。 |
| `source_data_reference` | 充当数据传输操作的源的输入连接。 支持的值为 [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、[DataReference](#data-reference)、[PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、[Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、[DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py) 或 [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `destination_data_reference` | 充当数据传输操作的目标的输入连接。 支持的值为 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 和 [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `allow_reuse` | 确定当使用相同的设置再次运行时，该步骤是否应重用以前的结果。 |

以下示例包含数据传输步骤：

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

| YAML 键 | 说明 |
| ----- | ----- |
| `inputs` | 输入可以是 [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py)、[DataReference](#data-reference)、[PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py)、[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)、[Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、[DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py) 或 [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 输出可以是 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 或 [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `script_name` | Python 脚本的名称（相对于 `source_directory`）。 |
| `source_directory` | 包含脚本、Conda 环境等的目录。 |
| `runconfig` | `.runconfig` 文件的路径。 此文件是 [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) 类的 YAML 表示形式。 有关此文件的结构的详细信息，请参阅 [runconfig.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)。 |
| `allow_reuse` | 确定当使用相同的设置再次运行时，该步骤是否应重用以前的结果。 |

以下示例包含 Python 脚本步骤：

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

### <a name="parallel-run-step"></a>并行运行步骤

| YAML 键 | 说明 |
| ----- | ----- |
| `inputs` | 输入可以是 [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py)、[DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py) 或 [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py)。 |
| `outputs` | 输出可以是 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 或 [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py)。 |
| `script_name` | Python 脚本的名称（相对于 `source_directory`）。 |
| `source_directory` | 包含脚本、Conda 环境等的目录。 |
| `parallel_run_config` | `parallel_run_config.yml` 文件的路径。 此文件是 [ParallelRunConfig](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig?view=azure-ml-py) 类的 YAML 表示形式。 |
| `allow_reuse` | 确定当使用相同的设置再次运行时，该步骤是否应重用以前的结果。 |

以下示例包含并行运行步骤：

```yaml
pipeline:
    description: SamplePipelineFromYaml
    default_compute: cpu-cluster
    data_references:
        MyMinistInput:
            dataset_name: mnist_sample_data
    parameters:
        PipelineParamTimeout:
            type: int
            default: 600
    steps:        
        Step1:
            parallel_run_config: "yaml/parallel_run_config.yml"
            type: "ParallelRunStep"
            name: "parallel-run-step-1"
            allow_reuse: True
            arguments:
            - "--progress_update_timeout"
            - parameter:timeout_parameter
            - "--side_input"
            - side_input:SideInputData
            parameters:
                timeout_parameter:
                    source: PipelineParamTimeout
            inputs:
                InputData:
                    source: MyMinistInput
            side_inputs:
                SideInputData:
                    source: Output4
                    bind_mode: mount
            outputs:
                OutputDataStep2:
                    destination: Output5
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="pipeline-with-multiple-steps"></a>包含多个步骤的管道 

| YAML 键 | 说明 |
| ----- | ----- |
| `steps` | 具有一个或多个 PipelineStep 定义的序列。 请注意，步骤 `outputs` 的 `destination` 键将成为下一步 `inputs` 的 `source` 键。| 

```yaml
pipeline:
    name: SamplePipelineFromYAML
    description: Sample multistep YAML pipeline
    data_references:
        TitanicDS:
            dataset_name: 'titanic_ds'
            bind_mode: download
    default_compute: cpu-cluster
    steps:
        Dataprep:
            type: "PythonScriptStep"
            name: "DataPrep Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "prep.py"
            arguments:
            - '--train_path'
            - output:train_path
            - '--test_path'
            - output:test_path
            allow_reuse: True
            inputs:
                titanic_ds:
                    source: TitanicDS
                    bind_mode: download
            outputs:
                train_path:
                    destination: train_csv
                    datastore: workspaceblobstore
                test_path:
                    destination: test_csv
        Training:
            type: "PythonScriptStep"
            name: "Training Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "train.py"
            arguments:
            - "--train_path"
            - input:train_path
            - "--test_path"
            - input:test_path
            inputs:
                train_path:
                    source: train_csv
                    bind_mode: download
                test_path:
                    source: test_csv
                    bind_mode: download

```

## <a name="schedules"></a>计划

为管道定义计划时，该计划可以由数据存储触发，或根据某个时间间隔重复运行。 下面是用于定义计划的键：

| YAML 键 | 说明 |
| ----- | ----- |
| `description` | 计划的说明。 |
| `recurrence` | 包含重复设置（如果计划是重复性的）。 |
| `pipeline_parameters` | 管道所需的任何参数。 |
| `wait_for_provisioning` | 是否等待计划预配完成。 |
| `wait_timeout` | 超时之前等待的秒数。 |
| `datastore_name` | 要在其中监视已修改/已添加的 Blob 的数据存储。 |
| `polling_interval` | 轮询已修改/已添加的 Blob 的间隔时间（分钟）。 默认值：5 分钟。 仅支持数据存储计划。 |
| `data_path_parameter_name` | 要使用更改的 Blob 路径设置的数据路径管道参数的名称。 仅支持数据存储计划。 |
| `continue_on_step_failure` | 当某个步骤失败时，是否继续执行提交的 PipelineRun 中的其他步骤。 如果提供此键，将替代管道的 `continue_on_step_failure` 设置。
| `path_on_datastore` | 可选。 要在其中监视已修改/已添加的 Blob 的数据存储上的路径。 该路径位于数据存储的容器下，因此，计划监视的实际路径是 container/`path_on_datastore`。 如果没有此路径，将监视数据存储容器。 不会监视在 `path_on_datastore` 的子文件夹中进行的添加/修改。 仅支持数据存储计划。 |

以下示例包含数据存储触发的计划的定义：

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

定义**重复性计划**时，请在 `recurrence` 下使用以下键：

| YAML 键 | 说明 |
| ----- | ----- |
| `frequency` | 计划的重复频率。 有效值为 `"Minute"`、`"Hour"`、`"Day"`、`"Week"` 或 `"Month"`。 |
| `interval` | 计划的激发频率。 整数值为再次激发计划之前要等待的时间单位数。 |
| `start_time` | 计划的开始时间。 该值的字符串格式为 `YYYY-MM-DDThh:mm:ss`。 如果未提供开始时间，则第一个工作负荷会即时运行，以后的工作负荷将按计划运行。 如果开始时间是过去的时间，则第一个工作负荷将在下一个计算的运行时间运行。 |
| `time_zone` | 开始时间所用的时区。 如果未提供时区，将使用 UTC。 |
| `hours` | 如果 `frequency` 是 `"Day"` 或 `"Week"`，可以指定从 0 到 23 的一个或多个整数（用逗号分隔），表示一天中应运行管道的小时时间。 只能使用 `time_of_day` 或 `hours` 和 `minutes`。 |
| `minutes` | 如果 `frequency` 是 `"Day"` 或 `"Week"`，可以指定从 0 到 59 的一个或多个整数（用逗号分隔），表示指定的小时中应运行管道的分钟时间。 只能使用 `time_of_day` 或 `hours` 和 `minutes`。 |
| `time_of_day` | 如果 `frequency` 是 `"Day"` 或 `"Week"`，可以指定一天中要运行计划的时间。 该值的字符串格式为 `hh:mm`。 只能使用 `time_of_day` 或 `hours` 和 `minutes`。 |
| `week_days` | 如果 `frequency` 是 `"Week"`，可以指定应运行计划的一个或多个星期日期（用逗号分隔）。 有效值为 `"Monday"`、`"Tuesday"`、`"Wednesday"`、`"Thursday"`、`"Friday"`、`"Saturday"` 和 `"Sunday"`。 |

以下示例包含重复性计划的定义：

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

了解如何[使用 Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)。
