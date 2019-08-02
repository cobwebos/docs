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
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: c780eea15b9f064d3279c75ac2f967e8b6099ecb
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596207"
---
# <a name="send-security-messages-sdk"></a>发送安全消息 SDK

本操作方法指南介绍了在不使用用于 IoT 代理的 Azure 安全中心的情况下, 选择收集和发送设备安全消息时的 IoT 服务功能的 Azure 安全中心, 并说明如何执行此操作。  

本指南介绍如何： 
> [!div class="checklist"]
> * 使用 API for C# 发送安全消息
> * 使用 API for C 发送安全消息

## <a name="azure-security-center-for-iot-capabilities"></a>用于 IoT 功能的 Azure 安全中心

Azure 安全中心可以处理和分析任何类型的安全消息数据, 只要发送的数据符合[IoT 架构的 Azure 安全中心](https://aka.ms/iot-security-schemas), 并将该消息设置为安全消息即可。

## <a name="security-message"></a>安全消息

用于 IoT 的 Azure 安全中心使用以下条件定义安全消息:
- 如果消息是通过 Azure IoT C/C# SDK 发送的
- 如果消息符合[安全消息架构](https://aka.ms/iot-security-schemas)
- 如果在发送之前将消息设置为安全消息, 则为

每个安全消息都包含发件人的元`AgentId`数据`AgentVersion` `MessageSchemaVersion` , 如、和安全事件的列表。
架构定义安全消息的有效属性和必需属性, 包括事件类型。

>[!Note]
> 发送的消息如果不符合该架构，则将被忽略。 在开始发送数据之前，请务必验证架构，因为当前不会存储已忽略的消息。 

>[!Note]
> 使用 Azure IoT C/C# SDK 未设置为安全消息的已发送邮件将不会路由到 IoT 管道的 Azure 安全中心

## <a name="valid-message-example"></a>有效的消息示例

下面的示例显示了有效的安全消息对象。 该示例包含消息元数据和一个`ProcessCreate`安全事件。

一旦设置为安全消息并发送, 此消息将由 Azure 安全中心进行 IoT 处理。

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

使用[Azure C# iot 设备 Sdk](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview)或[azure iot C 设备 Sdk](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview), 无需使用 azure 安全中心的 iot 代理发送安全消息。

若要从设备发送用于 IoT 的 Azure 安全中心处理设备数据, 请使用以下 Api 之一将消息标记为正确路由到 Azure 安全中心以进行 IoT 处理管道。 

即使标记为正确的标头, 发送的所有数据也必须符合[用于 IoT 消息架构的 Azure 安全中心](https://aka.ms/iot-security-schemas)。 

### <a name="send-security-message-api"></a>发送安全消息 API

“发送安全消息”API 目前可在 C 和 C# 中使用。  

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

## <a name="next-steps"></a>后续步骤
- 阅读 Azure 安全中心以获取 IoT 服务[概述](overview.md)
- 详细了解用于 IoT[体系结构](architecture.md)的 Azure 安全中心
- 启用该[服务](quickstart-onboard-iot-hub.md)
- 阅读[常见问题解答](resources-frequently-asked-questions.md)
- 了解如何访问[原始安全数据](how-to-security-data-access.md)
- 了解[建议](concept-recommendations.md)
- 了解[警报](concept-security-alerts.md)
