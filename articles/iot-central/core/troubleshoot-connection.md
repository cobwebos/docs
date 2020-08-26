---
title: 排查 Azure IoT Central 的设备连接问题 |Microsoft Docs
description: 排查在 IoT Central 中无法看到设备数据的原因
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.openlocfilehash: 4c95c5eccb5ff804adeae94074136c6242678127
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816059"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>排查设备中的数据不会显示在 Azure 中的原因 IoT Central

本文档帮助设备开发人员查明其设备发送到 IoT Central 的数据可能不会显示在应用程序中的原因。

要调查以下两个主要方面：

- 设备连接问题
  - 身份验证问题（例如设备）具有无效的凭据
  - 网络连接问题
  - 未批准或阻止设备
- 设备负载形状问题

此故障排除指南重点介绍设备连接问题和设备负载形状问题。

## <a name="device-connectivity-issues"></a>设备连接问题

本部分可帮助确定你的数据是否正在 IoT Central。

如果尚未这样做，请安装 `az cli` 工具和 `azure-iot` 扩展。

若要了解如何安装 `az cli` ，请参阅 [安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

若要 [安装](https://docs.microsoft.com/cli/azure/azure-cli-reference-for-IoT?view=azure-cli-latest#extension-reference-installation) 该 `azure-iot` 扩展，请运行以下命令：

```cmd/bash
az extension add --name azure-iot
```

> [!NOTE]
> 首次运行扩展命令时，系统可能会提示您安装 `uamqp` 库。

安装该 `azure-iot` 扩展后，请启动设备，查看其正在发送的消息是否正在进行 IoT Central。

使用以下命令登录到你的 IoT Central 应用程序的订阅：

```cmd/bash
az login
az set account --subscription <your-subscription-id>
```

若要监视设备正在发送的遥测数据，请使用以下命令：

```cmd/bash
az iot central app monitor-events --app-id <app-id> --device-id <device-name>
```

如果设备已成功连接到 IoT Central，则会看到类似于以下内容的输出：

```cmd/bash
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

若要监视设备与 IoT Central 交换的属性更新，请使用以下预览命令：

```cmd/bash
az iot central app monitor-properties --app-id <app-id> --device-id <device-name>
```

如果设备成功发送属性更新，将看到类似于以下内容的输出：

```cmd/bash
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

### <a name="interpreting-terminal-output"></a>解释终端输出

如果你看到数据显示在终端中，则数据将与 IoT Central 的应用程序一样。

如果在几分钟后看不到任何数据，请尝试在 `Enter` `return` 键盘上按或键，以防输出停滞。

如果终端上仍未显示任何数据，则可能是设备出现网络连接问题，或者没有正确地将数据发送到 IoT Central。

### <a name="check-the-provisioning-status-of-your-device"></a>检查设备的预配状态

如果监视器上未显示数据，请运行以下命令检查设备的设置状态：

```cmd/bash
az iot central app device registration-info --app-id <app-id> --device-id <device-name>
```

以下输出显示了阻止连接的设备的示例：

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| 设备预配状态 | 说明 | 可能的缓解 |
| - | - | - |
| 已预配 | 无立即识别的问题。 | 空值 |
| 已注册 | 设备尚未连接到 IoT Central。 | 检查设备日志是否存在连接问题。 |
| 已阻止 | 设备被阻止连接到 IoT Central。 | 设备被阻止连接到 IoT Central 应用程序。 在 IoT Central 中取消阻止设备，然后重试。 若要了解详细信息，请参阅 [阻止设备](concepts-get-connected.md#device-status-values)。 |
| 审批 | 设备未批准。 | 未批准设备连接到 IoT Central 应用程序。 批准 IoT Central 中的设备，然后重试。 若要了解详细信息，请参阅 [批准设备](concepts-get-connected.md#connect-without-registering-devices) |
| 无关 | 设备未与设备模板关联。 | 将设备与设备模板关联，以便 IoT Central 知道如何分析数据。 |

了解有关 [设备状态代码](concepts-get-connected.md#device-status-values)的详细信息。

### <a name="error-codes"></a>错误代码

如果仍无法诊断数据不显示的原因 `monitor-events` ，下一步是查找设备报告的错误代码。

在设备上启动调试会话，或从设备收集日志。 检查设备报告的任何错误代码。

下表显示了常见错误代码和要缓解的可能操作。

如果你看到与身份验证流相关的问题：

| 错误代码 | 说明 | 可能的缓解操作 |
| - | - | - |
| 400 | 请求正文无效。 例如，无法对其进行分析，或者无法验证对象。 | 请确保在证明流中发送正确的请求正文，或者使用设备 SDK。 |
| 401 | 无法验证授权令牌。 例如，它已过期或不适用于请求的 URI。 此错误代码也会在 TPM 证明流中返回给设备。 | 确保你的设备具有正确的凭据。 |
| 404 | 设备预配服务实例或某个资源（如注册）不存在。 | [向客户支持提交票证](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 |
| 412 | `ETag`根据 RFC7232，请求中的不匹配 `ETag` 现有资源的。 | [向客户支持提交票证](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 |
| 429 | 服务限制了操作。 对于具体的服务限制，请参阅 [IoT 中心设备预配服务限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits)。 | 减少消息频率，在更多设备之间拆分责任。 |
| 500 | 发生内部错误。 | 向[客户支持提交票证](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)，查看他们能否进一步提供帮助。 |

## <a name="payload-shape-issues"></a>负载形状问题

建立设备将数据发送到 IoT Central 后，下一步是确保你的设备以有效格式发送数据。

存在两个导致设备数据不会出现在 IoT Central 中的常见问题类别：

- 设备模板到设备数据不匹配：
  - 命名中不匹配，如输入错误或大小写匹配问题。
  - 未建模属性，其中未在设备模板中定义架构。
  - 架构不匹配，如模板中定义为的类型 `boolean` ，但数据为字符串。
  - 同一遥测名称是在多个接口中定义的，但设备不即插即用符合 IoT。
- 数据形状是无效的 JSON。 若要了解详细信息，请参阅 [遥测、属性和命令有效负载](concepts-telemetry-properties-commands.md)。

若要检测问题所在的类别，请为你的方案运行最适合的命令：

- 若要验证遥测数据，请使用预览命令：

    ```cmd/bash
    az iot central app validate-messages --app-id <app-id> --device-id <device-name>
    ```

- 若要验证属性更新，请使用预览命令

    ```cmd/bash
    az iot central app validate-properties --app-id <app-id> --device-id <device-name>
    ```

- 如果希望使用 GUI，请使用 IoT Central **原始数据** 视图，查看是否没有建模。 **原始数据**视图不会检测设备是否发送格式错误的 JSON。

`uamqp`首次运行命令时，系统可能会提示您安装库 `validate` 。

以下输出显示了来自 validate 命令的示例错误和警告消息：

```cmd/bash
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="原始数据视图的屏幕截图":::

### <a name="interpreting-terminal-output"></a>解释终端输出

检测到问题后，你可能需要更新设备固件，或创建一个新的设备模板来对以前未建模的数据建模。

如果选择创建可正确对数据建模的新模板，请将设备从旧模板迁移到新模板。 若要了解详细信息，请参阅 [在 Azure IoT Central 应用程序中管理设备](howto-manage-devices.md)。

## <a name="next-steps"></a>后续步骤

如果无法使用本指南进行故障排除并解决问题，请打开支持票证。 Azure 客户可在 Azure 门户中创建和管理支持请求：

- [Azure 门户](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
- [适用于美国政府的 Azure 门户](https://portal.azure.us/)

有关详细信息，请参阅 [Azure IoT 支持和帮助选项](../../iot-fundamentals/iot-support-help.md)。
