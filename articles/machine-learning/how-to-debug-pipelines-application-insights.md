---
title: Application Insights 中的机器学习管道的调试和故障排除
titleSuffix: Azure Machine Learning
description: 将日志记录添加到定型和批处理计分管道，并 Application Insights 中查看记录的结果。
services: machine-learning
author: aburek
ms.author: anrode
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: 85dcd9ef98deb2ea0117f2db280e49c4a57bf00f
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776295"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines-in-application-insights"></a>Application Insights 中的机器学习管道的调试和故障排除
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

[OpenCensus](https://opencensus.io/quickstart/python/) python 库可用于将日志路由到脚本中的 Application Insights。 通过在一个位置聚合日志，可以生成查询和诊断问题。 使用 Application Insights 将允许你跟踪一段时间内的日志，并跨运行比较管道日志。

将你的日志放入一次后，将提供异常和错误消息的历史记录。 由于 Application Insights 与 Azure 警报集成，因此还可以基于 Application Insights 的查询创建警报。

## <a name="prerequisites"></a>必备组件

* 按照以下步骤创建[Azure 机器学习](./how-to-manage-workspace.md)的工作区并[创建第一个管道](./how-to-create-your-first-pipeline.md)
* [配置开发环境](./how-to-configure-environment.md)以安装 Azure 机器学习 SDK。
* 在本地安装[OpenCensus Azure Monitor 导出](https://pypi.org/project/opencensus-ext-azure/)程序包：
  ```python
  pip install opencensus-ext-azure
  ```
* 创建[Application Insights 实例](../azure-monitor/app/opencensus-python.md)（本文档还包含有关获取资源的连接字符串的信息）

## <a name="getting-started"></a>入门

本部分介绍了从 Azure 机器学习管道使用 OpenCensus 的相关介绍。 有关详细教程，请参阅[OpenCensus Azure Monitor 导出程序](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

将 PythonScriptStep 添加到 Azure ML 管道。 配置[RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) ，使其依赖于 opencensus-azure。 配置 `APPLICATIONINSIGHTS_CONNECTION_STRING` 环境变量。

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

创建名为 `sample_step.py` 的文件。 导入 AzureLogHandler 类以将日志路由到 Application Insights。 还需要导入 Python 日志记录库。

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

## <a name="logging-with-custom-dimensions"></a>自定义维度日志记录
 
默认情况下，转发给 Application Insights 的日志没有足够的上下文，无法追溯到运行或实验。 为了使日志可用于诊断问题，需要额外的字段。 

若要添加这些字段，可添加自定义维度以便向日志消息提供上下文。 例如，当有人想要跨相同管道运行的多个步骤查看日志时。

自定义维度组成键-值（存储为字符串，字符串）对的字典。 然后，将字典发送到 Application Insights，并在查询结果中显示为一列。 其各个维度可用作[查询参数](#additional-helpful-queries)。

### <a name="helpful-context-to-include"></a>要包括的有用上下文

| 字段                          | 推理/示例                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | 可以查询具有相同 parent_run_id 的日志，以查看一段时间内所有步骤的日志，而无需深入了解每个步骤                                        |
| step_id                        | 可以查询具有相同 step_id 的日志，以查看范围较窄的问题发生在单个步骤的位置                                                        |
| step_name                      | 可以查询日志以查看一段时间内的步骤性能。 还有助于查找最近运行的 step_id，而无需深入了解门户 UI                                          |
| experiment_name                | 可以跨日志查询，查看一段时间内的试验性能。 还有助于查找最近运行的 parent_run_id 或 step_id，而无需深入了解门户 UI                   |
| run_url                 | 可以直接提供返回到运行进行调查的链接。 |

**其他有用的字段**

这些字段可能需要额外的代码检测，并且不是由运行上下文提供的。

| 字段                   | 推理/示例                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | 如果使用 CI/CD 进行部署，则此字段可以将日志与提供步骤和管道逻辑的代码版本相关联。 此链接可以进一步帮助诊断问题，或识别具有特定特征的模型（日志/指标值） |
| run_type                       | 可以区分不同的模型类型，也可以区分定型运行和评分运行 |

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

## <a name="opencensus-python-logging-considerations"></a>OpenCensus Python 日志记录注意事项

OpenCensus AzureLogHandler 用于将 Python 日志路由到 Application Insights。 因此，应考虑 Python 日志记录的细微差别。 创建记录器后，它将具有默认日志级别，并显示大于或等于该级别的日志。 [日志记录指南](https://docs.python.org/3/howto/logging-cookbook.html)是使用 Python 日志记录功能的好参考。

OpenCensus 库需要 `APPLICATIONINSIGHTS_CONNECTION_STRING` 环境变量。 建议设置此环境变量，而不是将其作为管道参数传递，以避免在明文连接字符串中传递。

## <a name="querying-logs-in-application-insights"></a>查询 Application Insights 中的日志

路由到 Application Insights 的日志会显示在 "跟踪" 或 "异常" 下。 确保调整时间范围以包括管道运行。

![Application Insights 查询结果](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Application Insights 中的结果将显示日志消息和级别、文件路径和代码行号。 它还将显示包括的任何自定义维度。 在此图中，customDimensions 字典显示了上一个[代码示例](#creating-a-custom-dimensions-dictionary)中的键/值对。

### <a name="additional-helpful-queries"></a>其他有用的查询

下面的某些查询使用 "customDimensions"。 这些严重性级别与 Python 日志最初的发送级别相对应。 有关其他查询信息，请参阅[Azure Monitor 日志查询](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)。

| 用例                                                               | 查询                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| 特定自定义维度的日志结果，例如 "parent_run_id" | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| 过去7天内所有定型运行的日志结果                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| 过去7天内带有 severityLevel 错误的日志结果              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| 过去7天内带有 severityLevel 错误的日志结果计数     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>后续步骤

一旦您的 Application Insights 实例中有日志，它们就可以用于根据查询结果设置[Azure Monitor 警报](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on)。

还可以将查询结果添加到[Azure 仪表板](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards#add-logs-analytics-query)，以获得更多见解。