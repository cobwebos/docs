---
title: 使用 Azure Monitor（预览版）监视 Python 应用程序 | Microsoft Docs
description: 提供有关使用 Azure Monitor 连接 OpenCensus Python 的说明
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6b8343d08962d8ce749e1160b0226b68571571f8
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2020
ms.locfileid: "83815717"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>为 Python 应用程序设置 Azure Monitor

通过与 [OpenCensus](https://opencensus.io) 集成，Azure Monitor 支持对 Python 应用程序进行分布式跟踪、指标收集和日志记录。 本文将指导你完成设置 OpenCensus for Python 并将监视数据发送到 Azure Monitor 的过程。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- Python 安装。 本文使用 [Python 3.7.0](https://www.python.org/downloads/)，不过更早的版本在进行微调后也可能适用。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>在 Azure Monitor 中创建 Application Insights 资源

首先需在 Azure Monitor 中创建一个 Application Insights 资源，该资源将生成一个检测密钥 (ikey)。 然后，使用 ikey 配置 OpenCensus SDK，以将遥测数据发送到 Azure Monitor。

1. 选择“创建资源” > “开发人员工具” > “Application Insights”。

   ![添加 Application Insights 资源](./media/opencensus-python/0001-create-resource.png)

1. 系统会显示一个配置框。 使用下表填写输入字段。

   | 设置        | 值           | 说明  |
   | ------------- |:-------------|:-----|
   | **名称**      | 全局唯一值 | 标识所监视的应用的名称 |
   | **资源组**     | myResourceGroup      | 用于托管 Application Insights 数据的新资源组的名称 |
   | **位置** | 美国东部 | 离你近的位置或离托管应用的位置近的位置 |

1. 选择“创建”。

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>检测适用于 Azure Monitor 的 OpenCensus Python SDK

安装 OpenCensus Azure Monitor 导出程序：

```console
python -m pip install opencensus-ext-azure
```

有关包和集成的完整列表，请参阅 [OpenCensus 包](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus)。

> [!NOTE]
> `python -m pip install opencensus-ext-azure` 命令假定你已为 Python 安装设置了 `PATH` 环境变量。 如果尚未配置此变量，则需要提供 Python 可执行文件所在位置的完整目录路径。 结果为如下所示的命令：`C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`。

SDK 使用三个 Azure Monitor 导出程序将不同类型的遥测发送到 Azure Monitor：跟踪、指标和日志。 有关这些遥测类型的详细信息，请参阅[数据平台概述](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)。 按照以下说明通过三个导出程序发送这些遥测类型。

## <a name="telemetry-type-mappings"></a>遥测类型映射

以下为 OpenCensus 提供的导出程序，它们映射到你将在 Azure Monitor 中看到的遥测类型。

![从 OpenCensus 到 Azure Monitor 的遥测类型映射的屏幕截图](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>跟踪

> [!NOTE]
> OpenCensus 中的 `Trace` 指[分布式跟踪](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)。 `AzureExporter` 将 `requests` 和 `dependency` 遥测发送到 Azure Monitor。

1. 首先，让我们在本地生成一些跟踪数据。 在 Python IDLE 或所选编辑器中，输入以下代码。

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

2. 运行代码时，系统会重复提示你输入一个值。 每次输入时，值都会输出到 shell，且 OpenCensus Python 模块将生成相应的 `SpanData` 块。 OpenCensus 项目[将跟踪定义为 span 树](https://opencensus.io/core-concepts/tracing/)。
    
    ```
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

3. 尽管输入值有助于演示，但最终我们希望向 Azure Monitor 发出 `SpanData`。 将连接字符串直接传入导出程序，也可以在 `APPLICATIONINSIGHTS_CONNECTION_STRING` 环境变量中指定它。 根据以下代码示例，修改上一步中的代码：

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

4. 现在，当你运行 Python 脚本时，系统仍会提示你输入值，但只有该值输出到 shell 中。 创建的 `SpanData` 将发送到 Azure Monitor。 可在 `dependencies` 下找到发出的 span 数据。 有关传出请求的更多详细信息，请参阅 OpenCensus Python [依赖项](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency)。
有关传入请求的更多详细信息，请参阅 OpenCensus Python [请求](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request)。

#### <a name="sampling"></a>采样

有关在 OpenCensus 中采样的信息，请查看 [OpenCensus 中的采样](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)。

#### <a name="trace-correlation"></a>跟踪关联

有关跟踪数据中的遥测关联的详细信息，请查看 OpenCensus Python [遥测关联](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)。

#### <a name="modify-telemetry"></a>修改遥测

有关在将跟踪遥测发送到 Azure Monitor 之前如何对其进行修改的详细信息，请参阅 OpenCensus Python [遥测处理器](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)。

### <a name="metrics"></a>指标

1. 首先，让我们生成一些本地指标数据。 我们将创建一个简单的指标用于跟踪用户按 Enter 的次数。

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
2. 运行代码时，系统会重复提示你按 Enter。 创建一个指标以跟踪按下 Enter 的次数。 对于每次输入，该值将递增，并且指标信息将在控制台中显示。 该信息包括指标更新时的当前值和当前时间戳。

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. 尽管输入值有助于演示，但最终我们希望向 Azure Monitor 发出指标数据。 将连接字符串直接传入导出程序，也可以在 `APPLICATIONINSIGHTS_CONNECTION_STRING` 环境变量中指定它。 根据以下代码示例，修改上一步中的代码：

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

4. 导出程序将以固定的时间间隔将指标数据发送到 Azure Monitor。 默认值为每 15 秒。 我们正在跟踪一个指标，因此，此指标数据及其包含的所有值和时间戳将在每个时间间隔发送。 可在 `customMetrics` 下找到数据。

#### <a name="standard-metrics"></a>标准指标

默认情况下，指标导出程序将向 Azure Monitor 发送一组标准指标。 可通过在指标导出程序的构造函数中将 `enable_standard_metrics` 标志设置为 `False` 来禁用此设置。

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```
以下是当前发送的标准指标的列表：

- 可用内存（字节）
- CPU 处理器时间（百分比）
- 传入请求速率（每秒）
- 传入请求平均执行时间（毫秒）
- 传出请求速率（每秒）
- 进程 CPU 使用率（百分比）
- 进程专用字节数（字节）

你应该能够在 `performanceCounters` 中看到这些指标。 传入请求速率将低于 `customMetrics`。 有关详细信息，请参阅[性能计时器](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters)。

#### <a name="modify-telemetry"></a>修改遥测

有关在将跟踪遥测发送到 Azure Monitor 之前如何对其进行修改的详细信息，请参阅 OpenCensus Python [遥测处理器](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)。

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

2.  代码将持续要求输入一个值。 对于输入的每个值，将发出一个日志条目。

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. 尽管输入值有助于演示，但最终我们希望向 Azure Monitor 发出日志数据。 将连接字符串直接传入导出程序，也可以在 `APPLICATIONINSIGHTS_CONNECTION_STRING` 环境变量中指定它。 根据以下代码示例，修改上一步中的代码：

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

4. 导出程序会将日志数据发送到 Azure Monitor。 可在 `traces` 下找到数据。 

    > [!NOTE]
    > 此上下文中的 `traces` 与 `Tracing` 不同。 `traces` 指在利用 `AzureLogHandler` 时将在 Azure Monitor 中看到的遥测的类型。 `Tracing` 是 OpenCensus 中的一个概念，与[分布式跟踪](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)相关。

5. 若要设置日志消息的格式，可使用内置 Python [日志记录 API](https://docs.python.org/3/library/logging.html#formatter-objects) 中的 `formatters`。

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    format_str = '%(asctime)s - %(levelname)-8s - %(message)s'
    date_format = '%Y-%m-%d %H:%M:%S'
    formatter = logging.Formatter(format_str, date_format)
    # TODO: replace the all-zero GUID with your instrumentation key.
    handler = AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

6. 还可使用 custom_dimensions 字段在 extra 关键字参数中将自定义属性添加到日志消息中。 这些属性将作为键值对在 Azure Monitor 的 `customDimensions` 中显示。
    > [!NOTE]
    > 若要使此功能正常运行，需要将字典传递给 custom_dimensions 字段。 如果传递任何其他类型的参数，则记录器将忽略它们。

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

#### <a name="sending-exceptions"></a>发送异常

OpenCensus Python 不会自动跟踪和发送 `exception` 遥测。 借助 Python 日志记录库使用异常，可通过 `AzureLogHandler` 发送它们。 可以像使用普通日志记录时一样添加自定义属性。

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
由于必须显式记录异常，这取决于用户想要如何记录未处理的异常。 只要用户显式记录异常遥测，OpenCensus 就不会对用户想要如何执行此操作施加限制。

#### <a name="sampling"></a>采样

有关在 OpenCensus 中采样的信息，请查看 [OpenCensus 中的采样](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications)。

#### <a name="log-correlation"></a>日志关联

有关如何使用跟踪上下文数据扩充日志的详细信息，请参阅 OpenCensus Python [日志集成](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation)。

#### <a name="modify-telemetry"></a>修改遥测

有关在将跟踪遥测发送到 Azure Monitor 之前如何对其进行修改的详细信息，请参阅 OpenCensus Python [遥测处理器](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)。

## <a name="view-your-data-with-queries"></a>使用查询查看数据

可以通过“日志(分析)”选项卡查看从应用程序发送的遥测数据。

![红色框中选定“日志(分析)”的概述窗格的屏幕截图](./media/opencensus-python/0010-logs-query.png)

在“活动”下的列表中：

- 对于使用 Azure Monitor 跟踪导出程序发送的遥测，传入请求在 `requests` 下显示。 传出或进程内请求在 `dependencies` 下显示。
- 对于使用 Azure Monitor 指标导出程序发送的遥测，发送的指标在 `customMetrics` 下显示。
- 对于使用 Azure Monitor 日志导出程序发送的遥测，日志在 `traces` 下显示。 异常在 `exceptions` 下显示。

有关如何使用查询和日志的更多详细信息，请参阅 [Azure Monitor 中的日志](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)。

## <a name="learn-more-about-opencensus-for-python"></a>了解有关 OpenCensus for Python 的详细信息

* [GitHub 上的 OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [自定义](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [GitHub 上的 Azure Monitor 导出程序](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus 集成](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Azure Monitor 示例应用程序](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>后续步骤

* [跟踪传入请求](./../../azure-monitor/app/opencensus-python-dependency.md)
* [跟踪传出请求](./../../azure-monitor/app/opencensus-python-request.md)
* [应用程序映射](./../../azure-monitor/app/app-map.md)
* [端到端性能监视](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>警报

* [可用性测试](../../azure-monitor/app/monitor-web-app-availability.md)：创建测试来确保站点在 Web 上可见。
* [智能诊断](../../azure-monitor/app/proactive-diagnostics.md)：这些测试可自动运行，因此不需要进行任何设置。 它们会告诉你应用是否具有异常的失败请求速率。
* [指标警报](../../azure-monitor/platform/alerts-log.md)：设置警报以在某个指标超过阈值时发出警告。 可以在编码到应用中的自定义指标中设置它们。
