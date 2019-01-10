---
title: 创建、运行和跟踪机器学习管道
titleSuffix: Azure Machine Learning service
description: 使用适用于 Python 的 Azure 机器学习 SDK 创建和运行机器学习管道。 使用管道来创建和管理将各个机器学习 (ML) 阶段整合到一起的工作流。 这些阶段包括数据准备、模型训练、模型部署以及推断。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6c6472b824eefdd1954f3645c69090d1fb5455de
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754452"
---
# <a name="create-and-run-a-machine-learning-pipeline-by-using-azure-machine-learning-sdk"></a>使用 Azure 机器学习 SDK 创建和运行机器学习管道

本文介绍如何使用 [Azure 机器学习 SDK](https://aka.ms/aml-sdk) 创建、发布、运行和跟踪[机器学习管道](concept-ml-pipelines.md)。  这些管道可以帮助创建和管理将各个机器学习阶段拼接在一起的工作流。 每个管道阶段（例如数据准备和模型训练）可以包含一个或多个步骤。

Azure 机器学习服务[工作区](how-to-manage-workspace.md)的成员可以看到创建的管道。 

管道使用远程计算目标进行计算，以及存储与该管道关联的中间数据和最终数据。 管道可以在支持的 [Azure 存储文档](https://docs.microsoft.com/azure/storage/)位置读取和写入数据。

>[!Note]
>如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 试用 [Azure 机器学习服务免费版或付费版](http://aka.ms/AMLFree)。

## <a name="prerequisites"></a>先决条件

* [配置开发环境](how-to-configure-environment.md)以安装 Azure 机器学习 SDK。

* 创建用于保存所有管道资源的 [Azure 机器学习工作区](how-to-configure-environment.md#workspace)。 

 ```python
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

### <a name="set-up-compute"></a>设置计算

在 Azure 机器学习中，术语“计算”（或“计算目标”）是指在机器学习管道中执行计算步骤的计算机或群集。 例如，可以创建 Azure 机器学习计算用于运行步骤。

```python
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

## <a name="construct-your-pipeline-steps"></a>构造管道步骤

现在，可以开始定义管道步骤。 可以通过 Azure 机器学习 SDK 使用许多内置步骤。 其中最基本的步骤是 `PythonScriptStep`，即在指定的计算目标中运行 Python 脚本。

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

>[!NOTE]
>定义步骤或生成管道时，不会将任何文件或数据上传到 Azure 机器学习服务。

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

## <a name="submit-the-pipeline"></a>提交管道

提交管道时，Azure 机器学习服务检查每个步骤的依赖项，并上传指定的源目录的快照。 如果未指定源目录，则上传当前的本地目录。

```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
```

第一次运行管道时，Azure 机器学习会：

* 将项目快照从与工作区关联的 Blob 存储下载到计算目标。
* 生成对应于管道中每个步骤的 Docker 映像。
* 将每个步骤的 Docker 映像从容器注册表下载到计算目标。
* 如果在步骤中指定了 `DataReference` 对象，则装载数据存储。 如果不支持装载，则改为将数据复制到计算目标。
* 运行在步骤定义中指定的计算目标中的步骤。 
* 创建项目，例如日志、stdout 和 stderr、指标以及步骤指定的输出。 然后上传这些项目并将其保存在用户的默认数据存储中。

![以管道方式运行实验的图](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

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

若要调用上述管道的运行，需要使用 [AzureCliAuthentication class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py)（AzureCliAuthentication 类）中所述的 Azure Active Directory 身份验证标头令牌。

```python
response = requests.post(published_pipeline1.endpoint, 
    headers=aad_token, 
    json={"ExperimentName": "My_Pipeline",
        "ParameterAssignments": {"pipeline_arg": 20}})
```
## <a name="view-results"></a>查看结果

查看所有管道的列表及其运行详细信息：
1. 登录到 [Azure 门户](https://portal.azure.com/)。  

1. [查看工作区](how-to-manage-workspace.md#view-a-workspace)以查找管道列表。
 ![机器学习管道列表](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. 选择特定的管道以查看运行结果。

## <a name="next-steps"></a>后续步骤
- 使用 [GitHub 上的这些 Jupyter Notebook](https://aka.ms/aml-pipeline-readme) 以进一步探索机器学习管道。
- 阅读有关 [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) 包和 [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) 包的 SDK 参考帮助信息。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
