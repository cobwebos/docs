---
title: 在应用程序见解中调试和排除机器学习管道
titleSuffix: Azure Machine Learning
description: 将日志记录添加到您的训练和批处理评分管道，并在应用程序见解中查看记录的结果。
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: b3e4bf19a7ec153f85483f3c5028e468e06ed7f0
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982355"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines-in-application-insights"></a>在应用程序见解中调试和排除机器学习管道
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

[OpenCensus](https://opencensus.io/quickstart/python/) python 库可用于从脚本将日志路由到应用程序见解。 聚合管道中的日志在一个位置运行，允许您生成查询和诊断问题。 使用应用程序见解将允许您跟踪日志随时间而过，并在运行中比较管道日志。

将日志放在原位后将提供异常和错误消息的历史记录。 由于应用程序见解与 Azure 警报集成，因此还可以根据应用程序见解查询创建警报。

## <a name="prerequisites"></a>先决条件

* 按照以下步骤创建 Azure[机器学习](./how-to-manage-workspace.md)工作区并[创建第一个管道](./how-to-create-your-first-pipeline.md)
* [配置开发环境](./how-to-configure-environment.md)以安装 Azure 机器学习 SDK。
* 在本地安装[OpenCensus Azure 监视器导出](https://pypi.org/project/opencensus-ext-azure/)包：
  ```python
  pip install opencensus-ext-azure
  ```
* 创建[应用程序见解实例](../azure-monitor/app/opencensus-python.md)（此文档还包含有关获取资源的连接字符串的信息）

## <a name="getting-started"></a>入门

本节是有关使用 Azure 机器学习管道的 OpenCensus 的介绍。 有关详细教程，请参阅[打开 Census Azure 监视器导出器](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

向 Azure ML 管道添加 PythonScript 步骤。 使用对开放"ext-azure"的依赖项配置[Run 配置](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py)。 配置`APPLICATIONINSIGHTS_CONNECTION_STRING`环境变量。

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

创建名为 `sample_step.py` 的文件。 导入 AzureLogHandler 类以将日志路由到应用程序见解。 您还需要导入 Python 日志记录库。

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

接下来，将 AzureLogHandler 添加到 python 记录器。

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>使用自定义维度进行日志记录
 
默认情况下，转发到应用程序见解的日志将没有足够的上下文追溯到运行或实验。 为了使日志可操作以诊断问题，需要额外的字段。 

要添加这些字段，可以添加自定义维度以向日志消息提供上下文。 例如，当有人想要在同一管道运行中查看跨多个步骤的日志时。

自定义维度构成键值（存储为字符串、字符串）对的字典。 然后，字典将发送到应用程序见解，并作为列显示在查询结果中。 其单个维度可用作[查询参数](#additional-helpful-queries)。

### <a name="helpful-context-to-include"></a>要包括的有用上下文

| 字段                          | 推理/示例                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | 可以查询具有相同parent_run_id的日志，以便随着时间的推移查看所有步骤的日志，而不必深入到每个步骤中                                        |
| step_id                        | 可以查询具有相同step_id的日志，以查看问题发生的地方，范围缩小到单个步骤                                                        |
| step_name                      | 可以查询日志以查看随时间而执行的步骤性能。 还有助于查找最近运行step_id，而无需潜入门户 UI                                          |
| experiment_name                | 可以跨日志查询以查看实验性能。 还有助于查找最近运行parent_run_id或step_id，而无需深入了解门户 UI                   |
| run_url                 | 可以提供直接回运行以进行调查的链接。 |

**其他有用的字段**

这些字段可能需要其他代码检测，并且不由运行上下文提供。

| 字段                   | 推理/示例                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | 如果使用 CI/CD 进行部署，此字段可以将日志与提供步骤和管道逻辑的代码版本相关联。 此链接可进一步帮助诊断问题，或识别具有特定特征的模型（日志/指标值） |
| run_type                       | 可以区分不同的模型类型，或训练与评分运行 |

### <a name="creating-a-custom-dimensions-dictionary"></a>创建自定义维度字典

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", custom_dimensions)
```

## <a name="opencensus-python-logging-considerations"></a>打开Census Python 日志记录注意事项

OpenCensus AzureLogHandler 用于将 Python 日志路由到应用程序见解。 因此，应考虑 Python 日志记录细微差别。 创建记录器时，它具有默认日志级别，并将显示大于或等于该级别的日志。 使用 Python 日志记录功能的一个很好的参考是[日志记录说明书](https://docs.python.org/3/howto/logging-cookbook.html)。

OpenCensus 库需要`APPLICATIONINSIGHTS_CONNECTION_STRING`环境变量。 我们建议设置此环境变量，而不是将其作为管道参数传入，以避免绕过纯文本连接字符串。

## <a name="querying-logs-in-application-insights"></a>在应用程序见解中查询日志

路由到应用程序见解的日志将在"跟踪"或"异常"下显示。 请务必调整时间窗口以包括管道运行。

![应用程序见解查询结果](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

应用程序见解中的结果将显示日志消息和级别、文件路径和代码行号。 它还将显示包含的任何自定义维度。 在此图像中，自定义 Dimensions 字典显示上一[个代码示例](#creating-a-custom-dimensions-dictionary)中的键/值对。

### <a name="additional-helpful-queries"></a>其他有用的查询

以下一些查询使用"自定义维度.级别"。 这些严重性级别对应于 Python 日志最初发送的级别。 有关其他查询信息，请参阅[Azure 监视器日志查询](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)。

| 用例                                                               | 查询                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| 记录特定自定义维度的结果，例如"parent_run_id" | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| 记录过去 7 天内所有培训运行的结果                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| 记录过去 7 天的严重性级别错误的结果              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| 过去 7 天具有严重性级别的错误的日志结果计数     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>后续步骤

在应用程序见解实例中拥有日志后，可以使用这些日志根据查询结果设置[Azure 监视器警报](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on)。

您还可以将查询的结果添加到[Azure 仪表板](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards#add-logs-analytics-query)以获取其他见解。
