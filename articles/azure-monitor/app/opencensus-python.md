---
title: 使用 Azure Application Insights 进行 OpenCensus Python 跟踪 | Microsoft Docs
description: 提供的说明介绍如何将 OpenCensus Python 跟踪与本地转发器和 Application Insights 集成
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/18/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ae9db483e15197e6cdaaaa5981410630184cc6ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65957243"
---
# <a name="collect-distributed-traces-from-python-preview"></a>从 Python（预览版）收集分布式跟踪

Application Insights 现在支持通过与 [OpenCensus](https://opencensus.io) 和我们新的[本地转发器](./../../azure-monitor/app/opencensus-local-forwarder.md)集成来对 Python 应用程序进行分布式跟踪。 本文将逐步介绍设置 OpenCensus for Python 并将跟踪数据提供给 Application Insights 的过程。

## <a name="prerequisites"></a>必备组件

- 需要一个 Azure 订阅。
- 应该安装 Python。本文使用 [Python 3.7.0](https://www.python.org/downloads/)，不过更早的版本在进行微调后也可能适用。
- 按照说明安装 [Windows 服务形式的本地转发器](./../../azure-monitor/app/opencensus-local-forwarder.md)

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-application-insights-resource"></a>创建 Application Insights 资源

首先需创建一个 Application Insights 资源，该资源将生成一个检测密钥 (ikey)。 然后使用 ikey 配置本地转发器，将 OpenCensus 检测应用程序中的分布式跟踪发送到 Application Insights。   

1. 选择“创建资源”   > “开发人员工具”   > “Application Insights”  。

   ![添加 Application Insights 资源](./media/opencensus-python/0001-create-resource.png)

   此时会显示配置对话框，请使用下表填写输入字段。

    | 设置        | 值           | 说明  |
   | ------------- |:-------------|:-----|
   | **名称**      | 全局唯一值 | 标识所监视的应用的名称 |
   | **应用程序类型** | 常规 | 所监视的应用的类型 |
   | **资源组**     | myResourceGroup      | 用于托管 App Insights 数据的新资源组的名称 |
   | **位置** | 美国东部 | 选择离你近的位置或离托管应用的位置近的位置 |

2. 单击**创建**。

## <a name="configure-local-forwarder"></a>配置本地转发器

1. 选择“概述”   > “概要”  > 复制应用程序的**检测密钥**。

   ![检测密钥的屏幕截图](./media/opencensus-python/0003-instrumentation-key.png)

2. 编辑 `LocalForwarder.config` 文件并添加检测密钥。 如果已按照[先决条件](./../../azure-monitor/app/opencensus-local-forwarder.md)中的说明操作，则该文件位于 `C:\LF-WindowsServiceHost`

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. 重启应用程序**本地转发器**服务。

## <a name="opencensus-python-package"></a>OpenCensus Python 包

1. 安装适用于 Python 和 pip 或从命令行 pipenv 导出程序打开人口普查包：

    ```console
    python -m pip install opencensus
    python -m pip install opencensus-ext-ocagent

    # pip env install opencensus
    ```

    > [!NOTE]
    > `python -m pip install opencensus` 假定你已为 Python 安装设置了一个 PATH 环境变量。 如果尚未对此进行配置，则需提供完整的目录路径，以便放置 Python 可执行文件，生成的命令类似于：`C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus`。

2. 首先，让我们在本地生成一些跟踪数据。 在 Python IDLE 或所选编辑器中，输入以下代码：

    ```python
    from opencensus.trace.tracer import Tracer

    def main():
        while True:
            valuePrompt()

    def valuePrompt():
        tracer = Tracer()
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    if __name__ == "__main__":
        main()

    ```

3. 运行代码时，系统会重复提示你输入一个值。 每次输入时，值都会输出到 shell，并会由 OpenCensus Python 模块生成相应的 **SpanData** 块。 OpenCensus 项目将[_跟踪定义为 span 树_](https://opencensus.io/core-concepts/tracing/)。
    
    ```python
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=1f07f062ac394c50925f2ae61e635e14, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='5c17a4ad6ba14299', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:15.847292Z', end_time='2018-09-15T20:42:17.615664Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=c71b4e88a22a495da61df52ce3eee3e1, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='51547c0af5f046eb', parent_span_id=None, attributes={}, start_time='2018-09-15T20:42:17.615664Z', end_time='2018-09-15T20:48:11.160314Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=b4cdcc9e6df44a8fbb6e8ddeccc1351c, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f2caacf7892744d1', parent_span_id=None, attributes={}, start_time='2018-09-15T20:48:11.175931Z', end_time='2018-09-15T20:48:12.629178Z', child_span_count=0, stack_trace=None, time_events=[], links=[], status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

4. 虽然这适用于演示目的，但最终我们希望以适当的方式发出 SpanData，以便它能够被**本地转发器服务**拾取并发送到 Application Insights。 将上一步的代码修改如下：

    ```python
    from opencensus.trace.tracer import Tracer
    from opencensus.trace import config_integration
    from opencensus.ext.ocagent.trace_exporter import TraceExporter
    from opencensus.trace import tracer as tracer_module

    import os

    def main():
        while True:
            valuePrompt()

    def valuePrompt():
        export_LocalForwarder = TraceExporter(
        service_name=os.getenv('SERVICE_NAME', 'python-service'),
        endpoint=os.getenv('OCAGENT_TRACE_EXPORTER_ENDPOINT'))

        tracer = Tracer(exporter=export_LocalForwarder)
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    if __name__ == "__main__":
        main()

    ```

5. 如果保存上述模块并尝试运行它，可能会收到针对 `grpc` 的 `ModuleNotFoundError`。 如果发生这种情况，请运行以下命令，以便用其安装 [grpcio 包](https://pypi.org/project/grpcio/)：

    ```console
    python -m pip install grpcio
    ```

6. 现在当你运行上述 Python 脚本时，系统仍会提示你输入值，但现在只有此值输出到 shell 中。

7. 若要确认**本地转发器**是否正在拾取跟踪，请检查 `LocalForwarder.config` 文件。 如果已按照[先决条件](https://docs.microsoft.com/azure/application-insights/local-forwarder)中的步骤执行了操作，它将位于 `C:\LF-WindowsServiceHost` 中。

    在下面的日志文件图像中，可以看到在运行第二个脚本（已在其中添加了导出程序）之前，`OpenCensus input BatchesReceived` 为 0。 开始运行更新的脚本以后，`BatchesReceived` 根据我们输入的值的数目递增：
    
    ![“新建 App Insights 资源”窗体](./media/opencensus-python/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>开始在 Azure 门户中监视

1. 现在可以在 Azure 门户中重新打开 Application Insights“概览”  页，查看当前正在运行的应用程序的详细信息。 选择“实时指标流”  。

   ![概览窗格的屏幕截图，其中的实时指标流在红框中呈选中状态。](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. 如果再次运行第二个 Python 脚本并开始输入值，则当实时跟踪数据从本地转发器服务到达 Application Insights 时，你会看到它们。

   ![实时指标流的屏幕截图，其中显示了性能数据](./media/opencensus-python/0006-stream.png)

3. 导航回“概览”页，选择“应用程序映射”以获取应用程序组件之间依赖关系和调用时间的可视布局。  

    ![基本应用程序映射的屏幕截图](./media/opencensus-python/0007-application-map.png)

    由于我们只跟踪一个方法调用，因此应用程序映射的信息不多。 但是，应用程序映射可以通过缩放将多得多的分布式应用程序可视化：

   ![应用程序地图](media/opencensus-python/application-map.png)

4. 选择“调查性能”，执行详细的性能分析并确定性能减慢的根本原因。 

    ![性能窗格的屏幕截图](./media/opencensus-python/0008-performance.png)

5. 选择“示例”，然后单击显示在右窗格中的任意示例，这将启动端到端事务详细信息体验。  虽然我们的示例应用只会显示单个事件，但更复杂的应用程序会让你在探索端到端事务时，可以深入到单个事件的调用堆栈级别。

     ![端到端事务界面的屏幕截图](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-python"></a>适用于 Python 的 OpenCensus 跟踪

我们只简单介绍了如何将适用于 Python 的 OpenCensus 与本地转发器和 Application Insights 集成。 正式的使用指南会介绍更高级的主题，例如：

* [取样器](https://opencensus.io/api/python/trace/usage.html#samplers)
* [Flask 集成](https://opencensus.io/api/python/trace/usage.html#flask)
* [Django 集成](https://opencensus.io/api/python/trace/usage.html#django)
* [MySQL 集成](https://opencensus.io/api/python/trace/usage.html#service-integration)
* [PostgreSQL](https://opencensus.io/api/python/trace/usage.html#postgresql)
  
## <a name="next-steps"></a>后续步骤

* [OpenCensus Python 使用指南](https://opencensus.io/api/python/trace/usage.html)
* [应用程序映射](./../../azure-monitor/app/app-map.md)
* [端到端性能监视](./../../azure-monitor/learn/tutorial-performance.md)
