---
title: 创建、运行和跟踪机器学习管道
titleSuffix: Azure Machine Learning
description: 使用适用于 Python 的 Azure 机器学习 SDK 创建和运行机器学习管道。 使用 ML 管道来创建和管理将机器学习（ML）阶段结合在一起的工作流。 这些阶段包括数据准备、模型定型、模型部署和推理/评分。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 3dc439c352bb3e6e56fae4b83d783da94720bfe1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818402"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Azure 机器学习 SDK 中创建和运行机器学习管道
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何使用 [Azure 机器学习 SDK](concept-ml-pipelines.md) 创建、发布、运行和跟踪[机器学习管道](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。  使用**ML 管道**创建一个工作流，该工作流将拼结多个 ML 阶段，然后将该管道发布到 Azure 机器学习工作区，以便以后访问或与他人共享。  ML 管道非常适合用于批处理评分方案，使用各种计算，重复使用步骤，而不是重新运行，以及与他人共享 ML 工作流。 

虽然你可以使用一种称为[Azure 管道](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml)的不同类型的管道来实现 ML 任务的 CI/CD 自动化，但这种类型的管道永远不会存储在工作区中。 [比较这些不同的管道](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use)。

ML 管道的每个阶段（如数据准备和模型定型）都可以包含一个或多个步骤。

你创建的 ML 管道对 Azure 机器学习[工作区](how-to-manage-workspace.md)的成员可见。 

ML 管道使用远程计算目标进行计算，并使用与该管道关联的中间数据和最终数据的存储。 它们可在支持的[Azure 存储](https://docs.microsoft.com/azure/storage/)位置读取和写入数据。

如果还没有 Azure 订阅，请在开始前创建免费帐户。 试用[Azure 机器学习免费或付费版本](https://aka.ms/AMLFree)。

## <a name="prerequisites"></a>先决条件

* 创建用于保存所有管道资源的 [Azure 机器学习工作区](how-to-manage-workspace.md)。

* [配置开发环境](how-to-configure-environment.md)以安装 Azure 机器学习 SDK，或使用已安装 sdk 的[Azure 机器学习笔记本 VM](concept-azure-machine-learning-architecture.md#compute-instance) 。

首先附加工作区：

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```


## <a name="set-up-machine-learning-resources"></a>设置机器学习资源

创建运行 ML 管道所需的资源：

* 设置一个数据存储，用于访问管道步骤中所需的数据。

* 配置 `DataReference` 对象，以指向驻留在数据存储中或可在数据存储中访问的数据。

* 设置[计算目标](concept-azure-machine-learning-architecture.md#compute-targets)，管道步骤将在其上运行。

### <a name="set-up-a-datastore"></a>设置数据存储

数据存储用于存储可供管道访问的数据。 每个工作区有一个默认的数据存储。 可以注册其他数据存储。 

创建工作区时， [Azure 文件](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)和[azure Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)会附加到工作区。 已注册默认的数据存储，用于连接到 Azure Blob 存储。 要了解详细信息，请参阅[确定何时使用 Azure 文件存储、Azure Blob 或 Azure 磁盘](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)。 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

将数据文件或目录上传到数据存储，以便可以在管道中访问它们。 此示例使用 Blob 存储作为数据存储：

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

一个管道包含一个或多个步骤。 步骤是在计算目标上运行的单元。 步骤可能会使用数据源，并生成“中间”数据。 步骤可以创建数据，例如模型、包含模型和依赖文件的目录，或临时数据。 然后，此数据可供管道中的其他后续步骤使用。

### <a name="configure-data-reference"></a>配置数据引用

你刚刚创建了一个可在管道中作为步骤输入引用的数据源。 管道中的数据源由 [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) 对象表示。 `DataReference` 对象指向驻留在数据存储中的或者可从数据存储访问的数据。

```python
from azureml.data.data_reference import DataReference

blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

中间数据（或步骤输出）由 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 对象表示。 `output_data1` 生成为步骤的输出，并用作一个或多个后续步骤的输入。 `PipelineData` 在步骤之间引入数据依赖项，并在管道中创建隐式执行顺序。

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

## <a name="set-up-compute-target"></a>设置计算目标

在 Azure 机器学习中，术语 "computes__ （或__计算目标__）" 是指在机器学习管道中执行计算步骤的计算机或群集。   有关计算目标的完整列表以及如何创建计算目标并将其附加到工作区的详细信息，请参阅[模型训练的计算目标](how-to-set-up-training-targets.md)。  无论是在训练模型还是运行管道步骤，创建和/或附加计算目标的过程都是相同的。 创建并附加计算目标后，请使用`ComputeTarget`管道步骤[中的 ](#steps) 对象。

> [!IMPORTANT]
> 内部远程作业不支持对计算目标执行管理操作。 由于机器学习管道作为远程作业提交，因此请勿对管道内的计算目标使用管理操作。

下面是创建和附加计算目标的示例：

* Azure 机器学习计算
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Azure 机器学习计算

可以创建 Azure 机器学习计算用于运行步骤。

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

### <a id="databricks"></a>Azure Databricks

Azure Databricks 是 Azure 云中基于 Apache Spark 的环境。 它可以用作 Azure 机器学习管道的计算目标。

请先创建 Azure Databricks 工作区，然后再使用该工作区。 若要创建工作区资源，请参阅[对 Azure Databricks 文档运行 Spark 作业](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。

要将 Azure Databricks 附加为计算目标，请提供以下信息：

* __Databricks 计算名称__：要分配给此计算资源的名称。
* __Databricks 工作区名称__： Azure Databricks 工作区的名称。
* __Databricks 访问令牌__：用于对 Azure Databricks 进行身份验证的访问令牌。 若要生成访问令牌，请参阅[身份验证](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html)文档。

以下代码演示如何使用 Azure 机器学习 SDK 将 Azure Databricks 附加为计算目标：

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

有关更详细的示例，请参阅 GitHub 上的[示例笔记本](https://aka.ms/pl-databricks)。

### <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics 是 Azure 云中的大数据分析平台。 它可以用作 Azure 机器学习管道的计算目标。

使用该平台之前，请先创建 Azure Data Lake Analytics 帐户。 若要创建此资源，请参阅 [Azure Data Lake Analytics 入门](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)文档。

若要将 Data Lake Analytics 附加为计算目标，必须使用 Azure 机器学习 SDK 并提供以下信息：

* __计算名称__：要分配给此计算资源的名称。
* __资源组__：包含 Data Lake Analytics 帐户的资源组。
* __帐户名称__： Data Lake Analytics 帐户名称。

以下代码演示如何将 Data Lake Analytics 附加为计算目标：

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

有关更详细的示例，请参阅 GitHub 上的[示例笔记本](https://aka.ms/pl-adla)。

> [!TIP]
> Azure 机器学习管道只能处理 Data Lake Analytics 帐户的默认数据存储中存储的数据。 如果需要处理的数据不在默认存储中，可以在训练之前使用 [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) 复制数据。

## <a id="steps"></a>构造管道步骤

创建计算目标并将其附加到工作区后，就可以定义管道步骤了。 可以通过 Azure 机器学习 SDK 使用许多内置步骤。 这些步骤中最基本的是[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)，它在指定的计算目标中运行 Python 脚本：

```python
from azureml.pipeline.steps import PythonScriptStep

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", output_data1],
    inputs=[blob_input_data],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

在协作环境中使用管道时，重复使用以前的结果（`allow_reuse`）是关键的，因为消除不必要的重新运行可提供灵活性。 当步骤的 script_name、输入和参数保持不变时，将使用默认行为。 当重复使用该步骤的输出时，该作业不会提交到计算，而是从上一次运行的结果立即用于下一步的运行。 如果 `allow_reuse` 设置为 "false"，则在管道执行过程中将始终为此步骤生成新的运行。 

定义步骤后，使用其中的部分或所有步骤生成管道。

> [!NOTE]
> 定义步骤或生成管道时，不会将任何文件或数据上传到 Azure 机器学习。

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

以下示例使用前面创建的 Azure Databricks 计算目标： 

```python
from azureml.pipeline.steps import DatabricksStep

dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

有关详细信息，请参阅[azure 管道-步骤包](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)和[管道类](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py)引用。

## <a name="submit-the-pipeline"></a>提交管道

提交管道时，Azure 机器学习会检查每个步骤的依赖项，并上传指定的源目录的快照。 如果未指定源目录，则上传当前的本地目录。 快照也作为工作区试验的一部分存储。

> [!IMPORTANT]
> 若要防止文件包含在快照中, 请在目录中创建 [.gitignore](https://git-scm.com/docs/gitignore) 或`.amlignore`文件, 并将文件添加到其中。 `.amlignore`文件使用与 [.gitignore](https://git-scm.com/docs/gitignore) 文件相同的语法和模式。 如果这两个文件都存在，则 `.amlignore` 文件优先。
>
> 有关详细信息，请参阅[快照](concept-azure-machine-learning-architecture.md#snapshots)。

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

第一次运行管道时，Azure 机器学习会：

* 将项目快照从与工作区关联的 Blob 存储下载到计算目标。
* 生成对应于管道中每个步骤的 Docker 映像。
* 从容器注册表中将每个步骤的 Docker 映像下载到计算目标。
* 如果在步骤中指定了 `DataReference` 对象，则会装载数据存储。 如果不支持装载，则改为将数据复制到计算目标。
* 运行在步骤定义中指定的计算目标中的步骤。 
* 创建项目，例如日志、stdout 和 stderr、指标以及步骤指定的输出。 然后上传这些项目并将其保存在用户的默认数据存储中。

![以管道方式运行实验的图](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

有关详细信息，请参阅[试验类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)引用。



## <a name="github-tracking-and-integration"></a>GitHub 跟踪和集成

当你开始在源目录为本地 Git 存储库的训练运行时，有关存储库的信息存储在运行历史记录中。 有关详细信息，请参阅[Git integration for Azure 机器学习](concept-train-model-git-integration.md)。

## <a name="publish-a-pipeline"></a>发布管道

可以发布一个管道，以便稍后结合不同的输入运行该管道。 要使已发布的管道的 REST 终结点接受参数，在发布之前必须将该管道参数化。

1. 若要创建管道参数，请使用带默认值的 [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) 对象。

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. 按如下所示，将此 `PipelineParameter` 对象作为参数添加到管道中的任一步骤：

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     target=compute_target,
     source_directory=project_folder)
   ```

3. 发布此管道，调用时它会接受参数。

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>运行已发布的管道

所有已发布的管道都具有 REST 终结点。 此终结点可以从非 Python 客户端等外部系统调用管道的运行。 在批量评分和重新训练方案中，此终结点支持“托管可重复性”。

若要调用前面的管道的运行，需要 Azure Active Directory authentication 标头令牌，如[AzureCliAuthentication 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py)引用中所述，或在 Azure 机器学习笔记本中的[身份验证](https://aka.ms/pl-restep-auth)中获取更多详细信息。

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

### <a name="view-results-of-a-published-pipeline"></a>查看已发布管道的结果

查看所有已发布管道的列表及其运行详细信息：
1. 登录到[Azure 机器学习 studio](https://ml.azure.com)。

1. [查看工作区](how-to-manage-workspace.md#view)以查找管道列表。
 ![机器学习管道列表](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. 选择特定的管道以查看运行结果。

[Azure 机器学习 studio]] （ https://ml.azure.com)中的工作区也提供这些结果。

### <a name="disable-a-published-pipeline"></a>禁用已发布的管道

若要从已发布的管道列表中隐藏管道，请将其禁用：

```
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

您可以使用 `p.enable()`再次启用它。 有关详细信息，请参阅[PublishedPipeline 类](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py)引用。


## <a name="caching--reuse"></a>缓存 & 重用  

为了优化和自定义管道的行为，您可以围绕缓存和重新使用来执行一些操作。 例如，您可以选择：
+ 通过在[步骤定义](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)期间设置 `allow_reuse=False` 来**关闭步骤运行输出的默认重用**。 在协作环境中使用管道时，重复使用是关键的，因为消除不必要的运行可提供灵活性。 但是，您可以选择不使用。
+ 将**哈希扩展到脚本以外**，还可以使用 `hash_paths=['<file or directory']` 为其他文件和目录包含绝对路径或相对路径 source_directory 
+ **为运行中的所有步骤强制执行输出**`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

默认情况下，将启用 `allow_reuse` 的步骤，并且仅对主脚本文件进行哈希处理。 因此，如果给定步骤的脚本保持不变（`script_name`、输入和参数），则会重复使用上一步运行的输出，该作业不会提交到计算，而以前运行的结果立即可用于下一步骤。  

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>后续步骤

- 使用 [GitHub 上的这些 Jupyter Notebook](https://aka.ms/aml-pipeline-readme) 以进一步探索机器学习管道。
- 请参阅适用于[azureml 管道核心](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)包和[azureml 管道-步骤](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)包的 SDK 参考帮助。
- 有关如何调试和排查管道问题的提示，请参阅操作[方法](how-to-debug-pipelines.md)。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
