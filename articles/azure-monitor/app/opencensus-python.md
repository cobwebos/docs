---
title: 监视具有 Azure Monitor 的 Python 应用程序（预览） |Microsoft Docs
description: 提供有关将 OpenCensus Python 与 Azure Monitor 连接的说明
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 83b4e1e10af8e398f6671f6b2561485bfdb6b7f4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432487"
---
# <a name="set-up-azure-monitor-for-your-python-application-preview"></a>设置 Python 应用程序的 Azure Monitor （预览）

Azure Monitor 通过与[OpenCensus](https://opencensus.io)集成，支持 Python 应用程序的分布式跟踪、指标收集和日志记录。 本文将指导你完成为 Python 设置 OpenCensus 并将监视数据发送到 Azure Monitor 的过程。

## <a name="prerequisites"></a>必备组件

- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- Python 安装。 本文使用[Python 3.7.0](https://www.python.org/downloads/)，但较早的版本可能会使用细微的更改。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com/)。

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>在 Azure Monitor 中创建 Application Insights 资源

首先必须在 Azure Monitor 中创建 Application Insights 资源，这将生成一个检测密钥（ikey）。 然后，使用 ikey 将 OpenCensus SDK 配置为将遥测数据发送到 Azure Monitor。

1. 选择“创建资源” > “开发人员工具” > “Application Insights”。

   ![添加 Application Insights 资源](./media/opencensus-python/0001-create-resource.png)

1. 此时将显示一个配置框。 使用下表填写输入字段。

   | 设置        | 值           | Description  |
   | ------------- |:-------------|:-----|
   | **名称**      | 全局唯一值 | 标识要监视的应用的名称 |
   | **资源组**     | myResourceGroup      | 用于承载 Application Insights 数据的新资源组的名称 |
   | **位置** | 美国东部 | 附近的位置或应用托管位置附近 |

1. 选择“创建”。

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>用于 Azure Monitor 的 OpenCensus Python SDK 检测

安装 OpenCensus Azure Monitor 导出程序：

```console
python -m pip install opencensus-ext-azure
```

有关包和集成的完整列表，请参阅[OpenCensus 包](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus)。

> [!NOTE]
> `python -m pip install opencensus-ext-azure` 命令假设你为 Python 安装设置了 `PATH` 环境变量。 如果尚未配置此变量，则需要为 Python 可执行文件所在的位置指定完整的目录路径。 结果如下所示的命令： `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`。

SDK 使用三个 Azure Monitor 导出程序将不同类型的遥测发送到 Azure Monitor：跟踪、指标和日志。 有关这些遥测类型的详细信息，请参阅[数据平台概述](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)。 使用以下说明通过三个导出程序发送这些遥测类型。

## <a name="telemetry-type-mappings"></a>遥测类型映射

下面是 OpenCensus 提供的导出程序，它映射到你将在 Azure Monitor 中看到的遥测类型。

![将遥测类型从 OpenCensus 映射到 Azure Monitor 的屏幕截图](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>跟踪

> [!NOTE]
> OpenCensus 中的 `Trace` 是指[分布式跟踪](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)。 `AzureExporter` 会将 `requests` 和 `dependency` 遥测发送到 Azure Monitor。

1. 首先，让我们在本地生成某些跟踪数据。 在 Python 空闲或您选择的编辑器中，输入以下代码。

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

2. 运行代码时，系统会重复提示你输入一个值。 对于每个条目，值都将打印到 shell，OpenCensus Python 模块将生成相应的 `SpanData`。 OpenCensus 项目将跟踪定义[为范围的树](https://opencensus.io/core-concepts/tracing/)。
    
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

3. 尽管输入值有助于演示，但最终我们希望将 `SpanData` 发送到 Azure Monitor。 根据下面的代码示例，从上一步中修改代码：

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

4. 现在，运行 Python 脚本时，系统仍会提示输入值，但会仅在 shell 中打印值。 创建的 `SpanData` 将发送到 Azure Monitor。 您可以在 `dependencies`下找到已发出的范围数据。

5. 有关 OpenCensus 中采样的详细信息，请参阅[OpenCensus 中的采样](https://docs.microsoft.com/azure/azure-monitor/app/sampling#configuring-fixed-rate-sampling-in-opencensus-python)。

6. 有关跟踪数据中遥测关联的详细信息，请参阅 OpenCensus[遥测关联](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)。

### <a name="metrics"></a>度量值

1. 首先，让我们生成一些本地指标数据。 我们将创建一个简单的度量值，用于跟踪用户按 Enter 的次数。

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
2. 运行代码将重复提示你按 Enter。 将创建一个度量值，用于跟踪按下 Enter 的次数。 对于每个条目，该值会递增，指标信息将显示在控制台中。 此信息包括当前值以及更新指标时的当前时间戳。

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. 尽管输入值有助于演示目的，但最终我们希望将指标数据发送到 Azure Monitor。 根据下面的代码示例，从上一步中修改代码：

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

4. 导出程序会按固定的时间间隔将指标数据发送到 Azure Monitor。 默认值为每15秒。 我们正在跟踪单个度量值，因此，每个间隔将发送此指标数据，其中包含其包含的任何值和时间戳。 可以在 `customMetrics`下查找数据。

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

2.  代码将持续要求输入值。 对于输入的每个值发出一个日志条目。

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

3. 尽管输入值有助于演示目的，但最终我们希望将日志数据发送到 Azure Monitor。 根据下面的代码示例，从上一步中修改代码：

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

4. 导出程序会将日志数据发送到 Azure Monitor。 可以在 `traces`下查找数据。 

> [!NOTE]
> 此上下文中 `traces` 与 `Tracing`不同。 `traces` 是指在使用 `AzureLogHandler`时将在 Azure Monitor 中看到的遥测类型。 `Tracing` 是指 OpenCensus 中的一种概念，并与[分布式跟踪](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing)相关。

5. 若要格式化日志消息，可以使用内置 Python[日志记录 API](https://docs.python.org/3/library/logging.html#formatter-objects)中的 `formatters`。

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

6. 你还可以将自定义维度添加到日志中。 它们将显示为 Azure Monitor 中 `customDimensions` 的键值对。
> [!NOTE]
> 要使此功能正常工作，需要将字典作为参数传递给日志，将忽略任何其他数据结构。 若要维护字符串格式，请将其存储在字典中，并将它们作为参数传递。

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    logger.warning('action', {'key-1': 'value-1', 'key-2': 'value2'})
    ```

7. 有关如何利用跟踪上下文数据丰富日志的详细信息，请参阅 OpenCensus Python[日志集成](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation)。

## <a name="view-your-data-with-queries"></a>通过查询查看数据

可以通过 "**日志（分析）** " 选项卡查看从应用程序发送的遥测数据。

!["概述" 窗格的屏幕截图，其中在红色框中选择了 "日志（分析）"](./media/opencensus-python/0010-logs-query.png)

在 "**活动**" 下的列表中：

- 对于随 Azure Monitor 跟踪导出程序发送的遥测数据，传入的请求显示在 "`requests`下。 传出或进程内请求出现在 `dependencies`下。
- 对于随 Azure Monitor 度量值导出程序一起发送的遥测数据，发送的度量值将显示在 "`customMetrics`"
- 对于随 Azure Monitor 日志导出程序发送的遥测数据，日志显示在 "`traces`下。 异常将显示在 `exceptions`下。

有关如何使用查询和日志的详细信息，请参阅[中的日志 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)。

## <a name="learn-more-about-opencensus-for-python"></a>详细了解用于 Python 的 OpenCensus

* [GitHub 上的 OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [自定义](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Flask 集成](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django 集成](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL 集成](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>后续步骤

* [应用程序映射](./../../azure-monitor/app/app-map.md)
* [端到端性能监视](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>警报

* [可用性测试](../../azure-monitor/app/monitor-web-app-availability.md)：创建测试来确保站点在 Web 上可见。
* [智能诊断](../../azure-monitor/app/proactive-diagnostics.md)：这些测试可自动运行，因此不需要进行任何设置。 它们会告诉你应用是否具有异常的失败请求速率。
* [指标警报](../../azure-monitor/app/alerts.md)：设置警报，以便在指标超过阈值时发出警告。 可以在编码到应用中的自定义指标中设置它们。
