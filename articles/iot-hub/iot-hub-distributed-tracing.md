---
title: 使用分布式跟踪（预览版）将关联 ID 添加到 IoT 消息
description: ''
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.openlocfilehash: e4403c245a3cae671f83260ae313ed400b0f7721
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259352"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>使用分布式跟踪（预览版）跟踪 Azure IoT 设备到云的消息

Microsoft Azure IoT 中心目前支持分布式跟踪[预览版功能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

IoT 中心是用于支持分布式跟踪的第一批 Azure 服务之一。 随着支持分布式跟踪的 Azure 服务越来越多，以后你可以在解决方案涉及的所有 Azure 服务中跟踪 IoT 消息。 有关分布式跟踪的背景信息，请参阅[分布式跟踪](../azure-monitor/app/distributed-tracing.md)。

为 IoT 中心启用分布式跟踪可以：

- 使用[跟踪上下文](https://github.com/w3c/trace-context)准确监视每个消息流经 IoT 中心的情况。 此跟踪上下文包含关联 ID。使用关联 ID 可将来自一个组件的事件与来自另一个组件的事件相关联。 可以使用[设备孪生](iot-hub-devguide-device-twins.md)对一部分或所有 IoT 设备消息应用关联 ID。
- 自动将跟踪上下文记录到 [Azure Monitor 诊断日志](iot-hub-monitor-resource-health.md)。
- 度量并了解设备与 IoT 中心之间的消息流和延迟以及路由终结点。
- 开始考虑如何针对 IoT 解决方案中的非 Azure 服务实施分布式跟踪。

本文将[适用于 C 的 Azure IoT 设备 SDK](./iot-hub-device-sdk-c-intro.md) 与分布式跟踪配合使用。 对其他 SDK 的分布式跟踪支持仍在开发中。

## <a name="prerequisites"></a>先决条件

- 分布式跟踪预览版目前仅支持在以下区域中创建的 IoT 中心：

  - **北欧**
  - **东南亚**
  - **美国西部 2**

- 本文假设读者知道如何将遥测消息发送到 IoT 中心。 请确保已完成[发送遥测数据 C 快速入门](./quickstart-send-telemetry-c.md)。

- 将某个设备注册到 IoT 中心（每篇快速入门中都提供了相关步骤），并记下连接字符串。

- [安装最新版本的 Git](https://git-scm.com/download/)。

## <a name="configure-iot-hub"></a>配置 IoT 中心

在本部分，你将配置一个 IoT 中心来记录分布式跟踪属性（关联 ID 和时间戳）。

1. 在 [Azure 门户](https://portal.azure.com/)中导航到该 IoT 中心。

1. 在 IoT 中心的左窗格中，向下滚动到“监视”部分并单击“诊断设置”。

1. 如果尚未启用诊断设置，请单击“启用诊断”。 如果已启用诊断设置，请单击“添加诊断设置”。

1. 在“名称”字段中，输入新诊断设置的名称。 例如 **DistributedTracingSettings**。

1. 选择以下一个或多个选项用于确定要将日志发送到何处：

    - **存档到存储帐户**：配置一个用于保存日志信息的存储帐户。
    - **流式传输到事件中心**：配置一个用于保存日志信息的事件中心。
    - **发送到 Log Analytics**：配置一个用于保存日志信息的 Log Analytics 工作区。

1. 在“日志”部分，选择要记录其信息的操作。

    请务必包含“DistributedTracing”，并配置“保留期”以指定要将日志保留几天。 保留日志会影响存储费用。

    ![显示 DistributedTracing 类别在 IoT 诊断设置中的位置的屏幕截图](./media/iot-hub-distributed-tracing/diag-logs.png)

1. 单击新设置旁边的“保存”。

1. （可选）若要了解消息如何流向不同的位置，请[对至少两个不同的终结点设置路由规则](iot-hub-devguide-messages-d2c.md)。

启用日志记录后，在以下任一情况下遇到包含有效跟踪属性的消息时，IoT 中心会记录日志：

- 消息抵达 IoT 中心的网关。
- IoT 中心处理消息。
- 消息路由到自定义终结点。 必须启用路由。

若要详细了解这些日志及其架构，请参阅 [IoT 中心诊断日志中的分布式跟踪](iot-hub-monitor-resource-health.md#distributed-tracing-preview)。

## <a name="set-up-device"></a>设置设备

在本部分，你将准备一个与 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 配合使用的开发环境。 然后修改某个示例，以针对设备的遥测消息启用分布式跟踪。

这些说明适用于在 Windows 上生成示例。 对于其他环境，请参阅[编译 C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) 或[用于平台特定的开发的预打包 C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development)。

### <a name="clone-the-source-code-and-initialize"></a>克隆源代码并初始化

1. 安装适用于 Visual Studio 2015 或 2017 的[“使用 C++ 的桌面开发”工作负荷](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2017)。

1. 安装 [CMake](https://cmake.org/)。 在命令提示符下键入 `cmake -version`，确保 CMake 位于 `PATH` 中。

1. 打开命令提示符或 Git Bash shell。 执行以下命令克隆 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库：

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    应该预料到此操作需要几分钟才能完成。

1. 在 git 存储库的根目录中创建 `cmake` 子目录，并导航到该文件夹。

    ```cmd
    cd azure-iot-sdk-c    
    mkdir cmake
    cd cmake
    cmake ..
    ```

    如果 `cmake` 找不到 C++ 编译器，则可能会在运行以上命令时出现生成错误。 如果出现这种情况，请尝试在 [Visual Studio 命令提示符](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)窗口中运行该命令。 

    生成成功后，最后的几个输出行如下所示：

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>编辑发送遥测数据的示例以启用分布式跟踪

1. 使用编辑器打开 `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` 源文件。

1. 找到 `connectionString` 常量的声明：

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    将 `connectionString` 常量值替换为在[发送遥测数据 C 快速入门](./quickstart-send-telemetry-c.md)的[注册设备](./quickstart-send-telemetry-c.md#register-a-device)部分记下的设备连接字符串。

1. 将 `MESSAGE_COUNT` 定义更改为 `5000`：

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. 找到在发送消息循环之前调用 `IoTHubDeviceClient_LL_SetConnectionStatusCallback` 注册连接状态回调函数的代码行。 在该代码行的下面添加如下所示的代码，以调用 `IoTHubDeviceClient_LL_EnablePolicyConfiguration` 来为设备启用分布式跟踪：

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    `IoTHubDeviceClient_LL_EnablePolicyConfiguration` 函数针对通过[设备孪生](./iot-hub-devguide-device-twins.md)配置的特定 IoT 中心功能启用策略。 使用上述代码行启用 `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` 之后，设备的跟踪行为将反映在设备孪生中所做的分布式跟踪更改。

1. 若要使示例应用保持运行且不耗尽所有配额，请在发送消息循环的末尾添加一秒钟的延迟：

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>编译和运行

1. 从前面创建的 CMake 目录 (`azure-iot-sdk-c/cmake`) 导航到 *iothub_ll_telemetry_sample* 项目目录，并编译示例：

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. 运行应用程序。 设备将发送支持分布式跟踪的遥测数据。

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. 保持运行该应用。 （可选）通过查看控制台窗口来观察正在发送到 IoT 中心的消息。

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>第三方客户端的解决方法

无需使用 C **SDK 即可预览**分布式跟踪功能。 因此，不建议使用此方法。

首先，必须遵循开发指南[创建和读取 Iot 中心消息](iot-hub-devguide-messages-construct.md)，在消息中实现所有 IoT 中心协议基元。 然后，在 MQTT/AMQP 消息中编辑要添加`tracestate`为**系统属性**的协议属性。 具体而言：

* 对于 MQTT，请`%24.tracestate=timestamp%3d1539243209`将添加到消息主题， `1539243209`其中应该替换为 unix 时间戳格式的消息创建时间。 作为示例，请参阅[C SDK 中](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761)的实现
* 对于 AMQP，将`key("tracestate")`和`value("timestamp=1539243209")`添加为消息注释。 有关引用实现，请参阅[此处](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527)。

若要控制包含此属性的消息百分比，请实现相应的逻辑来侦听云发起的事件，例如孪生更新。

## <a name="update-sampling-options"></a>更新采样选项 

若要更改要从云跟踪的消息百分比，必须更新设备孪生。 可通过多种方式完成此操作，包括使用门户中的 JSON 编辑器，或者 IoT 中心服务 SDK。 以下附属部分提供了示例。

### <a name="update-using-the-portal"></a>使用门户更新

1. 在 [Azure 门户](https://portal.azure.com/)中导航到自己的 IoT 中心，然后单击“IoT 设备”。

1. 单击自己的设备。

1. 找到“启用分布式跟踪(预览版)”，然后选择“启用”。

    ![在 Azure 门户中启用分布式跟踪](./media/iot-hub-distributed-tracing/azure-portal.png)

1. 选择介于 0% 与 100% 之间的**采样率**。

1. 单击“保存”。

1. 等待几秒钟，然后点击“刷新”，如果设备已成功确认，则会显示一个带有勾选标记的同步图标。

1. 返回到遥测消息应用的控制台窗口。 将会看到正在发送消息，消息的应用程序属性中包含 `tracestate`。

    ![跟踪状态](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. （可选）将采样率更改为不同的值，然后观察消息在应用程序属性中包含 `tracestate` 的频率变化。

### <a name="update-using-azure-iot-hub-toolkit-for-vs-code"></a>使用用于 VS Code 的 Azure IoT 中心工具包更新

1. 安装 VS Code，然后从[此处](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)安装最新版本的用于 VS Code 的 Azure IoT 中心工具包。

1. 打开 VS Code 并[设置 IoT 中心连接字符串](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites)。

1. 展开设备，并找到“分布式跟踪设置(预览版)”。 在该选项的下面，单击子节点对应的“更新分布式跟踪设置(预览版)”。

    ![在 Azure IoT 中心工具包中启用分布式跟踪](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. 在弹出窗口中选择“启用”，然后按 Enter 确认使用 100 作为采样率。

    ![更新采样模式](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![更新采样率](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>针对多个设备进行批量更新

若要更新多个设备的分布式跟踪采样配置，请使用[自动设备配置](iot-hub-auto-device-config.md)。 确保遵循此孪生架构：

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| 元素名称 | 必填 | 类型 | 描述 |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | 是 | 整数 | 目前支持使用两个模式值来启用和禁用采样。 `1` 表示启用，`2` 表示禁用。 |
| `sampling_rate` | 是 | 整数 | 此值是百分比。 只允许使用从 `0` 到 `100`（含）的值。  |

## <a name="query-and-visualize"></a>查询和可视化

若要查看 IoT 中心记录的所有跟踪，请查询在诊断设置中选择的日志存储。 本部分逐步讲解几个不同的选项。

### <a name="query-using-log-analytics"></a>使用 Log Analytics 进行查询

如果已设置[使用诊断日志的 Log Analytics](../azure-monitor/platform/resource-logs-collect-storage.md)，请通过查找 `DistributedTracing` 类别中的日志进行查询。 例如，以下查询显示记录所有的跟踪：

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Log Analytics 显示的示例日志：

| TimeGenerated | OperationName | 类别 | Level | CorrelationId | DurationMs | 属性 |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | DistributedTracing | 信息性 | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress | DistributedTracing | 信息性 | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | DistributedTracing | 信息性 | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

若要了解不同类型的日志，请参阅 [Azure IoT 中心诊断日志](iot-hub-monitor-resource-health.md#distributed-tracing-preview)。

### <a name="application-map"></a>应用程序映射

若要可视化 IoT 消息流，请设置应用程序映射示例应用。 该示例应用使用 Azure 函数和事件中心将分布式跟踪日志发送到[应用程序映射](../application-insights/app-insights-app-map.md)。

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">在 GitHub 上获取示例</a>

下图显示了应用映射中的分布式跟踪，其中包含三个路由终结点：

![应用映射中的 IoT 分布式跟踪](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>了解 Azure IoT 分布式跟踪

### <a name="context"></a>上下文

许多 IoT 解决方案，包括我们自己的[参考体系结构](https://aka.ms/iotrefarchitecture)（仅有英语版），通常遵循[微服务体系结构](https://docs.microsoft.com/azure/architecture/microservices/)的某种变体。 随着 IoT 解决方案变得越来越复杂，最终会使用十多个甚至更多的微服务。 这些微服务不一定来自 Azure。 可能很难查明 IoT 消息是在哪个位置删除或减慢的。 例如，某个 IoT 解决方案使用 5 个不同的 Azure 服务和 1500 个活动设备。 每个设备每秒发送 10 条设备到云的消息（每秒总共发送 15,000 条消息），但你注意到，Web 应用每秒只收到了 10,000 条消息。 问题出在哪里？ 如何查明原因？

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>微服务体系结构中的分布式跟踪模式

若要跨不同的服务重建 IoT 消息流，每个服务应该传播一个用于唯一标识消息的关联 ID。 在集中式系统中收集后，关联 ID 可让你查看消息流。 此方法称为[分布式跟踪模式](https://docs.microsoft.com/azure/architecture/microservices/logging-monitoring#distributed-tracing)。

为了使分布式跟踪得到更广泛的采用，Microsoft 正在努力制定[分布式跟踪的 W3C 标准提案](https://w3c.github.io/trace-context/)。

### <a name="iot-hub-support"></a>IoT 中心支持

启用后，IoT 中心的分布式跟踪支持将遵循以下流程：

1. 在 IoT 设备上生成消息。
1. IoT 设备确定（借助于云）应为此消息分配跟踪上下文。
1. SDK 将 `tracestate` 添加到消息应用程序属性，其中包含消息创建时间戳。
1. IoT 设备将消息发送到 IoT 中心。
1. 消息抵达 IoT 中心网关。
1. IoT 中心在消息应用程序属性中查找 `tracestate`，并检查其格式是否正确。
1. 如果正确，则 IoT 中心生成 `trace-id` 和 `span-id`，并将其记录到 Azure Monitor 诊断日志的 `DiagnosticIoTHubD2C` 类别下。
1. 消息处理完成后，IoT 中心生成另一个 `span-id`，并将其连同现有的 `trace-id` 记录到 `DiagnosticIoTHubIngress` 类别下。
1. 如果为消息启用了路由，则 IoT 中心会将消息写入自定义终结点，并在 `DiagnosticIoTHubEgress` 类别下记录名为 `trace-id` 的另一个 `span-id`。
1. 针对生成的每条消息重复上述步骤。

## <a name="public-preview-limits-and-considerations"></a>公共预览版限制和注意事项

- W3C 跟踪上下文标准提案目前正在草拟中。
- 目前，客户端 SDK 支持的唯一开发语言为 C。
- 云到设备孪生功能不适用于 [IoT 中心基本层](iot-hub-scaling.md#basic-and-standard-tiers)。 但是，如果 IoT 中心看到了适当撰写的跟踪上下文标头，则它仍会将日志记录到 Azure Monitor。
- 为确保有效的操作，IoT 中心会针对分布式跟踪期间的日志记录速率施加限制。

## <a name="next-steps"></a>后续步骤

- 若要详细了解微服务中的一般分布式跟踪模式，请参阅[微服务体系结构模式：分布式跟踪](https://microservices.io/patterns/observability/distributed-tracing.html)。
- 若要设置配置以将分布式跟踪设置应用到大量设备，请参阅[大规模配置和监视 IoT 设备](iot-hub-auto-device-config.md)。
- 若要详细了解 Azure Monitor，请参阅[什么是 Azure Monitor？](../azure-monitor/overview.md)。
