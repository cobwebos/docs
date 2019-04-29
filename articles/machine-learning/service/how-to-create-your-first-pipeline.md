---
title: 创建、运行和跟踪机器学习管道
titleSuffix: Azure Machine Learning service
description: 使用适用于 Python 的 Azure 机器学习 SDK 创建和运行机器学习管道。 使用管道来创建和管理将各个机器学习 (ML) 阶段整合到一起的工作流。 这些阶段包括数据准备、模型训练、模型部署以及推断。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 01/08/2019
ms.custom: seodec18
ms.openlocfilehash: 2e6bc0fd9de4fdba1188b40c49ebf9459d684d38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819912"
---
# <a name="create-and-run-a-machine-learning-pipeline-by-using-azure-machine-learning-sdk"></a>使用 Azure 机器学习 SDK 创建和运行机器学习管道

本文介绍如何使用 [Azure 机器学习 SDK](https://aka.ms/aml-sdk) 创建、发布、运行和跟踪[机器学习管道](concept-ml-pipelines.md)。  这些管道可以帮助创建和管理将各个机器学习阶段拼接在一起的工作流。 每个管道阶段（例如数据准备和模型训练）可以包含一个或多个步骤。

Azure 机器学习服务[工作区](how-to-manage-workspace.md)的成员可以看到创建的管道。 

管道使用远程计算目标进行计算，以及存储与该管道关联的中间数据和最终数据。 管道可以在支持的 [Azure 存储文档](https://docs.microsoft.com/azure/storage/)位置读取和写入数据。

如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

## <a name="prerequisites"></a>必备组件

* [配置开发环境](how-to-configure-environment.md)以安装 Azure 机器学习 SDK。

* 创建用于保存所有管道资源的 [Azure 机器学习工作区](how-to-configure-environment.md#workspace)。 

  ```python
  from azureml.core import Workspace
  
  ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
  ```

## <a name="set-up-machine-learning-resources"></a>设置机器学习资源

创建运行管道所需的资源：

* 设置一个数据存储，用于访问管道步骤中所需的数据。

* 配置 `DataReference` 对象，以指向驻留在数据存储中或可在数据存储中访问的数据。

* 设置[计算目标](concept-azure-machine-learning-architecture.md#compute-target)，管道步骤将在其上运行。

### <a name="set-up-a-datastore"></a>设置数据存储
数据存储用于存储可供管道访问的数据。 每个工作区有一个默认的数据存储。 可以注册其他数据存储。 

创建工作区时，默认会将 [Azure 文件存储](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)和 [Azure Blob 存储](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)附加到工作区。 Azure 文件存储是工作区的“默认数据存储”，但你也可以使用 Blob 存储作为数据存储。 要了解详细信息，请参阅[确定何时使用 Azure 文件存储、Azure Blob 或 Azure 磁盘](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)。 

```python
# Default datastore (Azure file storage)
def_data_store = ws.get_default_datastore() 

# The above call is equivalent to this 
def_data_store = Datastore(ws, "workspacefilestore")

# Get blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")
```

将数据文件或目录上传到数据存储，以便可以在管道中访问它们。 此示例使用数据存储的 Blob 存储版本：

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
blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

中间数据（或步骤输出）由 [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) 对象表示。 `output_data1` 生成为步骤的输出，并用作一个或多个后续步骤的输入。 `PipelineData` 在步骤之间引入数据依赖项，并在管道中创建隐式执行顺序。

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

## <a name="set-up-compute-target"></a>设置计算目标

在 Azure 机器学习中，术语“计算”（或“计算目标”）是指在机器学习管道中执行计算步骤的计算机或群集。   有关计算目标的完整列表以及如何创建计算目标并将其附加到工作区的详细信息，请参阅[模型训练的计算目标](how-to-set-up-training-targets.md)。  无论是在训练模型还是运行管道步骤，创建和/或附加计算目标的过程都是相同的。 创建并附加计算目标后，请使用[管道步骤](#steps)中的 `ComputeTarget` 对象。

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
 if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                min_nodes = 1, 
                                                                max_nodes = 4)
     # create the compute target
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # Can poll for a minimum number of nodes and for a specific timeout. 
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

### <a id="databricks"></a>Azure Databricks

Azure Databricks 是 Azure 云中基于 Apache Spark 的环境。 它可以用作 Azure 机器学习管道的计算目标。

请先创建 Azure Databricks 工作区，然后再使用该工作区。 若要创建这些资源，请参阅[在 Azure Databricks 中运行 Spark 作业](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)文档。

要将 Azure Databricks 附加为计算目标，请提供以下信息：

* __Databricks 计算名称__：要分配给此计算资源的名称。
* __Databricks 工作区名称__：Azure Databricks 工作区的名称。
* __Databricks 访问令牌__：用于对 Azure Databricks 进行身份验证的访问令牌。 若要生成访问令牌，请参阅[身份验证](https://docs.azuredatabricks.net/api/latest/authentication.html)文档。

以下代码演示如何使用 Azure 机器学习 SDK 将 Azure Databricks 附加为计算目标：

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```
### <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics 是 Azure 云中的大数据分析平台。 它可以用作 Azure 机器学习管道的计算目标。

使用该平台之前，请先创建 Azure Data Lake Analytics 帐户。 若要创建此资源，请参阅 [Azure Data Lake Analytics 入门](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)文档。

若要将 Data Lake Analytics 附加为计算目标，必须使用 Azure 机器学习 SDK 并提供以下信息：

* __计算名称__：要分配给此计算资源的名称。
* __资源组__：包含 Data Lake Analytics 帐户的资源组。
* __帐户名__：Data Lake Analytics 帐户名。

以下代码演示如何将 Data Lake Analytics 附加为计算目标：

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Azure 机器学习管道只能处理 Data Lake Analytics 帐户的默认数据存储中存储的数据。 如果需要处理的数据不在默认存储中，可以在训练之前使用 [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) 复制数据。

## <a id="steps"></a>构造管道步骤

创建计算目标并将其附加到工作区后，就可以定义管道步骤了。 可以通过 Azure 机器学习 SDK 使用许多内置步骤。 是最基本的这些步骤[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py)，指定的计算目标中运行 Python 脚本：

```python
trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", processed_data1],
    inputs=[blob_input_data],
    outputs=[processed_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

定义步骤后，使用其中的部分或所有步骤生成管道。

> [!NOTE]
> 定义步骤或生成管道时，不会将任何文件或数据上传到 Azure 机器学习服务。

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

以下示例使用前面创建的 Azure Databricks 计算目标： 

```python
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

有关详细信息，请参阅[azure 管道步骤包](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)并[管道类](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py)引用。

## <a name="submit-the-pipeline"></a>提交管道

提交管道时，Azure 机器学习服务检查每个步骤的依赖项，并上传指定的源目录的快照。 如果未指定源目录，则上传当前的本地目录。 快照也存储为你的工作区中的实验的一部分。

> [!IMPORTANT]
> 若要防止文件被包含在快照中，创建[.gitignore](https://git-scm.com/docs/gitignore)或`.amlignore`文件的目录中，并向其中添加文件。 `.amlignore`文件使用相同的语法，作为模式[.gitignore](https://git-scm.com/docs/gitignore)文件。 如果这两个文件存在，`.amlignore`文件具有优先权。
>
> 有关详细信息，请参阅[快照](concept-azure-machine-learning-architecture.md#snapshot)。

```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

第一次运行管道时，Azure 机器学习会：

* 将项目快照从与工作区关联的 Blob 存储下载到计算目标。
* 生成对应于管道中每个步骤的 Docker 映像。
* 将每个步骤的 Docker 映像从容器注册表下载到计算目标。
* 如果在步骤中指定了 `DataReference` 对象，则装载数据存储。 如果不支持装载，则改为将数据复制到计算目标。
* 运行在步骤定义中指定的计算目标中的步骤。 
* 创建项目，例如日志、stdout 和 stderr、指标以及步骤指定的输出。 然后上传这些项目并将其保存在用户的默认数据存储中。

![以管道方式运行实验的图](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

有关详细信息，请参阅[试验类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)引用。

## <a name="publish-a-pipeline"></a>发布管道

可以发布一个管道，以便稍后结合不同的输入运行该管道。 要使已发布的管道的 REST 终结点接受参数，在发布之前必须将该管道参数化。 

1. 若要创建管道参数，请使用带默认值的 [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) 对象。

   ```python
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
   published_pipeline1 = pipeline1.publish(
       name="My_Published_Pipeline", 
       description="My Published Pipeline Description")
   ```

## <a name="run-a-published-pipeline"></a>运行已发布的管道

所有已发布的管道都具有 REST 终结点。 此终结点可以从非 Python 客户端等外部系统调用管道的运行。 在批量评分和重新训练方案中，此终结点支持“托管可重复性”。

若要调用上述管道的运行，需要使用 [AzureCliAuthentication 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py)（AzureCliAuthentication 类）中所述的 Azure Active Directory 身份验证标头令牌或在 [Azure 机器学习中的身份验证](https://aka.ms/pl-restep-auth)笔记本中获取详细信息。

```python
response = requests.post(published_pipeline1.endpoint, 
    headers=aad_token, 
    json={"ExperimentName": "My_Pipeline",
        "ParameterAssignments": {"pipeline_arg": 20}})
```
## <a name="view-results"></a>查看结果

查看所有管道的列表及其运行详细信息：
1. 登录到 [Azure 门户](https://portal.azure.com/)。  

1. [查看工作区](how-to-manage-workspace.md#view)以查找管道列表。
 ![机器学习管道列表](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. 选择特定的管道以查看运行结果。

## <a name="next-steps"></a>后续步骤
- 使用 [GitHub 上的这些 Jupyter Notebook](https://aka.ms/aml-pipeline-readme) 以进一步探索机器学习管道。
- 阅读有关 [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 包和 [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) 包的 SDK 参考帮助信息。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
