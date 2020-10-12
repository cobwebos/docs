---
title: 发送用于 IoT 的 Defender 设备安全消息
description: 了解如何使用用于 IoT 的 Defender 发送安全消息。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.custom: devx-track-js
ms.openlocfilehash: 5217940a3696bd001db421e61be8313453807c8b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318511"
---
# <a name="send-security-messages-sdk"></a>发送安全消息 SDK

当你选择在不使用 IoT 代理的情况下收集和发送设备安全消息，并说明如何执行此操作时，本操作方法指南介绍了用于 IoT 服务功能的 Defender。

本指南介绍如何：

> [!div class="checklist"]
> * 使用 Azure IoT C SDK 发送安全消息
> * 使用 Azure IoT c # SDK 发送安全消息
> * 使用 Azure IoT Python SDK 发送安全消息
> * 使用 Azure IoT Node.js SDK 发送安全消息
> * 使用 Azure IoT Java SDK 发送安全消息

## <a name="defender-for-iot-capabilities"></a>用于 IoT 功能的 Defender

只要发送的数据符合 [IoT 架构的 defender](https://aka.ms/iot-security-schemas) 并将消息设置为安全消息，就可以处理和分析任何类型的安全消息数据。

## <a name="security-message"></a>安全消息

Defender for IoT 使用以下条件定义安全消息：

- 如果消息是通过 Azure IoT SDK 发送的
- 如果消息符合 [安全消息架构](https://aka.ms/iot-security-schemas)
- 如果在发送之前将消息设置为安全消息，则为

每个安全消息都包含发件人的元数据 `AgentId` ，如、 `AgentVersion` `MessageSchemaVersion` 和安全事件的列表。
架构定义安全消息的有效属性和必需属性，包括事件类型。

> [!NOTE]
> 发送的消息如果不符合该架构，则将被忽略。 在开始发送数据之前，请务必验证架构，因为当前不会存储已忽略的消息。

> [!NOTE]
> 使用 Azure IoT SDK 未设置为安全消息的发送的消息将不会路由到 IoT 管道的 Defender。

## <a name="valid-message-example"></a>有效的消息示例

下面的示例显示了有效的安全消息对象。 该示例包含消息元数据和一个 `ProcessCreate` 安全事件。

一旦设置为安全消息并发送，此消息将由 Defender for IoT 处理。

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>发送安全消息

通过使用[Azure Iot C 设备 sdk](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview)、 [Azure iot c # 设备 Sdk](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview)、 [azure IoT Node.js SDK](https://github.com/Azure/azure-iot-sdk-node)、 [Azure IOT Python sdk](https://github.com/Azure/azure-iot-sdk-python)或[Azure iot Java Sdk](https://github.com/Azure/azure-iot-sdk-java)，*无需*使用用于 IoT 代理的 Defender 的安全消息。

若要从设备发送用于 IoT 的设备数据进行处理，请使用以下 Api 之一将消息标记为正确路由到用于 IoT 处理管道的 Defender。

即使标记为正确的标头，发送的所有数据也必须符合 [IoT 消息架构的 Defender](https://aka.ms/iot-security-schemas)。

### <a name="send-security-message-api"></a>发送安全消息 API

**发送安全消息**API 目前在 c 和 c #、Python、Node.js 和 Java 中可用。

#### <a name="c-api"></a>C API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {

    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;

    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);

    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }

    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }

    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }

cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }

    return success;
}

static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }

    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

#### <a name="c-api"></a>C# API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="nodejs-api"></a>Node.js API

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>Python API

若要使用 Python API，需要安装 [azure iot-设备](https://pypi.org/project/azure-iot-device/)包。

使用 Python API 时，可以通过模块或使用唯一设备或模块连接字符串通过设备发送安全消息。 使用以下 Python 脚本示例时，使用的是设备，使用 **IoTHubDeviceClient**，使用模块时，请使用 **IoTHubModuleClient**。

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>Java API

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```

## <a name="next-steps"></a>后续步骤

- 阅读用于 IoT 服务的 Defender [概述](overview.md)
- 了解有关用于 IoT[体系结构](architecture.md)的 Defender 的详细信息
- 启用该[服务](quickstart-onboard-iot-hub.md)
- 阅读[常见问题解答](resources-frequently-asked-questions.md)
- 了解如何访问[原始安全数据](how-to-security-data-access.md)
- 了解[建议](concept-recommendations.md)
- 了解[警报](concept-security-alerts.md)
