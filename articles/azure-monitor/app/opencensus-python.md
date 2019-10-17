---
title: 监视具有 Azure Monitor 的 Python 应用程序（预览） |Microsoft Docs
description: 提供有关将 OpenCensus Python 与 Azure Monitor 连接的说明
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: ed61cb1bc88c48fe89c4a9390f04747749bd48c5
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329482"
---
# <a name="set-up-azure-monitor-for-your-python-application-preview"></a>设置 Python 应用程序的 Azure Monitor （预览）

Azure Monitor 通过与[OpenCensus](https://opencensus.io)集成，支持 Python 应用程序的分布式跟踪、指标收集和日志记录。 本文将逐步指导你逐步完成为 Python 设置 OpenCensus 并使监视数据 Azure Monitor 的过程。

## <a name="prerequisites"></a>必备组件

- 需要一个 Azure 订阅。
- 如果已安装 python，则本文将使用[python 3.7.0](https://www.python.org/downloads/)，但较早的版本可能会进行次要调整。

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-application-insights-resource-in-azure-monitor"></a>在 Azure Monitor 中创建 Application Insights 资源

首先必须在 Azure Monitor 中创建 Application Insights 资源，这将生成一个检测密钥（ikey）。 然后，使用 ikey 将 OpenCensus SDK 配置为将遥测数据发送到 Azure Monitor。

1. 选择“创建资源” > “开发人员工具” > “Application Insights”。

   ![添加 Application Insights 资源](./media/opencensus-python/0001-create-resource.png)

   此时会显示配置对话框，请使用下表填写输入字段。

    | 设置        | Value           | 描述  |
   | ------------- |:-------------|:-----|
   | 名称      | 全局唯一值 | 标识所监视的应用的名称 |
   | **资源组**     | myResourceGroup      | 用于托管 App Insights 数据的新资源组的名称 |
   | 位置 | 美国东部 | 选择离你近的位置或离托管应用的位置近的位置 |

2. 单击“创建”。

## <a name="instrumenting-with-opencensus-python-sdk-for-azure-monitor"></a>通过用于 Azure Monitor 的 OpenCensus Python SDK 进行检测

1. 安装 OpenCensus Azure Monitor 导出程序：

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` 假定你已为 Python 安装设置了一个 PATH 环境变量。 如果尚未对此进行配置，则需提供完整的目录路径，以便放置 Python 可执行文件，生成的命令类似于：`C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`。

2. SDK 利用三个 Azure Monitor 导出程序将不同类型的遥测发送到 Azure Monitor：跟踪、指标和日志。 请查看[数据平台概述](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)，了解有关这些不同类型的详细信息。 请按照下面的说明操作，了解如何通过三个导出程序发送这些不同的类型。

### <a name="trace"></a>跟踪

1. 首先，让我们在本地生成一些跟踪数据。 在 Python 空闲或您选择的编辑器中，输入以下代码。

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

2. 运行代码时，系统会重复提示你输入一个值。 每次输入时，值都会输出到 shell，并会由 OpenCensus Python 模块生成相应的 **SpanData** 块。 OpenCensus 项目将[_跟踪定义为 span 树_](https://opencensus.io/core-concepts/tracing/)。
    
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

3. 虽然对于演示非常有用，但最终我们需要发出 `SpanData` 到 Azure Monitor。 根据下面的代码示例，从上一步中修改代码：

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        ),
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

4. 现在，运行 Python 脚本时，系统仍会提示输入值，但现在只能在 shell 中打印值。 创建的 @no__t 0 将发送到 Azure Monitor。 可以在 `dependencies` 下找到发出的范围数据。

### <a name="metrics"></a>指标

1. 首先，让我们生成一些本地指标数据。 我们将创建一个简单的度量值，用于跟踪用户按 enter 的次数。

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
2. 运行代码将重复提示你按 enter。 将创建一个度量值，用于跟踪按下的输入数目。 对于每个条目，该值将递增，并且指标信息将显示在控制台中，并在更新指标时显示当前值和当前时间戳。

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. 虽然对于演示非常有用，但最终我们希望将指标数据发送到 Azure Monitor。 根据下面的代码示例，从上一步中修改代码：

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
    )
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

4. 导出程序会按固定的时间间隔将指标数据发送到 Azure Monitor，默认值为每15秒一次。 我们正在跟踪单个度量值，因此将每个间隔发送此指标数据（包含其包含的任何值和时间戳）。 可以在 `customMetrics` 下查找数据。

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

2.  该代码将要求持续提示输入值。 对于输入的每个值，都将发出一个日志条目，其中包含所说的值。

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

3. 虽然对于演示非常有用，但最终我们希望将指标数据发送到 Azure Monitor。 根据下面的代码示例，从上一步中修改代码：

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

4. 导出程序会将日志数据发送到 Azure Monitor。 可以在 `traces` 下查找数据。

## <a name="start-monitoring-in-the-azure-portal"></a>开始在 Azure 门户中监视

1. 现在可以在 Azure 门户中重新打开 Application Insights“概览”页，查看当前正在运行的应用程序的详细信息。 选择“实时指标流”。

   ![概览窗格的屏幕截图，其中的实时指标流在红框中呈选中状态。](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. 导航回“概览”页，选择“应用程序映射”以获取应用程序组件之间依赖关系和调用时间的可视布局。

    ![基本应用程序映射的屏幕截图](./media/opencensus-python/0007-application-map.png)

    由于我们只跟踪一个方法调用，因此应用程序映射的信息不多。 但是，应用程序映射可以通过缩放将多得多的分布式应用程序可视化：

   ![应用程序地图](media/opencensus-python/application-map.png)

3. 选择“调查性能”，执行详细的性能分析并确定性能减慢的根本原因。

    ![性能窗格的屏幕截图](./media/opencensus-python/0008-performance.png)

4. 选择“示例”，然后单击显示在右窗格中的任意示例，这将启动端到端事务详细信息体验。 虽然我们的示例应用只会显示单个事件，但更复杂的应用程序会让你在探索端到端事务时，可以深入到单个事件的调用堆栈级别。

     ![端到端事务界面的屏幕截图](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="view-your-data-with-queries"></a>通过查询查看数据

1. 可以通过 "日志（分析）" 选项卡查看从应用程序发送的遥测数据。

    ![在红色框中选择了日志（Analytics）的 "概述" 窗格的屏幕截图](./media/opencensus-python/0010-logs-query.png)

2. 对于随 Azure Monitor 跟踪导出程序发送的遥测数据，传入的请求将显示在 `requests` 下，传出/正在处理的请求将显示在 `dependencies` 下。

3. 对于随 Azure Monitor 度量值导出程序一起发送的遥测数据，发送的指标将显示在 `customMetrics` 下。

4. 对于随 Azure Monitor 日志导出程序发送的遥测数据，日志将显示在 `traces` 下，异常将显示在 `exceptions` 下。

5. 有关如何使用查询和日志的详细信息，请查看[Azure Monitor 中的日志](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs)。

## <a name="opencensus-for-python"></a>用于 Python 的 OpenCensus

* [GitHub 上的 OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [自定义](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Flask 集成](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django 集成](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL 集成](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>后续步骤

* [API 摘要](./../../azure-monitor/app/api-custom-events-metrics.md)
* [应用程序映射](./../../azure-monitor/app/app-map.md)
* [端到端性能监视](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>警报

* [可用性测试](../../azure-monitor/app/monitor-web-app-availability.md)：创建测试来确保站点在 Web 上可见。
* [智能诊断](../../azure-monitor/app/proactive-diagnostics.md)：这些测试可自动运行，因此不需要进行任何设置。 它们会告诉你应用是否具有异常的失败请求速率。
* [指标警报](../../azure-monitor/app/alerts.md)：设置警报，以便在指标超过阈值时发出警告。 可以在编码到应用中的自定义指标中设置它们。