---
title: "将 IoT 中心消息保存到 Azure 数据存储 | Microsoft Docs"
description: "使用 Azure Function App 将 IoT 中心消息保存到 Azure 表存储。 IoT 中心消息包含 IoT 设备发送的传感器数据等信息。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "iot 数据存储, iot 传感器数据存储"
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 4918648906212ea9708b6c6f0e89d1f4bb7bdcc5
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017


---
# <a name="save-iot-hub-messages-that-contain-information-like-sensor-data-to-azure-table-storage"></a>将包含传感器数据等信息的 IoT 中心消息保存到 Azure 表存储

![端到端关系图](media/iot-hub-get-started-e2e-diagram/3.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-will-learn"></a>你要学习的知识

了解如何创建 Azure 存储帐户，以及一个用于在 Azure 表存储中存储 IoT 中心消息的 Azure Function App。

## <a name="what-you-will-do"></a>执行的操作

- 创建 Azure 存储帐户。
- 准备 IoT 中心连接以读取消息。
- 创建并部署 Azure Function App。

## <a name="what-you-will-need"></a>所需的项目

- 已完成教程[设置设备](iot-hub-raspberry-pi-kit-node-get-started.md)，其中涵盖以下要求：
  - 一个有效的 Azure 订阅。
  - 已订阅中创建一个 Azure IoT 中心。
  - 一个可向 Azure IoT 中心发送消息的正常运行的应用程序。

## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

1. 在 Azure 门户中，单击“新建” > “存储” > “存储帐户”。
1. 输入存储帐户所需的信息：

   ![在 Azure 门户中创建存储帐户](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   **名称**：存储帐户的名称。 该名称必须全局唯一。

   **资源组**：使用 IoT 中心所用的同一资源组。

   **固定仪表板**：选中此选项可以方便地从仪表板访问 IoT 中心。
1. 单击“创建”。

## <a name="prepare-for-iot-hub-connection-to-read-messages"></a>准备 IoT 中心连接以读取消息

IoT 中心公开一个与事件中心兼容的内置终结点，使应用程序能够读取 IoT 中心消息。 同时，应用程序可以使用使用者组从 IoT 中心读取数据。 在创建用于从 IoT 中心读取数据的 Azure Function App 之前，需要：

- 获取 IoT 中心终结点的连接字符串。
- 为 IoT 中心创建使用者组。

### <a name="get-the-connection-string-of-your-iot-hub-endpoint"></a>获取 IoT 中心终结点的连接字符串

1. 打开 IoT 中心
1. 在“IoT 中心”窗格中，单击“消息”下面的“终结点”。
1. 在右窗格中，单击“内置终结点”下面的“事件”。
1. 在“属性”窗格中，记下以下值：
   - 与事件中心兼容的终结点
   - 与事件中心兼容的名称

   ![在 Azure 门户中获取 IoT 中心终结点的连接字符串](media\iot-hub-store-data-in-azure-table-storage\2_azure-portal-iot-hub-endpoint-connection-string.png)

1. 在“IoT 中心”窗格中，单击“设置”下面的“共享访问策略”。
1. 单击“iothubowner”。
1. 记下“主密钥”值。
1. 按如下所示构建 IoT 中心终结点的连接字符串：

   `Endpoint=<Event Hub-compatible endpoint>;SharedAccessKeyName=iothubowner;SharedAccessKey=<Primary key>`

   > [!Note]
   > 将 `<Event Hub-compatible endpoint>` 和 `<Primary key>` 替换为记下的值。

### <a name="create-a-consumer-group-for-your-iot-hub"></a>为 IoT 中心创建使用者组

1. 打开 IoT 中心
1. 在“IoT 中心”窗格中，单击“消息”下面的“终结点”。
1. 在右窗格中，单击“内置终结点”下面的“事件”。
1. 在“属性”窗格中的“使用者组”下面输入一个名称并记下该名称。
1. 单击“保存” 。

## <a name="create-and-deploy-an-azure-function-app"></a>创建并部署 Azure Function App

1. 在 [Azure 门户](https://portal.azure.com/)中，单击“新建” > “计算” > “Function App”。
1. 输入 Function App 所需的信息。

   ![在 Azure 门户中创建 Function App](media\iot-hub-store-data-in-azure-table-storage\3_azure-portal-create-function-app.png)

   **应用名称**：Function App 的名称。 该名称必须全局唯一。

   **资源组**：使用 IoT 中心所用的同一资源组。

   **存储帐户**：创建的存储帐户。

   **固定仪表板**：选中此选项可以方便地从仪表板访问 Function App。
1. 单击“创建”。
1. 创建 Function App 后将它打开。
1. 在 Function App 中创建一个新函数。
   1. 单击“新建函数”。
   1. 为“语言”选择“JavaScript”，为“方案”选择“数据处理”。
   1. 单击“EventHubTrigger JavaScript”模板。
   1. 输入模板所需的信息。

      **为函数命名**：函数的名称。

      **事件中心名称**：记下的与事件中心兼容的名称。

      **事件中心连接**：单击“新建”添加所构建的 IoT 中心终结点连接字符串。
   1. 单击“创建” 。
1. 配置函数的输出。
   1. 单击“集成” > “新建输出” > “Azure 表存储” > “选择”。

      ![在 Azure 门户中将表存储添加到 Function App](media\iot-hub-store-data-in-azure-table-storage\4_azure-portal-function-app-add-output-table-storage.png)
   1. 输入所需的信息。

      **表参数名称**：使用 `outputTable` 作为名称，将在 Azure Functions 的代码中使用它。
      
      **表名称**：使用 `deviceData` 作为名称。

      **存储帐户连接**：单击“新建”，然后选择或输入存储帐户。
   1. 单击“保存” 。
1. 在“触发器”下，单击“Azure 事件中心(myEventHubTrigger)”。
1. 在“事件中心使用者组”下，输入创建的使用者组的名称，然后单击“保存”。
1. 单击“开发”，然后单击“查看文件”。
1. 将 `index.js` 中的代码替换为以下内容，然后单击“保存”。

   ```javascript
   'use strict';

   // This function is triggered each time a message is revieved in the IoTHub.
   // The message payload is persisted in an Azure Storage Table
 
   module.exports = function (context, iotHubMessage) {
    context.log('Message received: ' + JSON.stringify(iotHubMessage));
    var date = Date.now();
    var partitionKey = Math.floor(date / (24 * 60 * 60 * 1000)) + '';
    var rowKey = date + '';
    context.bindings.outputTable = {
     "partitionKey": partitionKey,
     "rowKey": rowKey,
     "message": JSON.stringify(iotHubMessage)
    };
    context.done();
   };
   ```

现已创建 Function App。 它将在 Azure 表存储中存储 IoT 中心收到的消息。

> [!Note]
> 可以使用“运行”按钮测试该 Function App。 单击“运行”，将向 IoT 中心发送测试消息。 收到该消息后，应会触发 Function App 的启动并将该消息保存到 Azure 表存储。 “日志”窗格记录了过程详细信息。

## <a name="verify-your-message-in-your-table-storage"></a>在表存储中验证消息

1. 在设备上运行示例应用程序，将消息发送到 IoT 中心。
1. [下载并安装 Microsoft Azure 存储资源管理器](http://storageexplorer.com/)。
1. 打开 Microsoft Azure 存储资源管理器，单击“添加 Azure 帐户” > “登录”，然后登录到你的 Azure 帐户。
1. 单击你的 Azure 订阅 >“存储帐户”> 你的存储帐户 >“表” > “deviceData”。

   此时应会显示设备发送到 IoT 中心的、记录在 `deviceData` 表中的消息。

## <a name="next-steps"></a>后续步骤

现已成功创建 Azure 存储帐户，以及可将 IoT 中心收到的消息存储在 Azure 表存储中的 Azure Function App。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

