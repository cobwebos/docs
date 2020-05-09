---
title: 了解 Azure IoT 中心直接方法 | Microsoft Docs
description: 开发人员指南 - 使用直接方法从服务应用调用设备上的代码。
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: rezas
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 9fb2242f6e3f8ce78a0e5043a53ce3055819725b
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583681"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>了解和调用 IoT 中心的直接方法

借助 IoT 中心，用户可以从云中对设备调用直接方法。 直接方法表示与设备进行的请求-答复式交互，类似于会立即成功或失败（在用户指定的超时时间后）的 HTTP 调用。 此方法用于即时操作过程不同的情况，即时操作的不同取决于设备能否响应。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

每个设备方法针对一个设备。 [在多个设备上计划作业](iot-hub-devguide-jobs.md)展示了一种方法，用于对多个设备调用直接方法，并为已断开连接的设备计划方法调用。

只要拥有 IoT 中心的“服务连接”  权限，任何人都可以调用设备上的方法。

直接方法遵循请求-响应模式，适用于需要立即确认其结果的通信。 例如对设备的交互式控制，如打开风扇。

如果在使用所需属性、直接方法或云到设备消息方面有任何疑问，请参阅[云到设备通信指南](iot-hub-devguide-c2d-guidance.md)。

## <a name="method-lifecycle"></a>方法生命周期

直接方法在设备上实现，可能需要在方法有效负载中进行 0 次或 0 次以上的输入才能正确地实例化。 可以通过面向服务的 URI (`{iot hub}/twins/{device id}/methods/`) 调用直接方法。 设备通过特定于设备的 MQTT 主题 (`$iothub/methods/POST/{method name}/`) 或通过 AMQP 链接（`IoThub-methodname` 和 `IoThub-status` 应用程序属性）接收直接方法。 

> [!NOTE]
> 调用设备上的直接方法时，属性名称和值只能包含 US-ASCII 可打印字母数字，但下列组中的任一项除外：``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

直接方法是同步的，在超时期限（默认：30 秒，可设置为 5 到 300 秒）。 直接方法适用于交互式场景，即当且仅当设备处于联机状态且可接收命令时，用户希望设备做出响应。 例如，打开手机的灯。 在此类方案中，用户需要立即看到结果是成功还是失败，以便云服务可以尽快根据结果进行操作。 设备可能返回某些消息正文作为方法的结果，但系统不会要求方法一定这样做。 无法保证基于方法调用的排序或者任何并发语义。

直接方法从云端只能通过 HTTPS 调用，从设备端可以通过 MQTT 或 AMQP 调用。

方法请求和响应的有效负载为最大 128 KB 的 JSON 文档。

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>从后端应用调用直接方法

现在，从后端应用调用某个直接方法。

### <a name="method-invocation"></a>方法调用

设备上的直接方法调用是 HTTPS 调用，它由以下项构成：

* 特定于设备的请求 URI 以及 [API 版本](/rest/api/iothub/service/devicemethod/invokedevicemethod)  ：

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* POST 方法 

* 标头**，包含身份验证、请求 ID、内容类型和内容编码。

* 透明的 JSON *正文* ，采用以下格式：

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

在请求中作为 `responseTimeoutInSeconds` 提供的值是 IoT 中心服务在设备上执行直接方法所需等待的时间。 将此超时设置为至少与设备的直接方法的预期执行时间一样长。 如果未提供超时，则使用默认值：30 秒。 `responseTimeoutInSeconds` 的最小值和最大值分别为 5 秒和 300 秒。

在请求中作为 `connectTimeoutInSeconds` 提供的值是在调用直接方法后，IoT 中心服务等待断开连接的服务进入联机状态所需的时间。 默认值为 0，表示在调用直接方法时，设备必须已处于联机状态。 `connectTimeoutInSeconds` 的最大值为 300 秒。


#### <a name="example"></a>示例

此示例将允许你安全地启动请求，以便在注册到 Azure IoT 中心的 IoT 设备上调用直接方法。

若要开始，请使用[Azure CLI 的 Microsoft Azure IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)创建 SharedAccessSignature。 

```bash
az iot hub generate-sas-token -n <iothubName> -du <duration>
```

接下来，将 Authorization 标头替换为新生成的 SharedAccessSignature，然后`iothubName`在`deviceId`下面`methodName`的`payload`示例`curl`命令中修改、和参数以匹配你的实现。  

```bash
curl -X POST \
  https://<iothubName>.azure-devices.net/twins/<deviceId>/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

执行修改后的命令以调用指定的直接方法。 成功的请求将返回 HTTP 200 状态代码。

> [!NOTE]
> 上面的示例演示如何调用设备上的直接方法。  如果要在 IoT Edge 模块中调用直接方法，则需要修改 url 请求，如下所示：

```bash
https://<iothubName>.azure-devices.net/twins/<deviceId>/modules/<moduleName>/methods?api-version=2018-06
```
### <a name="response"></a>响应

后端应用接收响应，响应由以下项构成：

* *HTTP 状态代码*：
  * 200指示成功执行直接方法;
  * 404指示设备 ID 无效，或者在调用直接方法时设备未联机，并且之后为`connectTimeoutInSeconds` （使用附带的错误消息来了解根本原因）;
  * 504指示设备未响应内`responseTimeoutInSeconds`的直接方法调用而导致的网关超时。

* 标头**，包含 ETag、请求 ID、内容类型和内容编码。

* 采用以下格式的 JSON 正文**：

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    `status` 和 `body` 均由设备提供，用于响应，其中包含设备自身的状态代码和/或描述。

### <a name="method-invocation-for-iot-edge-modules"></a>IoT Edge 模块的方法调用

[IoT 服务客户端 C# SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) 中支持使用模块 ID 调用直接方法。

为此，请使用 `ServiceClient.InvokeDeviceMethodAsync()` 方法并传入 `deviceId` 和 `moduleId` 作为参数。

## <a name="handle-a-direct-method-on-a-device"></a>在设备上处理直接方法

让我们看看如何在 IoT 设备上处理直接方法。

### <a name="mqtt"></a>MQTT

以下部分适用于 MQTT 协议。

#### <a name="method-invocation"></a>方法调用

设备通过 MQTT 主题接收直接方法请求：`$iothub/methods/POST/{method name}/?$rid={request id}`。 每个设备的订阅数被限制为 5。 因此，建议不要分别订阅每个直接方法。 而是考虑订阅 `$iothub/methods/POST/#`，然后基于所需的方法名称筛选所传递的消息。

设备接收的正文采用以下格式：

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

方法请求为 QoS 0。

#### <a name="response"></a>响应

设备将响应发送到 `$iothub/methods/res/{status}/?$rid={request id}`，其中：

* `status` 属性是设备提供的方法执行状态。

* `$rid` 属性是从 IoT 中心接收的方法调用中的请求 ID。

正文由设备设置，可以是任何状态。

### <a name="amqp"></a>AMQP

以下部分适用于 AMQP 协议。

#### <a name="method-invocation"></a>方法调用

设备通过在地址 `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` 上创建一个接收链接以接收直接方法请求。

AMQP 消息会到达表示方法请求的接收链接。 它包含以下部分：

* 相关 ID 属性，其中包含一个应与相应的方法响应被传回的请求 ID。

* 名为 `IoThub-methodname` 的一个应用程序属性，其中包含调用的方法名称。

* AMQP 消息正文，其中包含作为 JSON 的方法有效负载。

#### <a name="response"></a>响应

设备会创建一个发送链接以在 `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound` 地址上返回方法响应。

方法的响应在发送链接上返回，并已按以下内容结构化：

* 相关 ID 属性，其中包含在方法的请求消息中传递的请求 ID。

* 名为 `IoThub-status` 的一个应用程序属性，其中包含用户提供的方法状态。

* AMQP 消息正文，其中包含作为 JSON 的方法响应。

## <a name="additional-reference-material"></a>其他参考资料

IoT 中心开发人员指南中的其他参考主题包括：

* [Iot 中心终结点](iot-hub-devguide-endpoints.md)介绍了每个 iot 中心针对运行时和管理操作公开的各种终结点。

* [限制和配额](iot-hub-devguide-quotas-throttling.md)介绍了适用的配额，以及使用 IoT 中心时预期会碰到的限制行为。

* [Azure IoT 设备和服务 SDK](iot-hub-devguide-sdks.md) 列出了开发与 IoT 中心交互的设备和服务应用时可使用的各种语言 SDK。

* [设备孪生、作业和消息路由的 IoT 中心查询语言](iot-hub-devguide-query-language.md)一文介绍了可用于从 IoT 中心检索设备孪生和作业相关信息的 IoT 中心查询语言。

* [IoT 中心 MQTT 支持](iot-hub-mqtt-support.md)提供有关 IoT 中心对 MQTT 协议的支持的详细信息。

## <a name="next-steps"></a>后续步骤

了解如何使用直接方法后，可根据兴趣参阅以下 IoT 中心开发人员指南文章：

* [在多个设备上计划作业](iot-hub-devguide-jobs.md)

若要尝试本文中介绍的一些概念，可以根据兴趣学习以下 IoT 中心教程：

* [使用直接方法](quickstart-control-device-node.md)
* [使用用于 VS Code 的 Azure IoT Tools 进行设备管理](iot-hub-device-management-iot-toolkit.md)
