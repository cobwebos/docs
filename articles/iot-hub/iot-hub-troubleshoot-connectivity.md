---
title: 诊断和排查 Azure IoT 中心的连接断开问题
description: 了解如何诊断和排查 Azure IoT 中心设备连接的常见错误
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: 3904c6390cfe8de197bae470c4ae32d22605ae6a
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/08/2019
ms.locfileid: "70801426"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>检测和排查 Azure IoT 中心的连接断开问题

由于存在许多可能的故障点，IoT 设备的连接问题有时很难排查。 设备端应用程序逻辑、物理网络、协议、硬件和 Azure IoT 中心都可能导致问题。 本文提供有关如何从云端（相对于设备端）检测和排查设备连接问题的建议。

## <a name="get-alerts-and-error-logs"></a>获取警报和错误日志

使用 Azure Monitor 可在设备连接断开时获取警报并写入日志。

### <a name="turn-on-diagnostic-logs"></a>启用诊断日志

若要记录设备连接事件和错误，请为 IoT 中心启用诊断。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 浏览到 IoT 中心。

3. 选择“诊断设置”。

4. 选择“启用诊断”。

5. 启用要收集的“连接”日志。

6. 为便于分析，应启用“发送到 Log Analytics”（[参阅定价](https://azure.microsoft.com/pricing/details/log-analytics/)）。 请参阅[解决连接错误](#resolve-connectivity-errors)下的示例。

   ![建议的设置](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

有关详细信息，请参阅[监视 Azure IoT 中心的运行状况并快速诊断问题](iot-hub-monitor-resource-health.md)。

### <a name="set-up-alerts-for-the-_connected-devices_-count-metric"></a>针对“联网设备”计数指标设置警报

若要在设备断开连接时获取警报，请针对“联网设备(预览版)”指标配置警报。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 浏览到 IoT 中心。

3. 选择“**警报**”。

4. 选择“新建警报规则”。

5. 选择“添加条件”，然后选择“联网设备(预览版)”。

6. 按提示操作，完成对所需阈值和警报选项的设置。

有关详细信息，请参阅 [Microsoft Azure 中的经典警报是什么？](../azure-monitor/platform/alerts-overview.md)。

## <a name="resolve-connectivity-errors"></a>解决连接错误

为联网设备启用诊断日志和警报后，如果出错，则会收到警报。 本部分介绍如何在收到警报时解决常见问题。 以下步骤假设已经在 Azure Monitor 日志中设置了诊断日志。

1. 在 Azure 门户中转到 Log Analytics 的工作区。

2. 选择 **“日志搜索”** 。

3. 若要隔离 IoT 中心的连接错误日志，请输入以下查询，然后选择“运行”：

    ```kusto
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. 如果返回了结果，请查看 `OperationName`、`ResultType`（错误代码）和 `ResultDescription`（错误消息），以获取有关错误的更多详细信息。

   ![错误日志示例](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

2. 使用下表来了解和解决常见错误。

    | Error | 根本原因 | 分辨率 |
    |-------|------------|------------|
    | 404104 DeviceConnectionClosedRemotely | 设备关闭了连接，但 IoT 中心不知道原因。 常见原因包括 MQTT/AMQP 超时和 Internet 连接断开。 | 通过[测试连接](tutorial-connectivity.md)，确保设备能够连接到 IoT 中心。 如果连接正常，但设备间歇性断开连接，请确保为所选的协议 (MQTT/AMPQ) 实现正确的保持活动状态设备逻辑。 |
    | 401003 IoTHubUnauthorized | IoT 中心无法对连接进行身份验证。 | 确保所用的 SAS 或其他安全令牌未过期。 [Azure IoT SDK](iot-hub-devguide-sdks.md) 无需特殊的配置即可自动生成令牌。 |
    | 409002 LinkCreationConflict | 设备有多个连接。 针对设备发出新的连接请求时，IoT 中心会关闭上一个连接并返回此错误。 | 在最常见的情况下，设备检测到断开连接并尝试重新建立连接，但 IoT 中心仍认为设备已连接。 IoT 中心关闭先前的连接并记录此错误。 此错误通常表现为一个不同的暂时性问题的副作用，因此请查看日志中的其他错误以进一步排除故障。 否则，请确保仅在连接断开时才发出新的连接请求。 |
    | 500001 ServerError | IoT 中心遇到服务器端问题。 该问题很可能是暂时性的。 尽管 IoT 中心团队会努力维持 [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/)，但一小部分 IoT 中心节点偶尔会遇到暂时性故障。 如果设备尝试连接到有问题的节点，则你会收到此错误。 | 若要缓解暂时性故障，请从设备发出重试命令。 若要[自动管理重试](iot-hub-reliability-features-in-sdks.md#connection-and-retry)，请确保使用最新版本的 [Azure IoT SDK](iot-hub-devguide-sdks.md)。<br><br>有关暂时性故障处理和重试的最佳做法，请参阅[暂时性故障处理](/azure/architecture/best-practices/transient-faults)。  <br><br>如果重试后问题仍然出现，请检查[资源运行状况](iot-hub-monitor-resource-health.md#use-azure-resource-health)和 [Azure 状态](https://azure.microsoft.com/status/history/)，以确定 IoT 中心是否存在已知的问题。 如果不存在已知的问题并且问题仍然出现，请[联系支持人员](https://azure.microsoft.com/support/options/)以做进一步调查。 |
    | 500008 GenericTimeout | IoT 中心在超时之前无法完成连接请求。与 500001 ServerError 一样，此错误可能是暂时性的。 | 遵循 500001 ServerError 的故障排除步骤找到根本原因并解决此错误。|

## <a name="other-steps-to-try"></a>可尝试的其他步骤

如果前面的步骤没有帮助，可尝试以下操作：

* 如果你有权以物理方式或远程访问（例如通过 SSH）有问题的设备，请遵循[设备端故障排除指南](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices)继续进行故障排除。

* 在 Azure 门户 > IoT 中心 > IoT 设备中验证设备是否已启用。

* 从 [Azure IoT 中心论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub)、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) 或 [Azure 支持](https://azure.microsoft.com/support/options/)获得帮助。

如果本指南未能提供所需的帮助，请在下面的反馈部分中留言，以帮助我们改进文档。

## <a name="next-steps"></a>后续步骤

* 要了解有关解决暂时性问题的详细信息，请参阅[暂时性故障处理](/azure/architecture/best-practices/transient-faults)。

* 要了解有关 Azure IoT SDK 和管理重试的详细信息，请参阅[如何使用 Azure IoT Hub 设备 SDK 管理连接和可靠消息传递](iot-hub-reliability-features-in-sdks.md#connection-and-retry)。
