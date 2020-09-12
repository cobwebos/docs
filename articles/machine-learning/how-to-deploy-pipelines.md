---
title: 发布 ML 管道
titleSuffix: Azure Machine Learning
description: 通过机器学习管道和用于 Python 的 Azure 机器学习 SDK 运行机器学习工作流。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: 08529d1bb50a1a5d5b3c7d0296aa36f021f45e98
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646086"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>发布和跟踪机器学习管道

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文将演示如何将机器学习管道与同事或客户共享。

机器学习管道是机器学习任务的可重复使用的工作流。 管道的一个优点是协作增加。 你还可以对管道进行版本处理，以允许客户在使用新版本时使用当前模型。 

## <a name="prerequisites"></a>先决条件

* 创建 [Azure 机器学习工作区](how-to-manage-workspace.md) 以容纳所有管道资源

* [配置开发环境](how-to-configure-environment.md)以安装 Azure 机器学习 sdk，或使用已安装 sdk 的[Azure 机器学习计算实例](concept-compute-instance.md)

* 创建并运行机器学习管道，如以下 [教程：生成用于批处理评分的 Azure 机器学习管道](tutorial-pipeline-batch-scoring-classification.md)。 有关其他选项，请参阅 [AZURE 机器学习 SDK 中创建和运行机器学习管道](how-to-create-your-first-pipeline.md)

## <a name="publish-a-pipeline"></a>发布管道

启动并运行管道后，可以发布管道，使其与不同的输入一起运行。 为了使已发布的管道的 REST 终结点接受参数，你必须将管道配置为对 `PipelineParameter` 将发生变化的参数使用对象。

1. 若要创建管道参数，请使用带默认值的 [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py&preserve-view=true) 对象。

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
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. 发布此管道，调用时它会接受参数。

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>运行已发布的管道

所有已发布的管道都具有 REST 终结点。 使用管道终结点，可以从任何外部系统（包括非 Python 客户端）触发管道运行。 在批量评分和重新训练方案中，此终结点支持“托管可重复性”。

若要调用前面的管道的运行，需要 Azure Active Directory authentication 标头令牌。 若要获取此类令牌，请参阅 [AzureCliAuthentication 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py&preserve-view=true) 参考和 Azure 机器学习笔记本中的 [身份验证](https://aka.ms/pl-restep-auth) 。

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>创建版本受控的管道终结点

可以创建包含多个已发布管道的管道终结点。 这为你提供了一个固定的 REST 终结点，因为你可以循环访问和更新 ML 管道。

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>将作业提交到管道终结点

可将作业提交到管道终结点的默认版本：

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

还可将作业提交到特定的版本：

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

可以使用 REST API 来完成相同的操作：

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

## <a name="use-published-pipelines-in-the-studio"></a>在工作室中使用已发布的管道

也可以从工作室运行已发布的管道：

1. 登录到 [Azure 机器学习工作室](https://ml.azure.com)。

1. [查看工作区](how-to-manage-workspace.md#view)。

1. 在左侧选择“终结点”。

1. 在顶部选择“管道终结点”。
 ![机器学习的已发布管道列表](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. 选择要运行的特定管道，使用或查看管道终结点的先前运行的结果。

## <a name="disable-a-published-pipeline"></a>禁用已发布的管道

若要在已发布管道的列表中隐藏某个管道，请在工作室或 SDK 中禁用它：

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

可以使用 `p.enable()` 再次启用它。 有关详细信息，请参阅 [PublishedPipeline 类](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py&preserve-view=true)参考。

## <a name="next-steps"></a>后续步骤

- 使用 [GitHub 上的这些 Jupyter Notebook](https://aka.ms/aml-pipeline-readme) 以进一步探索机器学习管道。
- 参阅有关 [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py&preserve-view=true) 包和 [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true) 包的 SDK 参考帮助信息。
- 参阅[操作指南](how-to-debug-pipelines.md)，获取有关调试管道和排查管道问题的提示。
