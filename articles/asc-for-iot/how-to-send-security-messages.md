---
title: 向 Azure 安全中心发送用于 IoT 的安全消息 |Microsoft Docs
description: 了解如何使用 Azure 安全中心的 IoT 发送安全消息。
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
ms.date: 10/03/2019
ms.author: mlottner
ms.openlocfilehash: 4d91eecc6168ae195fecdf788f091fd70b785f05
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937132"
---
# <a name="send-security-messages-sdk"></a>发送安全消息 SDK

本操作方法指南介绍了在不使用用于 IoT 代理的 Azure 安全中心的情况下，选择收集和发送设备安全消息时的 IoT 服务功能的 Azure 安全中心，并说明如何执行此操作。  

本指南介绍如何： 
> [!div class="checklist"]
> * 使用 Azure IoT C SDK 发送安全消息
> * 使用 Azure IoT C# SDK 发送安全消息
> * 使用 Azure IoT Python SDK 发送安全消息
> * 使用 Azure IoT node.js SDK 发送安全消息
> * 使用 Azure IoT Java SDK 发送安全消息


## <a name="azure-security-center-for-iot-capabilities"></a>用于 IoT 功能的 Azure 安全中心

Azure 安全中心可以处理和分析任何类型的安全消息数据，只要发送的数据符合[IoT 架构的 Azure 安全中心](https://aka.ms/iot-security-schemas)，并将该消息设置为安全消息即可。

## <a name="security-message"></a>安全消息

用于 IoT 的 Azure 安全中心使用以下条件定义安全消息：
- 如果消息是通过 Azure IoT SDK 发送的
- 如果消息符合[安全消息架构](https://aka.ms/iot-security-schemas)
- 如果在发送之前将消息设置为安全消息，则为

每个安全消息都包含发件人的元`AgentId`数据`AgentVersion` `MessageSchemaVersion` ，如、和安全事件的列表。
架构定义安全消息的有效属性和必需属性，包括事件类型。

>[!Note]
> 发送的消息如果不符合该架构，则将被忽略。 在开始发送数据之前，请务必验证架构，因为当前不会存储已忽略的消息。 

>[!Note]
> 使用 Azure IoT SDK 未设置为安全消息的发送的消息将不会路由到 IoT 管道的 Azure 安全中心。

## <a name="valid-message-example"></a>有效的消息示例

下面的示例显示了有效的安全消息对象。 该示例包含消息元数据和一个`ProcessCreate`安全事件。

一旦设置为安全消息并发送，此消息将由 Azure 安全中心进行 IoT 处理。

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

使用[azure Iot C 设备 sdk](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview)、 [AZURE iot C#设备 SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview)、 [Azure IOT node.js Sdk](https://github.com/Azure/azure-iot-sdk-node)、[AZURE iot Python sdk](https://github.com/Azure/azure-iot-sdk-python)或 [Azure iot Java sdk](https://github.com/Azure/azure-iot-sdk-java)，无需使用 azure 安全中心的 iot 代理发送安全消息.

若要从设备发送用于 IoT 的 Azure 安全中心处理设备数据，请使用以下 Api 之一将消息标记为正确路由到 Azure 安全中心以进行 IoT 处理管道。 

即使标记为正确的标头，发送的所有数据也必须符合[用于 IoT 消息架构的 Azure 安全中心](https://aka.ms/iot-security-schemas)。 

### <a name="send-security-message-api"></a>发送安全消息 API 

**发送安全消息**API 目前在 C 和C#、Python、node.js 和 Java 中可用。  

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

```python
async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_d2c_message(security_message)
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
- 阅读 Azure 安全中心以获取 IoT 服务[概述](overview.md)
- 详细了解用于 IoT[体系结构](architecture.md)的 Azure 安全中心
- 启用该[服务](quickstart-onboard-iot-hub.md)
- 阅读[常见问题解答](resources-frequently-asked-questions.md)
- 了解如何访问[原始安全数据](how-to-security-data-access.md)
- 了解[建议](concept-recommendations.md)
- 了解[警报](concept-security-alerts.md)
