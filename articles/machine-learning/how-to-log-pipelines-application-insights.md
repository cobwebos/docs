---
title: 监视和收集管道日志文件
titleSuffix: Azure Machine Learning
description: 将日志记录添加到训练和批量评分管道，并在 Application Insights 中查看记录的结果。
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.date: 08/11/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 808f71ae138922b93e3742f7ff2f526e419cf58c
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89645881"
---
# <a name="collect-machine-learning-pipeline-log-files-in-application-insights-for-alerts-and-debugging"></a>在 Application Insights 中收集用于警报和调试的机器学习管道日志文件
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

可以通过脚本使用 [OpenCensus](https://opencensus.io/quickstart/python/) Python 库将日志路由到 Application Insights。 在一个位置聚合管道运行发出的日志可以生成查询和诊断问题。 使用 Application Insights 可以跟踪一段时间内的日志，并比较各个运行的管道日志。

将日志放在一个位置可以提供异常和错误消息的历史记录。 由于 Application Insights 与 Azure 警报相集成，因此你还可以基于 Application Insights 查询创建警报。

## <a name="prerequisites"></a>必备条件

* 遵循相应的步骤创建 [Azure 机器学习](./how-to-manage-workspace.md)工作区并[创建第一个管道](./how-to-create-your-first-pipeline.md)
* [配置开发环境](./how-to-configure-environment.md)以安装 Azure 机器学习 SDK。
* 在本地安装 [OpenCensus Azure Monitor 导出程序](https://pypi.org/project/opencensus-ext-azure/)包：
  ```python
  pip install opencensus-ext-azure
  ```
* 创建一个 [Application Insights 实例](../azure-monitor/app/opencensus-python.md)（本文档还提供了有关获取资源连接字符串的信息）

## <a name="getting-started"></a>入门

本部分的简介内容与通过 Azure 机器学习管道使用 OpenCensus 的操作相关。 有关详细教程，请参阅 [OpenCensus Azure Monitor 导出程序](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

将 PythonScriptStep 添加到 Azure 机器学习管道。 使用 opencensus-ext-azure 中的依赖项配置 [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py&preserve-view=true)。 配置 `APPLICATIONINSIGHTS_CONNECTION_STRING` 环境变量。

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

接下来，将 AzureLogHandler 添加到 Python 记录器。

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>使用自定义维度进行日志记录
 
默认情况下，转发到 Application Insights 的日志不提供足够的上下文用于追溯运行或试验。 要使日志可用于诊断问题，需要添加额外的字段。 

若要添加这些字段，可以添加自定义维度，以提供日志消息的上下文。 例如，当某人想要查看同一管道运行中多个步骤的日志时。

自定义维度构成了键-值（以“字符串, 字符串”形式存储）对的字典。 然后，该字典将发送到 Application Insights，并在查询结果中显示为一列。 其各个维度可用作[查询参数](#additional-helpful-queries)。

### <a name="helpful-context-to-include"></a>要包含的有用上下文

| 字段                          | 理由/示例                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | 可以查询具有相同 parent_run_id 的运行的日志，以查看一段时间内所有步骤的日志，而无需深入到各个步骤                                        |
| step_id                        | 可以查询具有相同 step_id 的运行的日志，将范围缩小到单个步骤，以查看发生问题的位置                                                        |
| step_name                      | 可以查询日志以查看一段时间内的步骤性能。 还有助于查找最近的运行的 step_id，而无需深入到门户 UI                                          |
| experiment_name                | 可以跨日志进行查询，以查看一段时间内的试验性能。 还有助于查找最近的运行的 parent_run_id 或 step_id，而无需深入到门户 UI                   |
| run_url                 | 可以提供直接返回到运行的链接用于调查。 |

**其他有用字段**

这些字段可能需要额外的代码检测，且不会由运行上下文提供。

| 字段                   | 理由/示例                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | 如果使用 CI/CD 进行部署，此字段可将日志关联到提供步骤和管道逻辑的代码版本。 此链接可以进一步帮助诊断问题，或识别具有特定特征（日志/指标值）的模型 |
| run_type                       | 可以区分不同的模型类型，或者区分训练运行与评分运行 |

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

OpenCensus AzureLogHandler 用于将 Python 日志路由到 Application Insights。 因此，应考虑到 Python 日志记录的细微差别。 创建记录器后，它将采用默认日志级别，并显示大于或等于该级别的日志。 [日志记录 Cookbook](https://docs.python.org/3/howto/logging-cookbook.html) 提供了有关使用 Python 日志记录功能的有用参考。

OpenCensus 库需要 `APPLICATIONINSIGHTS_CONNECTION_STRING` 环境变量。 建议设置此环境变量，而不要将其作为管道参数传入，以避免传递纯文本连接字符串。

## <a name="querying-logs-in-application-insights"></a>在 Application Insights 中查询日志

路由到 Application Insights 的日志将显示在“跟踪”或“异常”下。 请务必调整时间范围以包括你的管道运行。

![Application Insights 查询结果](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Application Insights 中的结果将显示日志消息和级别、文件路径及代码行号。 它还显示包含的任何自定义维度。 在此图中，customDimensions 字典显示了前一[代码示例](#creating-a-custom-dimensions-dictionary)中的键/值对。

### <a name="additional-helpful-queries"></a>其他有用查询

以下某些查询使用“customDimensions.Level”。 这些严重性级别对应于最初发送 Python 日志时所用的级别。 有关其他查询信息，请参阅 [Azure Monitor 日志查询](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)。

| 用例                                                               | 查询                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| 特定自定义维度（例如“parent_run_id”）的日志结果 | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| 过去 7 天内所有训练运行的日志结果                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| 过去 7 天内出现 severityLevel 错误的日志结果              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| 过去 7 天内出现 severityLevel 错误的日志结果的计数     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>后续步骤

在 Application Insights 实例中启用日志后，可以使用这些日志基于查询结果设置 [Azure Monitor 警报](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on)。

还可以将查询结果添加到 [Azure 仪表板](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards#add-logs-analytics-query)以获取更多见解。
