---
title: 监视使用 Azure Application Insights 的 Python 应用程序 |Microsoft Docs
description: 提供有关使用 Application Insights OpenCensus Python 绑定的说明
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 07/02/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 2c043ad793dcf5e59eaf460d1ec4aa7a3b48810d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541435"
---
# <a name="collect-distributed-traces-from-python-preview"></a>从 Python（预览版）收集分布式跟踪

现在支持分布式跟踪通过与集成的 Python 应用程序的 application Insights [OpenCensus](https://opencensus.io)。 本文将指导你逐步完成为 Python 设置 OpenCensus 和获取到 Application Insights 监视数据的过程。

## <a name="prerequisites"></a>必备组件

- 需要一个 Azure 订阅。
- 应该安装 Python。本文使用 [Python 3.7.0](https://www.python.org/downloads/)，不过更早的版本在进行微调后也可能适用。

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-application-insights-resource"></a>创建 Application Insights 资源

首先，您必须创建 Application Insights 资源，这会导致生成检测 key(ikey)。 然后使用 ikey 配置 OpenCensus SDK 将遥测数据发送到 Application Insights。

1. 选择“创建资源”   > “开发人员工具”   > “Application Insights”  。

   ![添加 Application Insights 资源](./media/opencensus-python/0001-create-resource.png)

   此时会显示配置对话框，请使用下表填写输入字段。

    | 设置        | 值           | 说明  |
   | ------------- |:-------------|:-----|
   | **Name**      | 全局唯一值 | 标识所监视的应用的名称 |
   | **资源组**     | myResourceGroup      | 用于托管 App Insights 数据的新资源组的名称 |
   | **位置** | East US | 选择离你近的位置或离托管应用的位置近的位置 |

2. 单击**创建**。

## <a name="install-opencensus-azure-monitor-exporters"></a>安装 OpenCensus Azure 监视器导出程序

1. 安装 OpenCensus Azure 监视器导出程序：

    ```console
    python -m pip install opencensus-ext-azure
    ```

    > [!NOTE]
    > `python -m pip install opencensus-ext-azure` 假定你已为 Python 安装设置了一个 PATH 环境变量。 如果尚未对此进行配置，则需提供完整的目录路径，以便放置 Python 可执行文件，生成的命令类似于：`C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`。

2. 首先，让我们在本地生成一些跟踪数据。 在 Python IDLE 或所选编辑器中，输入以下代码：

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

3. 运行代码时，系统会重复提示你输入一个值。 每次输入时，值都会输出到 shell，并会由 OpenCensus Python 模块生成相应的 **SpanData** 块。 OpenCensus 项目将[_跟踪定义为 span 树_](https://opencensus.io/core-concepts/tracing/)。
    
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

4. 尽管出于演示目的有帮助，最终我们希望发出到 Application Insights SpanData。 修改代码，从前面的步骤基于下面的代码示例：

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
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
5. 现在，当您运行的 Python 脚本，您应仍会提示您输入值，但现在正在仅值打印在外壳程序中。

## <a name="start-monitoring-in-the-azure-portal"></a>开始在 Azure 门户中监视

1. 现在可以在 Azure 门户中重新打开 Application Insights“概览”  页，查看当前正在运行的应用程序的详细信息。 选择“实时指标流”  。

   ![概览窗格的屏幕截图，其中的实时指标流在红框中呈选中状态。](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. 导航回“概览”页，选择“应用程序映射”以获取应用程序组件之间依赖关系和调用时间的可视布局。  

    ![基本应用程序映射的屏幕截图](./media/opencensus-python/0007-application-map.png)

    由于我们只跟踪一个方法调用，因此应用程序映射的信息不多。 但是，应用程序映射可以通过缩放将多得多的分布式应用程序可视化：

   ![应用程序地图](media/opencensus-python/application-map.png)

3. 选择“调查性能”，执行详细的性能分析并确定性能减慢的根本原因。 

    ![性能窗格的屏幕截图](./media/opencensus-python/0008-performance.png)

4. 选择“示例”，然后单击显示在右窗格中的任意示例，这将启动端到端事务详细信息体验。  虽然我们的示例应用只会显示单个事件，但更复杂的应用程序会让你在探索端到端事务时，可以深入到单个事件的调用堆栈级别。

     ![端到端事务界面的屏幕截图](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-for-python"></a>用于 Python 的 OpenCensus

* [自定义](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Flask 集成](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Django 集成](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [MySQL 集成](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>后续步骤

* [GitHub 上的 OpenCensus Python](https://github.com/census-instrumentation/opencensus-python)
* [应用程序映射](./../../azure-monitor/app/app-map.md)
* [端到端性能监视](./../../azure-monitor/learn/tutorial-performance.md)