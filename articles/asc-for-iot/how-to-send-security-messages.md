---
title: 将安全消息发送到 Azure 安全中心，以便进行 IoT® 服务。微软文档
description: 了解如何使用适用于 IoT 的 Azure 安全中心发送安全消息。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 8bbbd8248c7418b667e34389cb47bd3f6b4f06ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963812"
---
# <a name="send-security-messages-sdk"></a>发送安全消息 SDK

本操作指南在选择不使用 IoT 代理 Azure 安全中心收集和发送设备安全消息时，请解释 IoT 服务 Azure 安全中心功能，并说明如何执行此操作。  

本指南介绍如何： 
> [!div class="checklist"]
> * 使用 Azure IoT C SDK 发送安全消息
> * 使用 Azure IoT C++ SDK 发送安全消息
> * 使用 Azure IoT Python SDK 发送安全消息
> * 使用 Azure IoT 节点.js SDK 发送安全消息
> * 使用 Azure IoT Java SDK 发送安全消息


## <a name="azure-security-center-for-iot-capabilities"></a>用于 IoT 功能的 Azure 安全中心

IoT 的 Azure 安全中心可以处理和分析任何类型的安全消息数据，只要发送的数据符合[IoT 架构的 Azure 安全中心](https://aka.ms/iot-security-schemas)，并且消息设置为安全消息。

## <a name="security-message"></a>安全消息

IoT 的 Azure 安全中心使用以下条件定义安全消息：
- 如果消息是使用 Azure IoT SDK 发送的
- 如果消息符合[安全消息架构](https://aka.ms/iot-security-schemas)
- 如果消息在发送前设置为安全消息

每个安全消息都包括发件人的元数据，如`AgentId`，`AgentVersion``MessageSchemaVersion`和安全事件的列表。
架构定义安全消息的有效和必需属性，包括事件类型。

>[!Note]
> 发送的消息如果不符合该架构，则将被忽略。 在开始发送数据之前，请务必验证架构，因为当前不会存储已忽略的消息。 

>[!Note]
> 未使用 Azure IoT SDK 设置为安全消息发送的邮件将不会路由到 IoT 管道的 Azure 安全中心。

## <a name="valid-message-example"></a>有效消息示例

下面的示例显示了一个有效的安全消息对象。 该示例包含消息元数据和一个`ProcessCreate`安全事件。

设置为安全消息并发送后，此消息将由 IoT 的 Azure 安全中心处理。

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

使用[Azure IoT C 设备 SDK、Azure](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) [IoT C++ 设备 SDK、Azure](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) [IoT Node.js SDK、Azure](https://github.com/Azure/azure-iot-sdk-node) [IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python)或[Azure IoT Java SDK，](https://github.com/Azure/azure-iot-sdk-java)*不使用*Azure 安全中心为 IoT 代理发送安全消息。

要从设备发送设备数据，供 Azure 安全中心进行 IoT 处理，请使用以下 API 之一标记消息，以便正确路由到 Azure 安全中心进行 IoT 处理管道。 

发送的所有数据（即使标有正确的标头）也必须符合[IoT 消息架构的 Azure 安全中心。](https://aka.ms/iot-security-schemas) 

### <a name="send-security-message-api"></a>发送安全消息 API 

**发送安全消息**API 当前在 C 和 C#、Python、Node.js 和 Java 中可用。  

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

要使用 Python API，您需要安装包[azure-iot 设备](https://pypi.org/project/azure-iot-device/)。

使用 Python API 时，可以使用唯一的设备或模块连接字符串通过模块或通过设备发送安全消息。 使用以下 Python 脚本示例时，使用设备时，请使用**IoTHubDeviceClient**，并使用模块使用**IoTHubModuleClient**。 

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
- 阅读 Azure 安全中心，了解 IoT 服务[概述](overview.md)
- 了解有关 IoT[体系结构](architecture.md)的 Azure 安全中心
- 启用该[服务](quickstart-onboard-iot-hub.md)
- 阅读[常见问题解答](resources-frequently-asked-questions.md)
- 了解如何访问[原始安全数据](how-to-security-data-access.md)
- 了解[建议](concept-recommendations.md)
- 了解[警报](concept-security-alerts.md)
