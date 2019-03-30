---
title: IoT 预览版将安全消息发送到 ASC |Microsoft Docs
description: 了解如何将使用适用于 IoT ASC 安全消息发送。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: d8eefff5a5ea03237eea60a63b63d70f605f10b3
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650149"
---
# <a name="send-security-messages-sdk"></a>发送安全消息 SDK

> [!IMPORTANT]
> Iot ASC 目前处于公共预览状态。
> 此预览版本没有附带服务级别协议提供，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本操作方法指南介绍 ASC 的 IoT 服务功能，当你选择收集并将你的设备而无需为 IoT 代理使用 ASC 发送安全消息时，并说明如何执行此操作。  

本指南介绍如何： 
> [!div class="checklist"]
> * 使用发送安全消息 API 的C#
> * 使用适用于 C 的发送安全消息 API

## <a name="asc-for-iot-capabilities"></a>针对 IoT 功能 ASC

适用于 IoT 的 ASC 可以处理和分析任何类型的安全消息数据，只要发送的数据符合[ASC 为 IoT 架构](https://aka.ms/iot-security-schemas)消息被设置为一条安全消息。

## <a name="security-message"></a>安全消息

适用于 IoT 的 ASC 定义一条安全消息，使用以下条件：
- 如果使用 Azure IoT C 发送消息 /C# SDK
- 如果消息符合[安全消息架构](https://aka.ms/iot-security-schemas)
- 如果消息已设置为一条安全消息在发送前

每个安全消息包括发件人的元数据，如`AgentId`， `AgentVersion`，`MessageSchemaVersion`和安全事件的列表。
架构定义包括的事件类型的安全消息的有效和所需属性。

[!NOTE]
> 发送消息的不符合该架构将被忽略。 请确保已忽略的消息不是当前存储在启动发送数据之前验证架构。 
> 发送消息的未设置为使用 Azure IoT C 的安全消息 /C# SDK 将不会路由到 ASC 为 IoT 管道

## <a name="valid-message-example"></a>有效的消息示例

下面的示例显示了有效的安全消息对象。 此示例包含消息元数据，另一个`ProcessCreate`安全事件。

一次设置为一条安全消息，发送，此消息将由处理 ASC 对 IoT。

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
                    "Executable": "/usr/bin/echo",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "nginx",
                    "CommandLine": "./backup .htaccess"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>发送安全消息 

发送安全消息，而无需使用 IoT 代理使用 ASC [Azure IoTC#设备 SDK](https://github.com/Azure/azure-iot-sdk-csharp)或[的 Azure IoT C 设备 SDK](https://github.com/Azure/azure-iot-sdk-c)。

若要从供处理 asc 的 IoT 设备发送设备数据，使用以下 Api 之一标记的正确路由到 ASC IoT 处理管道的消息。 将处理这种方式发送的消息，并将其 iot 在这两个 IoT 中心或 Azure 安全中心中显示为 ASC 中的安全见解。 

即使使用正确的标头，标记发送的所有数据也必须都符合[ASC 为 IoT 消息架构](https://aka.ms/iot-security-schemas)。 

### <a name="send-security-message-api"></a>发送安全消息 API

**发送安全消息**API 是目前用于 C 和C#。  

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
- 阅读 IoT 服务 ASC[概述](overview.md)
- 要详细了解 ASC 的 IoT[体系结构](architecture.md)
- 启用[服务](quickstart-onboard-iot-hub.md)
- 读取[常见问题](resources-frequently-asked-questions.md)
- 了解如何访问[原始安全数据](how-to-security-data-access.md)
- 了解[建议](concept-recommendations.md)
- 了解[警报](concept-security-alerts.md)
