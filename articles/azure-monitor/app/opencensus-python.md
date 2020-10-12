---
title: 监视 Azure Monitor 的 Python 应用程序 |Microsoft Docs
description: 提供有关使用 Azure Monitor 连接 OpenCensus Python 的说明
ms.topic: conceptual
ms.date: 09/24/2020
ms.reviewer: mbullwin
ms.custom: devx-track-python
ms.openlocfilehash: 1e6376cd8389a4f1f0defebce0a2c7b6d0f9deed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323259"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>为 Python 应用程序设置 Azure Monitor

通过与 [OpenCensus](https://opencensus.io) 集成，Azure Monitor 支持对 Python 应用程序进行分布式跟踪、指标收集和日志记录。 本文将指导你完成为 Python 设置 OpenCensus 并将监视数据发送到 Azure Monitor 的过程。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- Python 安装。 本文使用 [Python 3.7.0](https://www.python.org/downloads/release/python-370/)，但其他版本可能会使用细微的更改。 SDK 仅支持 Python 版本2.7 和 v2.0 3.7。
- 创建 Application Insights [资源](./create-new-resource.md)。 你将为你的资源分配自己的检测密钥 (ikey) 。

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>检测适用于 Azure Monitor 的 OpenCensus Python SDK

安装 OpenCensus Azure Monitor 导出程序：

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> `python -m pip install opencensus-ext-azure` 命令假定你已为 Python 安装设置了 `PATH` 环境变量。 如果尚未配置此变量，则需要提供 Python 可执行文件所在位置的完整目录路径。 结果为如下所示的命令：`C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`。

SDK 使用三个 Azure Monitor 导出程序将不同类型的遥测发送到 Azure Monitor。 它们是跟踪、指标和日志。 有关这些遥测类型的详细信息，请参阅[数据平台概述](../platform/data-platform.md)。 按照以下说明通过三个导出程序发送这些遥测类型。

## <a name="telemetry-type-mappings"></a>遥测类型映射

OpenCensus 提供的导出程序映射到 Azure Monitor 中看到的遥测类型。

| 可观察性的支柱 | Azure Monitor 中的遥测类型    | 说明                                         |
|-------------------------|------------------------------------|-----------------------------------------------------|
| 日志                    | 跟踪，异常，customEvents   | 日志遥测，异常遥测，事件遥测 |
| 指标                 | customMetrics、performanceCounters | 自定义指标性能计数器                |
| 跟踪                 | 请求依赖项             | 传入请求，传出请求                |

### <a name="logs"></a>日志

1. 首先，让我们生成一些本地日志数据。

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. 代码持续要求输入值。 对于输入的每个值，将发出一个日志条目。

    ```output
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

1. 尽管输入值有助于演示，但最终我们希望向 Azure Monitor 发出日志数据。 将连接字符串直接传递到导出程序。 或者，可以在环境变量中指定它 `APPLICATIONINSIGHTS_CONNECTION_STRING` 。 根据以下代码示例，修改上一步中的代码：

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)

    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. 导出程序会将日志数据发送到 Azure Monitor。 可在 `traces` 下找到数据。 

    > [!NOTE]
    > 在此上下文中， `traces` 与不同 `tracing` 。 此处， `traces` 是指在使用时将在 Azure Monitor 中看到的遥测类型 `AzureLogHandler` 。 但 `tracing` 涉及到 OpenCensus 中的概念，并与 [分布式跟踪](./distributed-tracing.md)相关。

    > [!NOTE]
    > 根记录器配置了警告级别。 这意味着，将忽略发送的严重性较低的任何日志，而不会将其发送到 Azure Monitor。 有关详细信息，请参阅这篇[文档](https://docs.python.org/3/library/logging.html#logging.Logger.setLevel)。

1. 还可以通过使用 "custom_dimensions" 字段，将自定义属性添加到 *额外* 关键字参数中的日志消息。 这些属性在 Azure Monitor 中显示为中的键值对 `customDimensions` 。
    > [!NOTE]
    > 若要使此功能正常运行，需要将字典传递给 custom_dimensions 字段。 如果传递任何其他类型的参数，记录器会将其忽略。

    ```python
    import logging

    from opencensus.ext.azure.log_exporter import AzureLogHandler

    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="configure-logging-for-django-applications"></a>为 Django 应用程序配置日志记录

可以按照上文所述在应用程序代码中为 Django 应用程序显式配置日志记录，也可以在 Django 的日志记录配置中指定日志记录。 此代码可以包含在用于 Django 设置配置的任何文件中。 有关如何配置 Django 设置的详细说明，请参阅 [Django 设置](https://docs.djangoproject.com/en/3.0/topics/settings/)。 有关配置日志记录的详细信息，请参阅 [Django 日志记录](https://docs.djangoproject.com/en/3.0/topics/logging/)。

```json
LOGGING = {
    "handlers": {
        "azure": {
            "level": "DEBUG",
        "class": "opencensus.ext.azure.log_exporter.AzureLogHandler",
            "instrumentation_key": "<your-ikey-here>",
         },
        "console": {
            "level": "DEBUG",
            "class": "logging.StreamHandler",
            "stream": sys.stdout,
         },
      },
    "loggers": {
        "logger_name": {"handlers": ["azure", "console"]},
    },
}
```

请确保使用的记录器的名称与配置中指定的相同。

```python
import logging

logger = logging.getLogger("logger_name")
logger.warning("this will be tracked")
```

#### <a name="send-exceptions"></a>发送异常

OpenCensus Python 不会自动跟踪和发送 `exception` 遥测数据。 通过 `AzureLogHandler` Python 日志记录库通过使用异常来发送。 可以像使用普通日志记录时一样添加自定义属性。

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
# TODO: replace the all-zero GUID with your instrumentation key.
logger.addHandler(AzureLogHandler(
    connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
)

properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

# Use properties in exception logs
try:
    result = 1 / 0  # generate a ZeroDivisionError
except Exception:
    logger.exception('Captured an exception.', extra=properties)
```
因为你必须显式记录异常，所以用户需要如何记录未经处理的异常。 OpenCensus 不会对用户希望执行此操作的方式施加限制，只要它们显式记录异常遥测。

#### <a name="send-events"></a>发送事件

可以采用与 `customEvent` 发送遥测数据完全相同的方式发送遥测数据， `trace` `AzureEventHandler` 而不是使用。

```python
import logging

from opencensus.ext.azure.log_exporter import AzureEventHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureEventHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.setLevel(logging.INFO)
logger.info('Hello, World!')
```

#### <a name="sampling"></a>采样

有关在 OpenCensus 中采样的信息，请查看 [OpenCensus 中的采样](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)。

#### <a name="log-correlation"></a>日志关联

有关如何使用跟踪上下文数据扩充日志的详细信息，请参阅 OpenCensus Python [日志集成](./correlation.md#log-correlation)。

#### <a name="modify-telemetry"></a>修改遥测

有关如何在将跟踪的遥测发送到 Azure Monitor 之前修改它们的详细信息，请参阅 OpenCensus Python [遥测处理器](./api-filtering-sampling.md#opencensus-python-telemetry-processors)。


### <a name="metrics"></a>指标

1. 首先，让我们生成一些本地指标数据。 我们将创建一个简单的度量值，用于跟踪用户选择 **Enter** 键的次数。

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
1. 重复运行代码会提示你选择 **Enter**。 将创建一个度量值，以跟踪选择 **输入** 的次数。 对于每个条目，该值会递增，指标信息将显示在控制台中。 该信息包括指标更新时的当前值和当前时间戳。

    ```output
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

1. 尽管输入值有助于演示，但最终我们希望向 Azure Monitor 发出指标数据。 将连接字符串直接传递到导出程序。 或者，可以在环境变量中指定它 `APPLICATIONINSIGHTS_CONNECTION_STRING` 。 根据以下代码示例，修改上一步中的代码：

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder

    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

1. 导出程序会按固定的时间间隔将指标数据发送到 Azure Monitor。 默认值为每 15 秒。 我们正在跟踪单个度量值，因此，每个间隔将发送此指标数据，其中包含其包含的任何值和时间戳。 可在 `customMetrics` 下找到数据。

#### <a name="performance-counters"></a>性能计数器

默认情况下，度量值导出程序会将一组性能计数器发送到 Azure Monitor。 可以通过在指标导出程序的构造函数中将 `enable_standard_metrics` 标志设为 `False` 来禁用此功能。

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```

当前已发送这些性能计数器：

- 可用内存（字节）
- CPU 处理器时间（百分比）
- 传入请求速率（每秒）
- 传入请求平均执行时间（毫秒）
- 进程 CPU 使用率（百分比）
- 进程专用字节数（字节）

你应该能够在 `performanceCounters` 中看到这些指标。 有关详细信息，请参阅[性能计时器](./performance-counters.md)。

#### <a name="modify-telemetry"></a>修改遥测

有关如何在将跟踪的遥测发送到 Azure Monitor 之前修改它们的信息，请参阅 OpenCensus Python [遥测处理器](./api-filtering-sampling.md#opencensus-python-telemetry-processors)。

### <a name="tracing"></a>跟踪

> [!NOTE]
> 在 OpenCensus 中， `tracing` 是指 [分布式跟踪](./distributed-tracing.md)。 `AzureExporter` 将 `requests` 和 `dependency` 遥测发送到 Azure Monitor。

1. 首先，让我们在本地生成一些跟踪数据。 在 Python IDLE 或所选编辑器中，输入以下代码：

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. 重复运行代码会提示输入值。 对于每个条目，将值输出到 shell。 OpenCensus Python 模块将生成相应的部分 `SpanData` 。 OpenCensus 项目[将跟踪定义为 span 树](https://opencensus.io/core-concepts/tracing/)。
    
    ```output
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

1. 尽管输入值有助于演示，但最终我们想要 `SpanData` Azure Monitor。 将连接字符串直接传递到导出程序。 或者，可以在环境变量中指定它 `APPLICATIONINSIGHTS_CONNECTION_STRING` 。 根据以下代码示例，修改上一步中的代码：

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

1. 现在，当你运行 Python 脚本时，系统仍会提示你输入值，但只有该值输出到 shell 中。 创建的 `SpanData` 将发送到 Azure Monitor。 可在 `dependencies` 下找到发出的 span 数据。 有关传出请求的详细信息，请参阅 OpenCensus Python [依赖项](./opencensus-python-dependency.md)。
有关传入请求的详细信息，请参阅 OpenCensus Python [请求](./opencensus-python-request.md)。

#### <a name="sampling"></a>采样

有关在 OpenCensus 中采样的信息，请查看 [OpenCensus 中的采样](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)。

#### <a name="trace-correlation"></a>跟踪关联

有关跟踪数据中遥测关联的详细信息，请参阅 OpenCensus Python [遥测关联](./correlation.md#telemetry-correlation-in-opencensus-python)。

#### <a name="modify-telemetry"></a>修改遥测

有关如何在将跟踪的遥测发送到 Azure Monitor 之前修改它们的详细信息，请参阅 OpenCensus Python [遥测处理器](./api-filtering-sampling.md#opencensus-python-telemetry-processors)。

## <a name="configure-azure-monitor-exporters"></a>配置 Azure Monitor 导出程序

如图所示，有三种不同的 Azure Monitor 导出程序支持 OpenCensus。 每种类型都将不同类型的遥测发送到 Azure Monitor。 若要查看每个导出程序发送的遥测类型，请参阅下表。

每个导出程序都接受通过构造函数传递的相同配置参数。 可在此处查看每个相关的详细信息：

- `connection_string`：用于连接到 Azure Monitor 资源的连接字符串。 其优先级高于 `instrumentation_key`。
- `enable_standard_metrics`：用于 `AzureMetricsExporter` 。 指示导出程序将[性能计数器](../platform/app-insights-metrics.md#performance-counters)指标自动发送到 Azure Monitor。 默认为 `True`。
- `export_interval`：用于指定导出频率（以秒为单位）。
- `instrumentation_key`：用于连接到 Azure Monitor 资源的检测密钥。
- `logging_sampling_rate`：用于 `AzureLogHandler` 。 为导出日志提供采样率 [0,1.0]。 默认值为 1.0。
- `max_batch_size`：指定一次导出的最大遥测大小。
- `proxies`：指定用于将数据发送到 Azure Monitor 的代理的序列。 有关详细信息，请参阅 [代理](https://requests.readthedocs.io/en/master/user/advanced/#proxies)。
- `storage_path`：本地存储文件夹所在位置的路径 (未发送的遥测) 。 `opencensus-ext-azure`从 v 1.0.3，默认路径是 OS temp 目录 + `opencensus-python`  +  `your-ikey` 。 在 v 1.0.3 之前，默认路径为 $USER + `.opencensus`  +  `.azure`  +  `python-file-name` 。

## <a name="view-your-data-with-queries"></a>使用查询查看数据

可以通过“日志(分析)”选项卡查看从应用程序发送的遥测数据。

![红色框中选定“日志(分析)”的概述窗格的屏幕截图](./media/opencensus-python/0010-logs-query.png)

在“活动”下的列表中：

- 对于使用 Azure Monitor 跟踪导出程序发送的遥测，传入请求在 `requests` 下显示。 传出或进程内请求在 `dependencies` 下显示。
- 对于使用 Azure Monitor 指标导出程序发送的遥测，发送的指标在 `customMetrics` 下显示。
- 对于使用 Azure Monitor 日志导出程序发送的遥测，日志在 `traces` 下显示。 异常在 `exceptions` 下显示。

有关如何使用查询和日志的更多详细信息，请参阅 [Azure Monitor 中的日志](../platform/data-platform-logs.md)。

## <a name="learn-more-about-opencensus-for-python"></a>了解有关 OpenCensus for Python 的详细信息

* [GitHub 上的 OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [自定义](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [GitHub 上的 Azure Monitor 导出程序](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus 集成](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Monitor 示例应用程序](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>后续步骤

* [跟踪传入请求](./opencensus-python-dependency.md)
* [跟踪传出请求](./opencensus-python-request.md)
* [应用程序映射](./app-map.md)
* [端到端性能监视](../learn/tutorial-performance.md)

### <a name="alerts"></a>警报

* [可用性测试](./monitor-web-app-availability.md)：创建测试来确保站点在 Web 上可见。
* [智能诊断](./proactive-diagnostics.md)：这些测试可自动运行，因此不需要进行任何设置。 它们会告诉你应用是否具有异常的失败请求速率。
* [指标警报](../platform/alerts-log.md)：设置警报以在某个指标超过阈值时发出警告。 可以在编码到应用中的自定义指标中设置它们。

